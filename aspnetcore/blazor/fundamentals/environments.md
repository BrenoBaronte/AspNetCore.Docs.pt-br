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
ms.openlocfilehash: 3d9b0cab42a826c7a5868324d891e597cd9ed986
ms.sourcegitcommit: b1a0257a93eca34bad0e64d60db4f6b914e03f89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678292"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="e60fb-103">Ambientes de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e60fb-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="e60fb-104">Este tópico aplica-se a Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="e60fb-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="e60fb-105">Para obter diretrizes gerais sobre ASP.NET Core configuração de aplicativo, que descreve as abordagens a serem usadas para Blazor Server aplicativos, consulte <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="e60fb-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e60fb-106">Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e60fb-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="e60fb-107">Quando o aplicativo é publicado, o ambiente assume como padrão a produção.</span><span class="sxs-lookup"><span data-stu-id="e60fb-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="e60fb-108">O aplicativo do lado do cliente Blazor ( *`Client`* ) de uma Blazor WebAssembly solução hospedada determina o ambiente do *`Server`* aplicativo da solução por meio de um middleware que comunica o ambiente ao navegador.</span><span class="sxs-lookup"><span data-stu-id="e60fb-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="e60fb-109">O *`Server`* aplicativo adiciona um cabeçalho chamado `blazor-environment` com o ambiente como o valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="e60fb-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="e60fb-110">O *`Client`* aplicativo lê o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="e60fb-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="e60fb-111">O *`Server`* aplicativo da solução é um aplicativo ASP.NET Core, portanto, mais informações sobre como configurar o ambiente são encontradas em <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="e60fb-111">The *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e60fb-112">Para um Blazor WebAssembly aplicativo autônomo em execução localmente, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e60fb-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="e60fb-113">Para especificar o ambiente para outros ambientes de hospedagem, adicione o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="e60fb-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="e60fb-114">No exemplo a seguir para o IIS, o cabeçalho personalizado ( `blazor-environment` ) é adicionado ao `web.config` arquivo publicado.</span><span class="sxs-lookup"><span data-stu-id="e60fb-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="e60fb-115">O `web.config` arquivo está localizado na `bin/Release/{TARGET FRAMEWORK}/publish` pasta, em que o espaço reservado `{TARGET FRAMEWORK}` é a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="e60fb-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

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
> <span data-ttu-id="e60fb-116">Para usar um `web.config` arquivo personalizado para o IIS que não é substituído quando o aplicativo é publicado na `publish` pasta, consulte <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="e60fb-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="e60fb-117">Obtenha o ambiente do aplicativo em um componente injetando <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e lendo a <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriedade.</span><span class="sxs-lookup"><span data-stu-id="e60fb-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="e60fb-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="e60fb-118">`Pages/ReadEnvironment.razor`:</span></span>

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

<span data-ttu-id="e60fb-119">Durante a inicialização, o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expõe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> através da <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriedade, que habilita a lógica específica do ambiente no código do construtor de hosts.</span><span class="sxs-lookup"><span data-stu-id="e60fb-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="e60fb-120">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="e60fb-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="e60fb-121">Os seguintes métodos de extensão de conveniência fornecidos por meio de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permitir a verificação do ambiente atual para desenvolvimento, produção, preparo e nomes de ambiente personalizados:</span><span class="sxs-lookup"><span data-stu-id="e60fb-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="e60fb-122">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="e60fb-122">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="e60fb-123">A <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriedade pode ser usada durante a inicialização quando o <xref:Microsoft.AspNetCore.Components.NavigationManager> serviço não está disponível.</span><span class="sxs-lookup"><span data-stu-id="e60fb-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e60fb-124">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e60fb-124">Additional resources</span></span>

* <xref:fundamentals/environments>
