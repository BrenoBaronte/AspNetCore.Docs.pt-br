---
title: Ambientes de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre ambientes no Blazor , incluindo como definir o ambiente de um Blazor WebAssembly aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: a5ead59e467da331b585e8daefb1d7d259c7edba
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101042"
---
# <a name="aspnet-core-blazor-environments"></a>Ambientes de ASP.NET Core Blazor

> [!NOTE]
> Este tópico aplica-se a Blazor WebAssembly . Para obter diretrizes gerais sobre ASP.NET Core configuração de aplicativo, que descreve as abordagens a serem usadas para Blazor Server aplicativos, consulte <xref:fundamentals/environments> .

Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento. Quando o aplicativo é publicado, o ambiente assume como padrão a produção.

O aplicativo do lado do cliente Blazor ( *`Client`* ) de uma Blazor WebAssembly solução hospedada determina o ambiente do *`Server`* aplicativo da solução por meio de um middleware que comunica o ambiente ao navegador. O *`Server`* aplicativo adiciona um cabeçalho chamado `blazor-environment` com o ambiente como o valor do cabeçalho. O *`Client`* aplicativo lê o cabeçalho. O *`Server`* aplicativo da solução é um aplicativo ASP.NET Core, portanto, mais informações sobre como configurar o ambiente são encontradas em <xref:fundamentals/environments> .

Para um Blazor WebAssembly aplicativo autônomo em execução localmente, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente de desenvolvimento. Para especificar o ambiente para outros ambientes de hospedagem, adicione o `blazor-environment` cabeçalho.

No exemplo a seguir para o IIS, o cabeçalho personalizado ( `blazor-environment` ) é adicionado ao `web.config` arquivo publicado. O `web.config` arquivo está localizado na `bin/Release/{TARGET FRAMEWORK}/publish` pasta, em que o espaço reservado `{TARGET FRAMEWORK}` é a estrutura de destino:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Para usar um `web.config` arquivo personalizado para o IIS que não é substituído quando o aplicativo é publicado na `publish` pasta, consulte <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .

Obtenha o ambiente do aplicativo em um componente injetando <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e lendo a <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriedade.

`Pages/ReadEnvironment.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

Durante a inicialização, o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expõe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> através da <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriedade, que habilita a lógica específica do ambiente no código do construtor de hosts.

Em `Program.Main` `Program.cs` :

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Os seguintes métodos de extensão de conveniência fornecidos por meio de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permitir a verificação do ambiente atual para desenvolvimento, produção, preparo e nomes de ambiente personalizados:

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

Em `Program.Main` `Program.cs` :

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

A <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriedade pode ser usada durante a inicialização quando o <xref:Microsoft.AspNetCore.Components.NavigationManager> serviço não está disponível.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/environments>
