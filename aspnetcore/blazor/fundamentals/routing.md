---
title: Roteamento de ASP.NET Core Blazor
author: guardrex
description: Saiba como gerenciar o roteamento de solicitações em aplicativos e como usar o componente NavLink em Blazor aplicativos para navegação.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: d6f64e67ad799847c0992bad8e4353bac07c9901
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485947"
---
# <a name="aspnet-core-no-locblazor-routing"></a>Roteamento de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex)

Neste artigo, saiba como gerenciar o roteamento de solicitações e como usar o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente para criar links de navegação em Blazor aplicativos.

## <a name="route-templates"></a>Modelos de rota

O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite o roteamento para Razor componentes em um Blazor aplicativo. O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente é usado no `App` componente de Blazor aplicativos.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

Quando um Razor componente ( `.razor` ) com uma [ `@page` diretiva](xref:mvc/views/razor#page) é compilado, a classe de componente gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota do componente.

Quando o aplicativo é iniciado, o assembly especificado como o roteador `AppAssembly` é verificado para coletar informações de rota para os componentes do aplicativo que têm um <xref:Microsoft.AspNetCore.Components.RouteAttribute> .

Em tempo de execução, o <xref:Microsoft.AspNetCore.Components.RouteView> componente:

* Recebe o <xref:Microsoft.AspNetCore.Components.RouteData> do <xref:Microsoft.AspNetCore.Components.Routing.Router> junto com quaisquer parâmetros de rota.
* Renderiza o componente especificado com seu [layout](xref:blazor/layouts), incluindo outros layouts aninhados.

Opcionalmente, especifique um <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parâmetro com uma classe de layout para componentes que não especificam um layout com a [ `@layout` diretiva](xref:blazor/layouts#specify-a-layout-in-a-component). Os modelos de Blazor projeto da estrutura especificam o `MainLayout` componente ( `Shared/MainLayout.razor` ) como o layout padrão do aplicativo. Para obter mais informações sobre layouts, consulte <xref:blazor/layouts> .

Os componentes dão suporte a vários modelos de rota usando várias [ `@page` diretivas](xref:mvc/views/razor#page). O componente de exemplo a seguir carrega em solicitações para `/BlazorRoute` e `/DifferentBlazorRoute` .

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma `<base>` marca em seu `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ) com o caminho base do aplicativo especificado no `href` atributo. Para obter mais informações, consulte <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fornecer conteúdo personalizado quando o conteúdo não for encontrado

O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.

No `App` componente, defina conteúdo personalizado no <xref:Microsoft.AspNetCore.Components.Routing.Router> modelo do componente <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

Há suporte para itens arbitrários como conteúdo das `<NotFound>` marcas, como outros componentes interativos. Para aplicar um layout padrão ao <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, consulte <xref:blazor/layouts#default-layout> .

## <a name="route-to-components-from-multiple-assemblies"></a>Rotear para componentes de vários assemblies

Use o <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parâmetro para especificar assemblies adicionais para o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente a ser considerado ao procurar componentes roteáveis. Assemblies adicionais são verificados além do assembly especificado para `AppAssembly` . No exemplo a seguir, `Component1` é um componente roteável definido em uma [biblioteca de classes de componente](xref:blazor/components/class-libraries)referenciada. O exemplo a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> resulta no suporte de roteamento para `Component1` .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a>Parâmetros de rota

O roteador usa parâmetros de rota para popular os [parâmetros de componente](xref:blazor/components/index#component-parameters) correspondentes com o mesmo nome. Os nomes de parâmetros de rota não diferenciam maiúsculas de minúsculas. No exemplo a seguir, o `text` parâmetro atribui o valor do segmento de rota à propriedade do componente `Text` . Quando uma solicitação é feita para `/RouteParameter/amazing` o, o `<h1>` conteúdo da marca é renderizado como `Blazor is amazing!` .

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

Há suporte para parâmetros opcionais. No exemplo a seguir, o `text` parâmetro opcional atribui o valor do segmento de rota à propriedade do componente `Text` . Se o segmento não estiver presente, o valor de `Text` será definido como `fantastic` .

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Não há suporte para parâmetros opcionais. No exemplo a seguir, duas [ `@page` diretivas](xref:mvc/views/razor#page) são aplicadas. A primeira diretiva permite a navegação para o componente sem um parâmetro. A segunda diretiva atribui o valor do `{text}` parâmetro de rota à propriedade do componente `Text` .

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) em vez de [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) para permitir a navegação do aplicativo para o mesmo componente com um valor de parâmetro opcional diferente. Com base no exemplo anterior, use `OnParametersSet` quando o usuário deve ser capaz de navegar de `/RouteParameter` para `/RouteParameter/amazing` ou de `/RouteParameter/amazing` `/RouteParameter` :

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>Restrições de rota

Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.

No exemplo a seguir, a rota para o `User` componente só corresponde se:

* Um `Id` segmento de rota está presente na URL da solicitação.
* O `Id` segmento é um tipo inteiro ( `int` ).

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

As restrições de rota mostradas na tabela a seguir estão disponíveis. Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.

| Constraint | Exemplo           | Correspondências de exemplo                                                                  | Constante<br>culture<br>correspondência |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Não                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sim                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sim                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sim                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sim                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Não                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sim                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sim                              |

> [!WARNING]
> As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou <xref:System.DateTime>) sempre usam a cultura invariável. Essas restrições consideram que a URL não é localizável.

## <a name="routing-with-urls-that-contain-dots"></a>Roteamento com URLs que contêm pontos

Para hospedado Blazor WebAssembly e Blazor Server aplicativos, o modelo de rota padrão do lado do servidor pressupõe que se o último segmento de uma URL de solicitação contiver um ponto ( `.` ) que um arquivo é solicitado. Por exemplo, a URL `https://localhost.com:5001/example/some.thing` é interpretada pelo roteador como uma solicitação de um arquivo chamado `some.thing` . Sem configuração adicional, um aplicativo retornará uma resposta *404-não encontrada* se `some.thing` o tiver sido direcionado para rotear para um componente com uma [ `@page` diretiva](xref:mvc/views/razor#page) e `some.thing` for um valor de parâmetro de rota. Para usar uma rota com um ou mais parâmetros que contêm um ponto, o aplicativo deve configurar a rota com um modelo personalizado.

Considere o seguinte `Example` componente que pode receber um parâmetro de rota do último segmento da URL.

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

Para permitir que o *`Server`* aplicativo de uma Blazor WebAssembly solução hospedada encaminhe a solicitação com um ponto no `param` parâmetro de rota, adicione um modelo de rota de arquivo de fallback com o parâmetro opcional no `Startup.Configure` .

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

Para configurar um Blazor Server aplicativo para rotear a solicitação com um ponto no `param` parâmetro de rota, adicione um modelo de rota de página de fallback com o parâmetro opcional em `Startup.Configure` .

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

Para obter mais informações, consulte <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Capturar todos os parâmetros de rota

::: moniker range=">= aspnetcore-5.0"

Catch-todos os parâmetros de rota, que capturam caminhos entre vários limites de pasta, têm suporte em componentes.

Catch-todos os parâmetros de rota são:

* Chamado para corresponder ao nome do segmento de rota. A nomeação não diferencia maiúsculas de minúsculas.
* Um tipo `string`. A estrutura não fornece a conversão automática.
* No final da URL.

`Pages/CatchAll.razor`:

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

Para a URL `/catch-all/this/is/a/test` com um modelo de rota de `/catch-all/{*pageRoute}` , o valor de `PageRoute` é definido como `this/is/a/test` .

As barras e os segmentos do caminho capturado são decodificados. Para um modelo de rota do `/catch-all/{*pageRoute}` , a URL `/catch-all/this/is/a%2Ftest%2A` produz `this/is/a/test*` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Catch-todos os parâmetros de rota têm suporte no ASP.NET Core 5,0 ou posterior. Para obter mais informações, selecione a versão 5,0 deste artigo.

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>Auxiliares de URI e estado de navegação

Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para gerenciar URIs e navegação em código C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> fornece o evento e os métodos mostrados na tabela a seguir.

| Membro | DESCRIÇÃO |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtém o URI absoluto atual. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto. Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde ao `href` atributo no elemento do documento `<base>` em `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navega para o URI especificado. Se `forceLoad` for `true` :<ul><li>O roteamento do lado do cliente é ignorado.</li><li>O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Um evento que é acionado quando o local de navegação é alterado. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converte um URI relativo em um URI absoluto. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dado um URI de base (por exemplo, um URI retornado anteriormente pelo <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte um URI absoluto em um URI relativo ao prefixo de URI de base. |

Para o <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> evento, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fornece as seguintes informações sobre eventos de navegação:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: A URL do novo local.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor interceptou a navegação do navegador. Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> fez com que a navegação ocorresse.

O seguinte componente:

* Navega para o componente do aplicativo `Counter` ( `Pages/Counter.razor` ) quando o botão é selecionado usando <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .
* Manipula o evento de alteração de local assinando <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .
  * O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework. A desconexão do método permite a coleta de lixo do componente.
  * A implementação do agente registra em log as seguintes informações quando o botão é selecionado:

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

## <a name="query-string-and-parse-parameters"></a>Cadeia de caracteres de consulta e parâmetros de análise

A cadeia de caracteres de consulta de uma solicitação é obtida da <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> Propriedade:

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Para analisar os parâmetros de uma cadeia de caracteres de consulta:

* Um aplicativo pode usar a <xref:Microsoft.AspNetCore.WebUtilities> API. Se a API não estiver disponível para o aplicativo, adicione uma referência de pacote no arquivo de projeto do aplicativo para [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Obtenha o valor depois de analisar a cadeia de caracteres de consulta com <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .

O exemplo de componente a seguir `ParseQueryString` analisa uma chave de parâmetro de cadeia de caracteres de consulta chamada `ship` . Por exemplo, o par chave-valor da cadeia de caracteres de consulta de URL `?ship=Tardis` captura o valor `Tardis` em `queryValue` . Para o exemplo a seguir, navegue até o aplicativo com a URL `https://localhost:5001/parse-query-string?ship=Tardis` .

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-component"></a>componente `NavLink`

Use um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente no lugar de elementos de hiperlink HTML ( `<a>` ) ao criar links de navegação. Um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual. A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos. Opcionalmente, atribua um nome de classe CSS ao <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> para aplicar uma classe CSS personalizada ao link renderizado quando a rota atual corresponder ao `href` .

O componente a seguir `NavMenu` cria uma [`Bootstrap`](https://getbootstrap.com/docs/) barra de navegação que demonstra como usar <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componentes:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> O `NavMenu` componente ( `NavMenu.razor` ) é fornecido na `Shared` pasta de um aplicativo gerado a partir dos Blazor modelos de projeto.

Há duas <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando corresponde à URL atual inteira.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*padrão*): o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando ele corresponde a qualquer prefixo da URL atual.

No exemplo anterior, a página inicial <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/` ). O segundo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recebe a `active` classe quando o usuário visita qualquer URL com um `component` prefixo (por exemplo, `https://localhost:5001/component` e `https://localhost:5001/component/another-segment` ).

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Atributos de componente adicionais são passados para a marca de âncora renderizada. No exemplo a seguir, o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente inclui o `target` atributo:

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

A seguinte marcação HTML é renderizada:

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> Devido à maneira que Blazor renderiza conteúdo filho, `NavLink` os componentes de renderização dentro de um `for` loop exigirão uma variável de índice local se a variável de loop de incremento for usada no `NavLink` conteúdo do componente (filho):
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> Usar uma variável de índice nesse cenário é um requisito para **qualquer** componente filho que usa uma variável de loop em seu [conteúdo filho](xref:blazor/components/index#child-content), não apenas o `NavLink` componente.
>
> Como alternativa, use um `foreach` loop com <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a>Integração de roteamento de ponto de extremidade ASP.NET Core

*Esta seção se aplica somente a Blazor Server aplicativos.*

Blazor Server é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing). Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com o <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> no `Startup.Configure` .

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

A configuração típica é rotear todas as solicitações para uma Razor página, que atua como o host para a parte do lado do servidor do Blazor Server aplicativo. Por convenção, a página *host* geralmente é nomeada `_Host.cshtml` na `Pages` pasta do aplicativo.

A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota. A rota de fallback é usada quando outras rotas não correspondem. Isso permite que o aplicativo use outros controladores e páginas sem interferir no roteamento de componentes no Blazor Server aplicativo.

Para obter informações sobre como configurar <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> a hospedagem de servidor de URL não raiz, consulte <xref:blazor/host-and-deploy/index#app-base-path> .
