---
title: 'Modelos de ASP.NET Core Blazor'
author: guardrex
description: 'Saiba mais sobre os Blazor modelos de aplicativo ASP.NET Core e a Blazor estrutura do projeto.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/templates
ms.openlocfilehash: eef381367d7aa59dcc430c529746088d4488e700
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054925"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="6cf57-103">Modelos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6cf57-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="6cf57-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6cf57-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6cf57-105">A Blazor estrutura fornece modelos para desenvolver aplicativos para cada um dos Blazor modelos de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="6cf57-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="6cf57-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="6cf57-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="6cf57-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="6cf57-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="6cf57-108">Para obter mais informações sobre os Blazor modelos de hospedagem do, consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="6cf57-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="6cf57-109">As opções de modelo estão disponíveis passando a `--help` opção para o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando da CLI:</span><span class="sxs-lookup"><span data-stu-id="6cf57-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="6cf57-110">Blazor estrutura do projeto</span><span class="sxs-lookup"><span data-stu-id="6cf57-110">Blazor project structure</span></span>

<span data-ttu-id="6cf57-111">Os seguintes arquivos e pastas compõem um Blazor aplicativo gerado a partir de um Blazor modelo de projeto:</span><span class="sxs-lookup"><span data-stu-id="6cf57-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

* <span data-ttu-id="6cf57-112">`Program.cs`: O ponto de entrada do aplicativo que configura:</span><span class="sxs-lookup"><span data-stu-id="6cf57-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="6cf57-113">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="6cf57-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="6cf57-114">Host Webassembly ( Blazor WebAssembly ): o código nesse arquivo é exclusivo para aplicativos criados a partir do Blazor WebAssembly modelo ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="6cf57-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="6cf57-115">O `App` componente é o componente raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="6cf57-116">O `App` componente é especificado como o `app` elemento DOM ( `<app>...</app>` ) para o conjunto de componentes raiz ( `builder.RootComponents.Add<App>("app")` ).</span><span class="sxs-lookup"><span data-stu-id="6cf57-116">The `App` component is specified as the `app` DOM element (`<app>...</app>`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="6cf57-117">Os [Serviços](xref:blazor/fundamentals/dependency-injection) são adicionados e configurados (por exemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="6cf57-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

* <span data-ttu-id="6cf57-118">`Startup.cs` ( Blazor Server ): Contém a lógica de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="6cf57-119">A `Startup` classe define dois métodos:</span><span class="sxs-lookup"><span data-stu-id="6cf57-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="6cf57-120">`ConfigureServices`: Configura os serviços de injeção de [dependência (di)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="6cf57-121">Em Blazor Server aplicativos, os serviços são adicionados chamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e o `WeatherForecastService` é adicionado ao contêiner de serviço para ser usado pelo componente de exemplo `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="6cf57-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="6cf57-122">`Configure`: Configura o pipeline de tratamento de solicitação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6cf57-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="6cf57-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> é chamado para configurar um ponto de extremidade para a conexão em tempo real com o navegador.</span><span class="sxs-lookup"><span data-stu-id="6cf57-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="6cf57-124">A conexão é criada com [SignalR](xref:signalr/introduction) , que é uma estrutura para adicionar funcionalidade da Web em tempo real a aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6cf57-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="6cf57-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo ( `Pages/_Host.cshtml` ) e habilitar a navegação.</span><span class="sxs-lookup"><span data-stu-id="6cf57-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="6cf57-126">`wwwroot/index.html` ( Blazor WebAssembly ): A página raiz do aplicativo implementada como uma página HTML:</span><span class="sxs-lookup"><span data-stu-id="6cf57-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="6cf57-127">Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.</span><span class="sxs-lookup"><span data-stu-id="6cf57-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="6cf57-128">A página especifica onde o `App` componente raiz é renderizado.</span><span class="sxs-lookup"><span data-stu-id="6cf57-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="6cf57-129">O componente é renderizado no local do `app` elemento DOM ( `<app>...</app>` ).</span><span class="sxs-lookup"><span data-stu-id="6cf57-129">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="6cf57-130">O `_framework/blazor.webassembly.js` arquivo JavaScript é carregado, que:</span><span class="sxs-lookup"><span data-stu-id="6cf57-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="6cf57-131">Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="6cf57-132">Inicializa o tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="6cf57-133">`App.razor`: O componente raiz do aplicativo que configura o roteamento do lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente.</span><span class="sxs-lookup"><span data-stu-id="6cf57-133">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="6cf57-134">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.</span><span class="sxs-lookup"><span data-stu-id="6cf57-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="6cf57-135">`Pages` pasta: contém os componentes/páginas roteáveis ( `.razor` ) que compõem o Blazor aplicativo e a Razor página raiz de um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-135">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="6cf57-136">A rota para cada página é especificada usando a [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="6cf57-136">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="6cf57-137">O modelo inclui o seguinte:</span><span class="sxs-lookup"><span data-stu-id="6cf57-137">The template includes the following:</span></span>
  * <span data-ttu-id="6cf57-138">`_Host.cshtml` ( Blazor Server ): A página raiz do aplicativo implementada como um Razor Web</span><span class="sxs-lookup"><span data-stu-id="6cf57-138">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="6cf57-139">Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.</span><span class="sxs-lookup"><span data-stu-id="6cf57-139">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="6cf57-140">O `_framework/blazor.server.js` arquivo JavaScript é carregado, o que configura a conexão em tempo real SignalR entre o navegador e o servidor.</span><span class="sxs-lookup"><span data-stu-id="6cf57-140">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="6cf57-141">A página host especifica onde o `App` componente raiz ( `App.razor` ) é renderizado.</span><span class="sxs-lookup"><span data-stu-id="6cf57-141">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="6cf57-142">`Counter` ( `Pages/Counter.razor` ): Implementa a página do contador.</span><span class="sxs-lookup"><span data-stu-id="6cf57-142">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="6cf57-143">`Error` ( `Error.razor` Blazor Server somente aplicativo): renderizado quando ocorre uma exceção sem tratamento no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-143">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="6cf57-144">`FetchData` ( `Pages/FetchData.razor` ): Implementa a página de busca de dados.</span><span class="sxs-lookup"><span data-stu-id="6cf57-144">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="6cf57-145">`Index` ( `Pages/Index.razor` ): Implementa a Home Page.</span><span class="sxs-lookup"><span data-stu-id="6cf57-145">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="6cf57-146">`Properties/launchSettings.json`: Mantém a [configuração do ambiente de desenvolvimento](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="6cf57-146">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="6cf57-147">`Shared` pasta: contém outros componentes de interface do usuário ( `.razor` ) usados pelo aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6cf57-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="6cf57-148">`MainLayout` ( `MainLayout.razor` ): O componente de [layout](xref:blazor/layouts)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-148">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="6cf57-149">`NavMenu` ( `NavMenu.razor` ): Implementa a navegação na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="6cf57-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="6cf57-150">Inclui o [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="6cf57-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="6cf57-151">O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.</span><span class="sxs-lookup"><span data-stu-id="6cf57-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="6cf57-152">`_Imports.razor`: Inclui Razor diretivas comuns para incluir nos componentes do aplicativo ( `.razor` ), como [`@using`](xref:mvc/views/razor#using) diretivas para namespaces.</span><span class="sxs-lookup"><span data-stu-id="6cf57-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="6cf57-153">`Data` Folder ( Blazor Server ): contém a `WeatherForecast` classe e a implementação do `WeatherForecastService` que fornecem dados meteorológicos de exemplo para o `FetchData` componente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="6cf57-154">`wwwroot`: A pasta [raiz da Web](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="6cf57-155">`appsettings.json`: Mantém [as definições de configuração](xref:blazor/fundamentals/configuration) para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6cf57-155">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="6cf57-156">Em um Blazor WebAssembly aplicativo, o arquivo de configurações do aplicativo está localizado na `wwwroot` pasta.</span><span class="sxs-lookup"><span data-stu-id="6cf57-156">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="6cf57-157">Em um Blazor Server aplicativo, o arquivo de configurações do aplicativo está localizado na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="6cf57-157">In a Blazor Server app, the app settings file is located at the project root.</span></span>
