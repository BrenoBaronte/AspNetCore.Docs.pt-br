---
title: Modelos de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre os Blazor modelos de aplicativo ASP.NET Core e a Blazor estrutura do projeto.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
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
uid: blazor/templates
ms.openlocfilehash: 0069a6d25c1a8edcfaa454623a95702aae58ca22
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625889"
---
# <a name="aspnet-core-no-locblazor-templates"></a>Modelos de ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

A Blazor estrutura fornece modelos para desenvolver aplicativos para cada um dos Blazor modelos de hospedagem:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Para obter mais informações sobre os Blazor modelos de hospedagem do, consulte <xref:blazor/hosting-models> .

As opções de modelo estão disponíveis passando a `--help` opção para o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando da CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>Blazor estrutura do projeto

Os seguintes arquivos e pastas compõem um Blazor aplicativo gerado a partir de um Blazor modelo de projeto:

* `Program.cs`: O ponto de entrada do aplicativo que configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )
  * Host Webassembly ( Blazor WebAssembly ): o código nesse arquivo é exclusivo para aplicativos criados a partir do Blazor WebAssembly modelo ( `blazorwasm` ).
    * O `App` componente é o componente raiz do aplicativo. O `App` componente é especificado como o `app` elemento DOM ( `<app>...</app>` ) para o conjunto de componentes raiz ( `builder.RootComponents.Add<App>("app")` ).
    * Os [Serviços](xref:blazor/fundamentals/dependency-injection) são adicionados e configurados (por exemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

* `Startup.cs` ( Blazor Server ): Contém a lógica de inicialização do aplicativo. A `Startup` classe define dois métodos:

  * `ConfigureServices`: Configura os serviços de injeção de [dependência (di)](xref:fundamentals/dependency-injection) do aplicativo. Em Blazor Server aplicativos, os serviços são adicionados chamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e o `WeatherForecastService` é adicionado ao contêiner de serviço para ser usado pelo componente de exemplo `FetchData` .
  * `Configure`: Configura o pipeline de tratamento de solicitação do aplicativo:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> é chamado para configurar um ponto de extremidade para a conexão em tempo real com o navegador. A conexão é criada com [SignalR](xref:signalr/introduction) , que é uma estrutura para adicionar funcionalidade da Web em tempo real a aplicativos.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo ( `Pages/_Host.cshtml` ) e habilitar a navegação.

* `wwwroot/index.html` ( Blazor WebAssembly ): A página raiz do aplicativo implementada como uma página HTML:
  * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
  * A página especifica onde o `App` componente raiz é renderizado. O componente é renderizado no local do `app` elemento DOM ( `<app>...</app>` ).
  * O `_framework/blazor.webassembly.js` arquivo JavaScript é carregado, que:
    * Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
    * Inicializa o tempo de execução para executar o aplicativo.

* `App.razor`: O componente raiz do aplicativo que configura o roteamento do lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.

* `Pages` pasta: contém os componentes/páginas roteáveis ( `.razor` ) que compõem o Blazor aplicativo e a Razor página raiz de um Blazor Server aplicativo. A rota para cada página é especificada usando a [`@page`](xref:mvc/views/razor#page) diretiva. O modelo inclui o seguinte:
  * `_Host.cshtml` ( Blazor Server ): A página raiz do aplicativo implementada como um Razor Web
    * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
    * O `_framework/blazor.server.js` arquivo JavaScript é carregado, o que configura a conexão em tempo real SignalR entre o navegador e o servidor.
    * A página host especifica onde o `App` componente raiz ( `App.razor` ) é renderizado.
  * `Counter` ( `Pages/Counter.razor` ): Implementa a página do contador.
  * `Error` ( `Error.razor` Blazor Server somente aplicativo): renderizado quando ocorre uma exceção sem tratamento no aplicativo.
  * `FetchData` ( `Pages/FetchData.razor` ): Implementa a página de busca de dados.
  * `Index` ( `Pages/Index.razor` ): Implementa a Home Page.
  
* `Properties/launchSettings.json`: Mantém a [configuração do ambiente de desenvolvimento](xref:fundamentals/environments#development-and-launchsettingsjson).

* `Shared` pasta: contém outros componentes de interface do usuário ( `.razor` ) usados pelo aplicativo:
  * `MainLayout` ( `MainLayout.razor` ): O componente de [layout](xref:blazor/layouts)do aplicativo.
  * `NavMenu` ( `NavMenu.razor` ): Implementa a navegação na barra lateral. Inclui o [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes. O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.

* `_Imports.razor`: Inclui Razor diretivas comuns para incluir nos componentes do aplicativo ( `.razor` ), como [`@using`](xref:mvc/views/razor#using) diretivas para namespaces.

* `Data` Folder ( Blazor Server ): contém a `WeatherForecast` classe e a implementação do `WeatherForecastService` que fornecem dados meteorológicos de exemplo para o `FetchData` componente do aplicativo.

* `wwwroot`: A pasta [raiz da Web](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo.

* `appsettings.json`: Mantém [as definições de configuração](xref:blazor/fundamentals/configuration) para o aplicativo. Em um Blazor WebAssembly aplicativo, o arquivo de configurações do aplicativo está localizado na `wwwroot` pasta. Em um Blazor Server aplicativo, o arquivo de configurações do aplicativo está localizado na raiz do projeto.
