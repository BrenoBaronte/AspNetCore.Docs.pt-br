---
title: BlazorEstrutura do projeto ASP.NET Core
author: guardrex
description: Saiba mais sobre a Blazor estrutura do projeto de aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/project-structure
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513542"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="33864-103">BlazorEstrutura do projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33864-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="33864-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="33864-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="33864-105">Os seguintes arquivos e pastas compõem um Blazor aplicativo gerado a partir de um Blazor modelo de projeto:</span><span class="sxs-lookup"><span data-stu-id="33864-105">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="33864-106">`Program.cs`: O ponto de entrada do aplicativo que configura:</span><span class="sxs-lookup"><span data-stu-id="33864-106">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="33864-107">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="33864-107">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="33864-108">Host Webassembly ( Blazor WebAssembly ): o código nesse arquivo é exclusivo para aplicativos criados a partir do Blazor WebAssembly modelo ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="33864-108">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="33864-109">O `App` componente é o componente raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-109">The `App` component is the root component of the app.</span></span> <span data-ttu-id="33864-110">O `App` componente é especificado como o `div` elemento DOM com um `id` de `app` ( `<div id="app">Loading...</div>` in `wwwroot/index.html` ) para o conjunto de componentes raiz ( `builder.RootComponents.Add<App>("#app")` ).</span><span class="sxs-lookup"><span data-stu-id="33864-110">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="33864-111">Os [Serviços](xref:blazor/fundamentals/dependency-injection) são adicionados e configurados (por exemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="33864-111">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="33864-112">`Program.cs`: O ponto de entrada do aplicativo que configura:</span><span class="sxs-lookup"><span data-stu-id="33864-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="33864-113">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="33864-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="33864-114">Host Webassembly ( Blazor WebAssembly ): o código nesse arquivo é exclusivo para aplicativos criados a partir do Blazor WebAssembly modelo ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="33864-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="33864-115">O `App` componente é o componente raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="33864-116">O `App` componente é especificado como o `app` elemento DOM ( `<app>Loading...</app>` in `wwwroot/index.html` ) para o conjunto de componentes raiz ( `builder.RootComponents.Add<App>("app")` ).</span><span class="sxs-lookup"><span data-stu-id="33864-116">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="33864-117">Os [Serviços](xref:blazor/fundamentals/dependency-injection) são adicionados e configurados (por exemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="33864-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="33864-118">`Startup.cs` ( Blazor Server ): Contém a lógica de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="33864-119">A `Startup` classe define dois métodos:</span><span class="sxs-lookup"><span data-stu-id="33864-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="33864-120">`ConfigureServices`: Configura os serviços de injeção de [dependência (di)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="33864-121">Em Blazor Server aplicativos, os serviços são adicionados chamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e o `WeatherForecastService` é adicionado ao contêiner de serviço para ser usado pelo componente de exemplo `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="33864-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="33864-122">`Configure`: Configura o pipeline de tratamento de solicitação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="33864-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="33864-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> é chamado para configurar um ponto de extremidade para a conexão em tempo real com o navegador.</span><span class="sxs-lookup"><span data-stu-id="33864-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="33864-124">A conexão é criada com [SignalR](xref:signalr/introduction) , que é uma estrutura para adicionar funcionalidade da Web em tempo real a aplicativos.</span><span class="sxs-lookup"><span data-stu-id="33864-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="33864-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo ( `Pages/_Host.cshtml` ) e habilitar a navegação.</span><span class="sxs-lookup"><span data-stu-id="33864-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="33864-126">`wwwroot/index.html` ( Blazor WebAssembly ): A página raiz do aplicativo implementada como uma página HTML:</span><span class="sxs-lookup"><span data-stu-id="33864-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="33864-127">Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.</span><span class="sxs-lookup"><span data-stu-id="33864-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="33864-128">A página especifica onde o `App` componente raiz é renderizado.</span><span class="sxs-lookup"><span data-stu-id="33864-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="33864-129">O componente é renderizado no local do `div` elemento DOM com um `id` de `app` ( `<div id="app">Loading...</div>` ).</span><span class="sxs-lookup"><span data-stu-id="33864-129">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>
  * <span data-ttu-id="33864-130">O `_framework/blazor.webassembly.js` arquivo JavaScript é carregado, que:</span><span class="sxs-lookup"><span data-stu-id="33864-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="33864-131">Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="33864-132">Inicializa o tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-132">Initializes the runtime to run the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="33864-133">`wwwroot/index.html` ( Blazor WebAssembly ): A página raiz do aplicativo implementada como uma página HTML:</span><span class="sxs-lookup"><span data-stu-id="33864-133">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="33864-134">Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.</span><span class="sxs-lookup"><span data-stu-id="33864-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="33864-135">A página especifica onde o `App` componente raiz é renderizado.</span><span class="sxs-lookup"><span data-stu-id="33864-135">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="33864-136">O componente é renderizado no local do `app` elemento DOM ( `<app>Loading...</app>` ).</span><span class="sxs-lookup"><span data-stu-id="33864-136">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>
  * <span data-ttu-id="33864-137">O `_framework/blazor.webassembly.js` arquivo JavaScript é carregado, que:</span><span class="sxs-lookup"><span data-stu-id="33864-137">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="33864-138">Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-138">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="33864-139">Inicializa o tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-139">Initializes the runtime to run the app.</span></span>

::: moniker-end

* <span data-ttu-id="33864-140">`App.razor`: O componente raiz do aplicativo que configura o roteamento do lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente.</span><span class="sxs-lookup"><span data-stu-id="33864-140">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="33864-141">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.</span><span class="sxs-lookup"><span data-stu-id="33864-141">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="33864-142">`Pages` pasta: contém os componentes/páginas roteáveis ( `.razor` ) que compõem o Blazor aplicativo e a Razor página raiz de um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-142">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="33864-143">A rota para cada página é especificada usando a [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="33864-143">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="33864-144">O modelo inclui o seguinte:</span><span class="sxs-lookup"><span data-stu-id="33864-144">The template includes the following:</span></span>
  * <span data-ttu-id="33864-145">`_Host.cshtml` ( Blazor Server ): A página raiz do aplicativo implementada como um Razor Web</span><span class="sxs-lookup"><span data-stu-id="33864-145">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="33864-146">Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.</span><span class="sxs-lookup"><span data-stu-id="33864-146">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="33864-147">O `_framework/blazor.server.js` arquivo JavaScript é carregado, o que configura a conexão em tempo real SignalR entre o navegador e o servidor.</span><span class="sxs-lookup"><span data-stu-id="33864-147">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="33864-148">A página host especifica onde o `App` componente raiz ( `App.razor` ) é renderizado.</span><span class="sxs-lookup"><span data-stu-id="33864-148">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="33864-149">`Counter` componente ( `Pages/Counter.razor` ): implementa a página do contador.</span><span class="sxs-lookup"><span data-stu-id="33864-149">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="33864-150">`Error` componente ( `Error.razor` , Blazor Server somente aplicativo): renderizado quando ocorre uma exceção sem tratamento no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-150">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="33864-151">`FetchData` Component ( `Pages/FetchData.razor` ): implementa a página de busca de dados.</span><span class="sxs-lookup"><span data-stu-id="33864-151">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="33864-152">`Index` Component ( `Pages/Index.razor` ): implementa a Home Page.</span><span class="sxs-lookup"><span data-stu-id="33864-152">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="33864-153">`Properties/launchSettings.json`: Mantém a [configuração do ambiente de desenvolvimento](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="33864-153">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="33864-154">`Shared` pasta: contém outros componentes de interface do usuário ( `.razor` ) usados pelo aplicativo:</span><span class="sxs-lookup"><span data-stu-id="33864-154">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="33864-155">`MainLayout` Component ( `MainLayout.razor` ): o componente de [layout](xref:blazor/layouts)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-155">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="33864-156">`MainLayout.razor.css`: Stylesheet para o layout principal do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-156">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="33864-157">`NavMenu` Component ( `NavMenu.razor` ): implementa a navegação na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="33864-157">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="33864-158">Inclui o [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="33864-158">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="33864-159">O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.</span><span class="sxs-lookup"><span data-stu-id="33864-159">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="33864-160">`NavMenu.razor.css`: Folha de estilos do menu de navegação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-160">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="33864-161">`Shared` pasta: contém outros componentes de interface do usuário ( `.razor` ) usados pelo aplicativo:</span><span class="sxs-lookup"><span data-stu-id="33864-161">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="33864-162">`MainLayout` Component ( `MainLayout.razor` ): o componente de [layout](xref:blazor/layouts)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-162">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="33864-163">`NavMenu` Component ( `NavMenu.razor` ): implementa a navegação na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="33864-163">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="33864-164">Inclui o [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="33864-164">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="33864-165">O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.</span><span class="sxs-lookup"><span data-stu-id="33864-165">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="33864-166">`_Imports.razor`: Inclui Razor diretivas comuns para incluir nos componentes do aplicativo ( `.razor` ), como [`@using`](xref:mvc/views/razor#using) diretivas para namespaces.</span><span class="sxs-lookup"><span data-stu-id="33864-166">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="33864-167">`Data` Folder ( Blazor Server ): contém a `WeatherForecast` classe e a implementação do `WeatherForecastService` que fornecem dados meteorológicos de exemplo para o `FetchData` componente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-167">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="33864-168">`wwwroot`: A pasta [raiz da Web](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-168">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="33864-169">`appsettings.json`: Mantém [as definições de configuração](xref:blazor/fundamentals/configuration) para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33864-169">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="33864-170">Em um Blazor WebAssembly aplicativo, o arquivo de configurações do aplicativo está localizado na `wwwroot` pasta.</span><span class="sxs-lookup"><span data-stu-id="33864-170">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="33864-171">Em um Blazor Server aplicativo, o arquivo de configurações do aplicativo está localizado na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="33864-171">In a Blazor Server app, the app settings file is located at the project root.</span></span>
