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
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="f290b-103">Configuração de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f290b-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="f290b-104">Este tópico aplica-se a Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f290b-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="f290b-105">Para obter diretrizes gerais sobre a configuração do aplicativo ASP.NET Core, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f290b-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="f290b-106">Blazor WebAssembly carrega a configuração dos seguintes arquivos de configurações de aplicativo por padrão:</span><span class="sxs-lookup"><span data-stu-id="f290b-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="f290b-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="f290b-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="f290b-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, em que o `{ENVIRONMENT}` espaço reservado é o [ambiente de tempo de execução](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f290b-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f290b-109">Outros provedores de configuração registrados pelo aplicativo também podem fornecer configuração, mas nem todos os provedores ou recursos de provedor são apropriados para Blazor WebAssembly aplicativos:</span><span class="sxs-lookup"><span data-stu-id="f290b-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="f290b-110">[Azure Key Vault provedor de configuração](xref:security/key-vault-configuration): o provedor não tem suporte para identidade gerenciada e ID do aplicativo (ID do cliente) com cenários de segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f290b-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="f290b-111">A ID do aplicativo com um segredo do cliente não é recomendada para nenhum aplicativo ASP.NET Core, especialmente Blazor WebAssembly aplicativos porque o segredo do cliente não pode ser protegido do lado do cliente para acessar o serviço Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="f290b-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="f290b-112">[Provedor de configuração de Azure app](/azure/azure-app-configuration/quickstart-aspnet-core-app): o provedor não é apropriado para Blazor WebAssembly aplicativos porque os Blazor WebAssembly aplicativos não são executados em um servidor no Azure.</span><span class="sxs-lookup"><span data-stu-id="f290b-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="f290b-113">A configuração em um Blazor WebAssembly aplicativo é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="f290b-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="f290b-114">**Não armazene segredos do aplicativo, credenciais ou quaisquer outros dados confidenciais na configuração de um Blazor WebAssembly aplicativo.**</span><span class="sxs-lookup"><span data-stu-id="f290b-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="f290b-115">Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f290b-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="f290b-116">Configuração de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f290b-116">App settings configuration</span></span>

<span data-ttu-id="f290b-117">A configuração em arquivos de configurações do aplicativo é carregada por padrão.</span><span class="sxs-lookup"><span data-stu-id="f290b-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="f290b-118">No exemplo a seguir, um valor de configuração de interface do usuário é armazenado em um arquivo de configurações do aplicativo e carregado pela Blazor estrutura automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f290b-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="f290b-119">O valor é lido por um componente.</span><span class="sxs-lookup"><span data-stu-id="f290b-119">The value is read by a component.</span></span>

<span data-ttu-id="f290b-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f290b-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="f290b-121">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="f290b-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="f290b-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="f290b-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="f290b-123">Para ler outros arquivos de configuração da `wwwroot` pasta para a configuração, use um <xref:System.Net.Http.HttpClient> para obter o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="f290b-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="f290b-124">O exemplo a seguir lê um arquivo de configuração ( `cars.json` ) na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f290b-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="f290b-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="f290b-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="f290b-126">Adicione o namespace para <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f290b-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="f290b-127">No `Program.Main` `Program.cs` , modifique o registro de <xref:System.Net.Http.HttpClient> serviço existente para usar o cliente do para ler o arquivo:</span><span class="sxs-lookup"><span data-stu-id="f290b-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

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

## <a name="memory-configuration-source"></a><span data-ttu-id="f290b-128">Fonte de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="f290b-128">Memory Configuration Source</span></span>

<span data-ttu-id="f290b-129">O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> no `Program.Main` para fornecer configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="f290b-129">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="f290b-130">Adicione o namespace para <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f290b-130">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="f290b-131">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f290b-131">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="f290b-132">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="f290b-132">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="f290b-133">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="f290b-133">`Pages/MemoryConfig.razor`:</span></span>

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

<span data-ttu-id="f290b-134">Obtenha uma seção da configuração em código C# com <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f290b-134">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f290b-135">O exemplo a seguir obtém a `wheels` seção para a configuração no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="f290b-135">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="f290b-136">Configuração de autenticação</span><span class="sxs-lookup"><span data-stu-id="f290b-136">Authentication configuration</span></span>

<span data-ttu-id="f290b-137">Forneça a configuração de autenticação em um arquivo de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f290b-137">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="f290b-138">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f290b-138">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="f290b-139">Carregue a configuração de um Identity provedor com o <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="f290b-139">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="f290b-140">O exemplo a seguir carrega a configuração de um [provedor OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).</span><span class="sxs-lookup"><span data-stu-id="f290b-140">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="f290b-141">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f290b-141">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="f290b-142">Configuração de log</span><span class="sxs-lookup"><span data-stu-id="f290b-142">Logging configuration</span></span>

<span data-ttu-id="f290b-143">Adicione uma referência de pacote para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f290b-143">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="f290b-144">No arquivo de configurações do aplicativo, forneça a configuração de log.</span><span class="sxs-lookup"><span data-stu-id="f290b-144">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="f290b-145">A configuração de log é carregada no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="f290b-145">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="f290b-146">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f290b-146">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="f290b-147">Adicione o namespace para <xref:Microsoft.Extensions.Logging?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f290b-147">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="f290b-148">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f290b-148">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="f290b-149">Configuração do host Builder</span><span class="sxs-lookup"><span data-stu-id="f290b-149">Host builder configuration</span></span>

<span data-ttu-id="f290b-150">Ler a configuração do host Builder de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="f290b-150">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="f290b-151">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f290b-151">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="f290b-152">Configuração armazenada em cache</span><span class="sxs-lookup"><span data-stu-id="f290b-152">Cached configuration</span></span>

<span data-ttu-id="f290b-153">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="f290b-153">Configuration files are cached for offline use.</span></span> <span data-ttu-id="f290b-154">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="f290b-154">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="f290b-155">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="f290b-155">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="f290b-156">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="f290b-156">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="f290b-157">Os `service-worker.js` arquivos e do PWA `service-worker-assets.js` devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="f290b-157">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="f290b-158">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="f290b-158">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
