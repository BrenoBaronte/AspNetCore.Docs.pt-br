---
title: BlazorEstrutura do projeto ASP.NET Core
author: guardrex
description: Saiba mais sobre a Blazor estrutura do projeto de aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
ms.openlocfilehash: ae41d096c50d350b7fcde52da59382614e62c109
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658619"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>BlazorEstrutura do projeto ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Este artigo descreve os arquivos e as pastas que compõem um Blazor aplicativo gerado a partir dos Blazor modelos de projeto.

## Blazor WebAssembly

O Blazor WebAssembly modelo ( `blazorwasm` ) cria os arquivos iniciais e a estrutura de diretório para um Blazor WebAssembly aplicativo. O aplicativo é populado com código de demonstração para um `FetchData` componente que carrega dados de um ativo estático, `weather.json` e interação do usuário com um `Counter` componente.

* `Pages` pasta: contém os componentes/páginas roteáveis ( `.razor` ) que compõem o Blazor aplicativo. A rota para cada página é especificada usando a [`@page`](xref:mvc/views/razor#page) diretiva. O modelo inclui os seguintes componentes:
  * `Counter` componente ( `Counter.razor` ): implementa a página do contador.
  * `FetchData` Component ( `FetchData.razor` ): implementa a página de busca de dados.
  * `Index` Component ( `Index.razor` ): implementa a Home Page.
  
* `Properties/launchSettings.json`: Mantém a [configuração do ambiente de desenvolvimento](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared` pasta: contém os seguintes componentes compartilhados e folhas de estilo:
  * `MainLayout` Component ( `MainLayout.razor` ): o componente de [layout](xref:blazor/layouts)do aplicativo.
  * `MainLayout.razor.css`: Stylesheet para o layout principal do aplicativo.
  * `NavMenu` Component ( `NavMenu.razor` ): implementa a navegação na barra lateral. Inclui o [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes. O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.
  * `NavMenu.razor.css`: Folha de estilos do menu de navegação do aplicativo.
  * `SurveyPrompt` componente ( `SurveyPrompt.razor` ): Blazor componente de pesquisa.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` pasta: contém os seguintes componentes compartilhados:
  * `MainLayout` Component ( `MainLayout.razor` ): o componente de [layout](xref:blazor/layouts)do aplicativo.
  * `NavMenu` Component ( `NavMenu.razor` ): implementa a navegação na barra lateral. Inclui o [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes. O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.
  * `SurveyPrompt` componente ( `SurveyPrompt.razor` ): Blazor componente de pesquisa.
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`: A pasta [raiz da Web](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo, incluindo `appsettings.json` e arquivos de configurações de aplicativo ambiental para [definições de configuração](xref:blazor/fundamentals/configuration). A `index.html` página da Web é a página raiz do aplicativo implementada como uma página HTML:
  * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
  * A página especifica onde o `App` componente raiz é renderizado. O componente é renderizado no local do `div` elemento DOM com um `id` de `app` ( `<div id="app">Loading...</div>` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`: A pasta [raiz da Web](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo, incluindo `appsettings.json` e arquivos de configurações de aplicativo ambiental para [definições de configuração](xref:blazor/fundamentals/configuration). A `index.html` página da Web é a página raiz do aplicativo implementada como uma página HTML:
  * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
  * A página especifica onde o `App` componente raiz é renderizado. O componente é renderizado no local do `app` elemento DOM ( `<app>Loading...</app>` ).

::: moniker-end

* `_Imports.razor`: Inclui Razor diretivas comuns para incluir nos componentes do aplicativo ( `.razor` ), como [`@using`](xref:mvc/views/razor#using) diretivas para namespaces.

* `App.razor`: O componente raiz do aplicativo que configura o roteamento do lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: O ponto de entrada do aplicativo que configura o host do Webassembly:
  
  * O `App` componente é o componente raiz do aplicativo. O `App` componente é especificado como o `div` elemento DOM com um `id` de `app` ( `<div id="app">Loading...</div>` in `wwwroot/index.html` ) para o conjunto de componentes raiz ( `builder.RootComponents.Add<App>("#app")` ).
  * Os [Serviços](xref:blazor/fundamentals/dependency-injection) são adicionados e configurados (por exemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: O ponto de entrada do aplicativo que configura o host do Webassembly:

  * O `App` componente é o componente raiz do aplicativo. O `App` componente é especificado como o `app` elemento DOM ( `<app>Loading...</app>` in `wwwroot/index.html` ) para o conjunto de componentes raiz ( `builder.RootComponents.Add<App>("app")` ).
  * Os [Serviços](xref:blazor/fundamentals/dependency-injection) são adicionados e configurados (por exemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

## Blazor Server

O Blazor Server modelo ( `blazorserver` ) cria os arquivos iniciais e a estrutura de diretório para um Blazor Server aplicativo. O aplicativo é populado com código de demonstração para um `FetchData` componente que carrega dados de um serviço registrado, `WeatherForecastService` e interação do usuário com um `Counter` componente.

* `Data` pasta: contém a `WeatherForecast` classe e a implementação do `WeatherForecastService` que fornece dados de clima de exemplo para o `FetchData` componente do aplicativo.

* `Pages` pasta: contém os componentes/páginas roteáveis ( `.razor` ) que compõem o Blazor aplicativo e a Razor página raiz de um Blazor Server aplicativo. A rota para cada página é especificada usando a [`@page`](xref:mvc/views/razor#page) diretiva. O modelo inclui o seguinte:
  * `_Host.cshtml`: A página raiz do aplicativo implementada como uma Razor página:
    * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
    * A página host especifica onde o `App` componente raiz ( `App.razor` ) é renderizado.
  * `Counter` componente ( `Counter.razor` ): implementa a página do contador.
  * `Error` componente ( `Error.razor` ): renderizado quando ocorre uma exceção sem tratamento no aplicativo.
  * `FetchData` Component ( `FetchData.razor` ): implementa a página de busca de dados.
  * `Index` Component ( `Index.razor` ): implementa a Home Page.
  
* `Properties/launchSettings.json`: Mantém a [configuração do ambiente de desenvolvimento](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared` pasta: contém os seguintes componentes compartilhados e folhas de estilo:
  * `MainLayout` Component ( `MainLayout.razor` ): o componente de [layout](xref:blazor/layouts)do aplicativo.
  * `MainLayout.razor.css`: Stylesheet para o layout principal do aplicativo.
  * `NavMenu` Component ( `NavMenu.razor` ): implementa a navegação na barra lateral. Inclui o [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes. O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.
  * `NavMenu.razor.css`: Folha de estilos do menu de navegação do aplicativo.
  * `SurveyPrompt` componente ( `SurveyPrompt.razor` ): Blazor componente de pesquisa.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` pasta: contém os seguintes componentes compartilhados:
  * `MainLayout` Component ( `MainLayout.razor` ): o componente de [layout](xref:blazor/layouts)do aplicativo.
  * `NavMenu` Component ( `NavMenu.razor` ): implementa a navegação na barra lateral. Inclui o [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes. O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.
  * `SurveyPrompt` componente ( `SurveyPrompt.razor` ): Blazor componente de pesquisa.
  
::: moniker-end

* `wwwroot`: A pasta [raiz da Web](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo.

* `_Imports.razor`: Inclui Razor diretivas comuns para incluir nos componentes do aplicativo ( `.razor` ), como [`@using`](xref:mvc/views/razor#using) diretivas para namespaces.

* `App.razor`: O componente raiz do aplicativo que configura o roteamento do lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.

* `appsettings.json` e arquivos de configurações do aplicativo ambiental: fornecem [definições de configuração](xref:blazor/fundamentals/configuration) para o aplicativo.

* `Program.cs`: O ponto de entrada do aplicativo que configura o [host](xref:fundamentals/host/generic-host)ASP.NET Core.

* `Startup.cs`: Contém a lógica de inicialização do aplicativo. A `Startup` classe define dois métodos:

  * `ConfigureServices`: Configura os serviços de injeção de [dependência (di)](xref:fundamentals/dependency-injection) do aplicativo. Os serviços são adicionados chamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e o `WeatherForecastService` é adicionado ao contêiner de serviço para ser usado pelo componente de exemplo `FetchData` .
  * `Configure`: Configura o pipeline de tratamento de solicitação do aplicativo:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> é chamado para configurar um ponto de extremidade para a conexão em tempo real com o navegador. A conexão é criada com [SignalR](xref:signalr/introduction) , que é uma estrutura para adicionar funcionalidade da Web em tempo real a aplicativos.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo ( `Pages/_Host.cshtml` ) e habilitar a navegação.
