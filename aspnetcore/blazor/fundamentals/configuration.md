---
title: Configuração de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre a configuração de Blazor aplicativos, incluindo configurações de aplicativo, autenticação e configuração de log.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 48d78f40e9254bac182ffbc534550157664bcc5b
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106928"
---
# <a name="aspnet-core-blazor-configuration"></a>Configuração de ASP.NET Core Blazor

> [!NOTE]
> Este tópico aplica-se a Blazor WebAssembly . Para obter diretrizes gerais sobre a configuração do aplicativo ASP.NET Core, consulte <xref:fundamentals/configuration/index> .

Blazor WebAssembly carrega a configuração dos seguintes arquivos de configurações de aplicativo por padrão:

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`, em que o `{ENVIRONMENT}` espaço reservado é o [ambiente de tempo de execução](xref:fundamentals/environments)do aplicativo.

Outros provedores de configuração registrados pelo aplicativo também podem fornecer configuração, mas nem todos os provedores ou recursos de provedor são apropriados para Blazor WebAssembly aplicativos:

* [Azure Key Vault provedor de configuração](xref:security/key-vault-configuration): o provedor não tem suporte para identidade gerenciada e ID do aplicativo (ID do cliente) com cenários de segredo do cliente. A ID do aplicativo com um segredo do cliente não é recomendada para nenhum aplicativo ASP.NET Core, especialmente Blazor WebAssembly aplicativos porque o segredo do cliente não pode ser protegido do lado do cliente para acessar o serviço Azure Key Vault.
* [Provedor de configuração de Azure app](/azure/azure-app-configuration/quickstart-aspnet-core-app): o provedor não é apropriado para Blazor WebAssembly aplicativos porque os Blazor WebAssembly aplicativos não são executados em um servidor no Azure.

> [!WARNING]
> A configuração em um Blazor WebAssembly aplicativo é visível para os usuários. **Não armazene segredos do aplicativo, credenciais ou quaisquer outros dados confidenciais na configuração de um Blazor WebAssembly aplicativo.**

Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Configuração de configurações do aplicativo

A configuração em arquivos de configurações do aplicativo é carregada por padrão. No exemplo a seguir, um valor de configuração de interface do usuário é armazenado em um arquivo de configurações do aplicativo e carregado pela Blazor estrutura automaticamente. O valor é lido por um componente.

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração.

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

Para ler outros arquivos de configuração da `wwwroot` pasta para a configuração, use um <xref:System.Net.Http.HttpClient> para obter o conteúdo do arquivo. O exemplo a seguir lê um arquivo de configuração ( `cars.json` ) na configuração do aplicativo.

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

Adicione o namespace para <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> para `Program.cs` :

```csharp
using Microsoft.Extensions.Configuration;
```

No `Program.Main` `Program.cs` , modifique o registro de <xref:System.Net.Http.HttpClient> serviço existente para usar o cliente do para ler o arquivo:

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="memory-configuration-source"></a>Fonte de configuração de memória

O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> no `Program.Main` para fornecer configuração adicional.

Adicione o namespace para <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> para `Program.cs` :

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

Em `Program.Main` `Program.cs` :

```csharp
var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

builder.Configuration.Add(memoryConfig);
```

Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração.

`Pages/MemoryConfig.razor`:

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

Obtenha uma seção da configuração em código C# com <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> . O exemplo a seguir obtém a `wheels` seção para a configuração no exemplo anterior:

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>Configuração de autenticação

Forneça a configuração de autenticação em um arquivo de configurações do aplicativo.

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Carregue a configuração de um Identity provedor com o <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> no `Program.Main` . O exemplo a seguir carrega a configuração de um [provedor OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Configuração de log

Adicione uma referência de pacote para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) o arquivo de projeto do aplicativo. No arquivo de configurações do aplicativo, forneça a configuração de log. A configuração de log é carregada no `Program.Main` .

`wwwroot/appsettings.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

Adicione o namespace para <xref:Microsoft.Extensions.Logging?displayProperty=fullName> para `Program.cs` :

```csharp
using Microsoft.Extensions.Logging;
```

Em `Program.Main` `Program.cs` :

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Configuração do host Builder

Ler a configuração do host Builder de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> no `Program.Main` .

Em `Program.Main` `Program.cs` :

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Configuração armazenada em cache

Os arquivos de configuração são armazenados em cache para uso offline. Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação. A edição de arquivos de configuração entre implantações não tem efeito porque:

* Os usuários têm versões em cache dos arquivos que eles continuam a usar.
* Os `service-worker.js` arquivos e do PWA `service-worker-assets.js` devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.

Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .
