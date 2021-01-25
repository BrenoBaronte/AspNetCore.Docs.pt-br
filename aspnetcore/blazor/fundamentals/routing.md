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
ms.openlocfilehash: 74886eae7431045e56de535b5221040bd56cdc2d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751562"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="b28b0-103">Roteamento de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b28b0-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="b28b0-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b28b0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b28b0-105">Neste artigo, saiba como gerenciar o roteamento de solicitações e como usar o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente para criar links de navegação em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b28b0-105">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="b28b0-106">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="b28b0-106">Route templates</span></span>

<span data-ttu-id="b28b0-107">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite o roteamento para Razor componentes em um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b28b0-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="b28b0-108">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente é usado no `App` componente de Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b28b0-108">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="b28b0-109">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-109">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

<span data-ttu-id="b28b0-110">Quando um Razor componente ( `.razor` ) com uma [ `@page` diretiva](xref:mvc/views/razor#page) é compilado, a classe de componente gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota do componente.</span><span class="sxs-lookup"><span data-stu-id="b28b0-110">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="b28b0-111">Quando o aplicativo é iniciado, o assembly especificado como o roteador `AppAssembly` é verificado para coletar informações de rota para os componentes do aplicativo que têm um <xref:Microsoft.AspNetCore.Components.RouteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-111">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="b28b0-112">Em tempo de execução, o <xref:Microsoft.AspNetCore.Components.RouteView> componente:</span><span class="sxs-lookup"><span data-stu-id="b28b0-112">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="b28b0-113">Recebe o <xref:Microsoft.AspNetCore.Components.RouteData> do <xref:Microsoft.AspNetCore.Components.Routing.Router> junto com quaisquer parâmetros de rota.</span><span class="sxs-lookup"><span data-stu-id="b28b0-113">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="b28b0-114">Renderiza o componente especificado com seu [layout](xref:blazor/layouts), incluindo outros layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="b28b0-114">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="b28b0-115">Opcionalmente, especifique um <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parâmetro com uma classe de layout para componentes que não especificam um layout com a [ `@layout` diretiva](xref:blazor/layouts#specify-a-layout-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="b28b0-115">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="b28b0-116">Os modelos de Blazor projeto da estrutura especificam o `MainLayout` componente ( `Shared/MainLayout.razor` ) como o layout padrão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b28b0-116">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="b28b0-117">Para obter mais informações sobre layouts, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-117">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="b28b0-118">Os componentes dão suporte a vários modelos de rota usando várias [ `@page` diretivas](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="b28b0-118">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="b28b0-119">O componente de exemplo a seguir carrega em solicitações para `/BlazorRoute` e `/DifferentBlazorRoute` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-119">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="b28b0-120">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-120">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="b28b0-121">Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma `<base>` marca em seu `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ) com o caminho base do aplicativo especificado no `href` atributo.</span><span class="sxs-lookup"><span data-stu-id="b28b0-121">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="b28b0-122">Para obter mais informações, consulte <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="b28b0-122">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="b28b0-123">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="b28b0-123">Provide custom content when content isn't found</span></span>

<span data-ttu-id="b28b0-124">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="b28b0-124">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="b28b0-125">No `App` componente, defina conteúdo personalizado no <xref:Microsoft.AspNetCore.Components.Routing.Router> modelo do componente <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-125">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="b28b0-126">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-126">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="b28b0-127">Há suporte para itens arbitrários como conteúdo das `<NotFound>` marcas, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="b28b0-127">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="b28b0-128">Para aplicar um layout padrão ao <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, consulte <xref:blazor/layouts#default-layout> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-128">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="b28b0-129">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="b28b0-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="b28b0-130">Use o <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parâmetro para especificar assemblies adicionais para o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="b28b0-130">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="b28b0-131">Assemblies adicionais são verificados além do assembly especificado para `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-131">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="b28b0-132">No exemplo a seguir, `Component1` é um componente roteável definido em uma [biblioteca de classes de componente](xref:blazor/components/class-libraries)referenciada.</span><span class="sxs-lookup"><span data-stu-id="b28b0-132">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="b28b0-133">O exemplo a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> resulta no suporte de roteamento para `Component1` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-133">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="b28b0-134">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-134">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="b28b0-135">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="b28b0-135">Route parameters</span></span>

<span data-ttu-id="b28b0-136">O roteador usa parâmetros de rota para popular os [parâmetros de componente](xref:blazor/components/index#component-parameters) correspondentes com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="b28b0-136">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="b28b0-137">Os nomes de parâmetros de rota não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b28b0-137">Route parameter names are case insensitive.</span></span> <span data-ttu-id="b28b0-138">No exemplo a seguir, o `text` parâmetro atribui o valor do segmento de rota à propriedade do componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-138">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b28b0-139">Quando uma solicitação é feita para `/RouteParameter/amazing` o, o `<h1>` conteúdo da marca é renderizado como `Blazor is amazing!` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-139">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="b28b0-140">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-140">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b28b0-141">Há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="b28b0-141">Optional parameters are supported.</span></span> <span data-ttu-id="b28b0-142">No exemplo a seguir, o `text` parâmetro opcional atribui o valor do segmento de rota à propriedade do componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-142">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b28b0-143">Se o segmento não estiver presente, o valor de `Text` será definido como `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-143">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="b28b0-144">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-144">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b28b0-145">Não há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="b28b0-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="b28b0-146">No exemplo a seguir, duas [ `@page` diretivas](xref:mvc/views/razor#page) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="b28b0-146">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="b28b0-147">A primeira diretiva permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b28b0-147">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b28b0-148">A segunda diretiva atribui o valor do `{text}` parâmetro de rota à propriedade do componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-148">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="b28b0-149">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-149">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b28b0-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) em vez de [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) para permitir a navegação do aplicativo para o mesmo componente com um valor de parâmetro opcional diferente.</span><span class="sxs-lookup"><span data-stu-id="b28b0-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="b28b0-151">Com base no exemplo anterior, use `OnParametersSet` quando o usuário deve ser capaz de navegar de `/RouteParameter` para `/RouteParameter/amazing` ou de `/RouteParameter/amazing` `/RouteParameter` :</span><span class="sxs-lookup"><span data-stu-id="b28b0-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="b28b0-152">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="b28b0-152">Route constraints</span></span>

<span data-ttu-id="b28b0-153">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="b28b0-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="b28b0-154">No exemplo a seguir, a rota para o `User` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="b28b0-154">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="b28b0-155">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b28b0-155">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="b28b0-156">O `Id` segmento é um tipo inteiro ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="b28b0-156">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="b28b0-157">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-157">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="b28b0-158">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b28b0-158">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="b28b0-159">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b28b0-159">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="b28b0-160">Constraint</span><span class="sxs-lookup"><span data-stu-id="b28b0-160">Constraint</span></span> | <span data-ttu-id="b28b0-161">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b28b0-161">Example</span></span>           | <span data-ttu-id="b28b0-162">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="b28b0-162">Example Matches</span></span>                                                                  | <span data-ttu-id="b28b0-163">Constante</span><span class="sxs-lookup"><span data-stu-id="b28b0-163">Invariant</span></span><br><span data-ttu-id="b28b0-164">culture</span><span class="sxs-lookup"><span data-stu-id="b28b0-164">culture</span></span><br><span data-ttu-id="b28b0-165">correspondência</span><span class="sxs-lookup"><span data-stu-id="b28b0-165">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="b28b0-166">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="b28b0-166">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="b28b0-167">Não</span><span class="sxs-lookup"><span data-stu-id="b28b0-167">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="b28b0-168">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="b28b0-168">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="b28b0-169">Sim</span><span class="sxs-lookup"><span data-stu-id="b28b0-169">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="b28b0-170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="b28b0-170">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="b28b0-171">Sim</span><span class="sxs-lookup"><span data-stu-id="b28b0-171">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="b28b0-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b28b0-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b28b0-173">Sim</span><span class="sxs-lookup"><span data-stu-id="b28b0-173">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="b28b0-174">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b28b0-174">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b28b0-175">Sim</span><span class="sxs-lookup"><span data-stu-id="b28b0-175">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="b28b0-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="b28b0-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="b28b0-177">Não</span><span class="sxs-lookup"><span data-stu-id="b28b0-177">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="b28b0-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b28b0-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b28b0-179">Sim</span><span class="sxs-lookup"><span data-stu-id="b28b0-179">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="b28b0-180">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b28b0-180">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b28b0-181">Sim</span><span class="sxs-lookup"><span data-stu-id="b28b0-181">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="b28b0-182">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou <xref:System.DateTime>) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="b28b0-182">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="b28b0-183">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="b28b0-183">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="b28b0-184">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="b28b0-184">Routing with URLs that contain dots</span></span>

<span data-ttu-id="b28b0-185">Para hospedado Blazor WebAssembly e Blazor Server aplicativos, o modelo de rota padrão do lado do servidor pressupõe que se o último segmento de uma URL de solicitação contiver um ponto ( `.` ) que um arquivo é solicitado.</span><span class="sxs-lookup"><span data-stu-id="b28b0-185">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="b28b0-186">Por exemplo, a URL `https://localhost.com:5001/example/some.thing` é interpretada pelo roteador como uma solicitação de um arquivo chamado `some.thing` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-186">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="b28b0-187">Sem configuração adicional, um aplicativo retornará uma resposta *404-não encontrada* se `some.thing` o tiver sido direcionado para rotear para um componente com uma [ `@page` diretiva](xref:mvc/views/razor#page) e `some.thing` for um valor de parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="b28b0-187">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="b28b0-188">Para usar uma rota com um ou mais parâmetros que contêm um ponto, o aplicativo deve configurar a rota com um modelo personalizado.</span><span class="sxs-lookup"><span data-stu-id="b28b0-188">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="b28b0-189">Considere o seguinte `Example` componente que pode receber um parâmetro de rota do último segmento da URL.</span><span class="sxs-lookup"><span data-stu-id="b28b0-189">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="b28b0-190">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-190">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b28b0-191">Para permitir que o *`Server`* aplicativo de uma Blazor WebAssembly solução hospedada encaminhe a solicitação com um ponto no `param` parâmetro de rota, adicione um modelo de rota de arquivo de fallback com o parâmetro opcional no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-191">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b28b0-192">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-192">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="b28b0-193">Para configurar um Blazor Server aplicativo para rotear a solicitação com um ponto no `param` parâmetro de rota, adicione um modelo de rota de página de fallback com o parâmetro opcional em `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-193">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b28b0-194">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-194">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="b28b0-195">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b28b0-195">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="b28b0-196">Capturar todos os parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="b28b0-196">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b28b0-197">Catch-todos os parâmetros de rota, que capturam caminhos entre vários limites de pasta, têm suporte em componentes.</span><span class="sxs-lookup"><span data-stu-id="b28b0-197">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="b28b0-198">Catch-todos os parâmetros de rota são:</span><span class="sxs-lookup"><span data-stu-id="b28b0-198">Catch-all route parameters are:</span></span>

* <span data-ttu-id="b28b0-199">Chamado para corresponder ao nome do segmento de rota.</span><span class="sxs-lookup"><span data-stu-id="b28b0-199">Named to match the route segment name.</span></span> <span data-ttu-id="b28b0-200">A nomeação não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b28b0-200">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="b28b0-201">Um tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="b28b0-201">A `string` type.</span></span> <span data-ttu-id="b28b0-202">A estrutura não fornece a conversão automática.</span><span class="sxs-lookup"><span data-stu-id="b28b0-202">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="b28b0-203">No final da URL.</span><span class="sxs-lookup"><span data-stu-id="b28b0-203">At the end of the URL.</span></span>

<span data-ttu-id="b28b0-204">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-204">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

<span data-ttu-id="b28b0-205">Para a URL `/catch-all/this/is/a/test` com um modelo de rota de `/catch-all/{*pageRoute}` , o valor de `PageRoute` é definido como `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-205">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="b28b0-206">As barras e os segmentos do caminho capturado são decodificados.</span><span class="sxs-lookup"><span data-stu-id="b28b0-206">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="b28b0-207">Para um modelo de rota do `/catch-all/{*pageRoute}` , a URL `/catch-all/this/is/a%2Ftest%2A` produz `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-207">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b28b0-208">Catch-todos os parâmetros de rota têm suporte no ASP.NET Core 5,0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="b28b0-208">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="b28b0-209">Para obter mais informações, selecione a versão 5,0 deste artigo.</span><span class="sxs-lookup"><span data-stu-id="b28b0-209">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="b28b0-210">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="b28b0-210">URI and navigation state helpers</span></span>

<span data-ttu-id="b28b0-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para gerenciar URIs e navegação em código C#.</span><span class="sxs-lookup"><span data-stu-id="b28b0-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="b28b0-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="b28b0-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="b28b0-213">Membro</span><span class="sxs-lookup"><span data-stu-id="b28b0-213">Member</span></span> | <span data-ttu-id="b28b0-214">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="b28b0-214">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="b28b0-215">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="b28b0-215">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="b28b0-216">Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="b28b0-216">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="b28b0-217">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde ao `href` atributo no elemento do documento `<base>` em `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="b28b0-217">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="b28b0-218">Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="b28b0-218">Navigates to the specified URI.</span></span> <span data-ttu-id="b28b0-219">Se `forceLoad` for `true` :</span><span class="sxs-lookup"><span data-stu-id="b28b0-219">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="b28b0-220">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="b28b0-220">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="b28b0-221">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b28b0-221">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="b28b0-222">Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="b28b0-222">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="b28b0-223">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="b28b0-223">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="b28b0-224">Dado um URI de base (por exemplo, um URI retornado anteriormente pelo <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="b28b0-224">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="b28b0-225">Para o <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> evento, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fornece as seguintes informações sobre eventos de navegação:</span><span class="sxs-lookup"><span data-stu-id="b28b0-225">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="b28b0-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: A URL do novo local.</span><span class="sxs-lookup"><span data-stu-id="b28b0-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="b28b0-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor interceptou a navegação do navegador.</span><span class="sxs-lookup"><span data-stu-id="b28b0-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="b28b0-228">Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> fez com que a navegação ocorresse.</span><span class="sxs-lookup"><span data-stu-id="b28b0-228">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="b28b0-229">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="b28b0-229">The following component:</span></span>

* <span data-ttu-id="b28b0-230">Navega para o componente do aplicativo `Counter` ( `Pages/Counter.razor` ) quando o botão é selecionado usando <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-230">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="b28b0-231">Manipula o evento de alteração de local assinando <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-231">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="b28b0-232">O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework.</span><span class="sxs-lookup"><span data-stu-id="b28b0-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="b28b0-233">A desconexão do método permite a coleta de lixo do componente.</span><span class="sxs-lookup"><span data-stu-id="b28b0-233">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="b28b0-234">A implementação do agente registra em log as seguintes informações quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="b28b0-234">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="b28b0-235">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-235">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

<span data-ttu-id="b28b0-236">Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-236">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="b28b0-237">Cadeia de caracteres de consulta e parâmetros de análise</span><span class="sxs-lookup"><span data-stu-id="b28b0-237">Query string and parse parameters</span></span>

<span data-ttu-id="b28b0-238">A cadeia de caracteres de consulta de uma solicitação é obtida da <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="b28b0-238">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="b28b0-239">Para analisar os parâmetros de uma cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="b28b0-239">To parse a query string's parameters:</span></span>

* <span data-ttu-id="b28b0-240">Um aplicativo pode usar a <xref:Microsoft.AspNetCore.WebUtilities> API.</span><span class="sxs-lookup"><span data-stu-id="b28b0-240">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="b28b0-241">Se a API não estiver disponível para o aplicativo, adicione uma referência de pacote no arquivo de projeto do aplicativo para [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="b28b0-241">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="b28b0-242">Obtenha o valor depois de analisar a cadeia de caracteres de consulta com <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-242">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b28b0-243">O exemplo de componente a seguir `ParseQueryString` analisa uma chave de parâmetro de cadeia de caracteres de consulta chamada `ship` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-243">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="b28b0-244">Por exemplo, o par chave-valor da cadeia de caracteres de consulta de URL `?ship=Tardis` captura o valor `Tardis` em `queryValue` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-244">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="b28b0-245">Para o exemplo a seguir, navegue até o aplicativo com a URL `https://localhost:5001/parse-query-string?ship=Tardis` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-245">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="b28b0-246">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-246">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="b28b0-247">`NavLink` e `NavMenu` componentes do</span><span class="sxs-lookup"><span data-stu-id="b28b0-247">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="b28b0-248">Use um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente no lugar de elementos de hiperlink HTML ( `<a>` ) ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="b28b0-248">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="b28b0-249">Um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="b28b0-249">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="b28b0-250">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="b28b0-250">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="b28b0-251">Opcionalmente, atribua um nome de classe CSS ao <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> para aplicar uma classe CSS personalizada ao link renderizado quando a rota atual corresponder ao `href` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-251">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="b28b0-252">O componente a seguir `NavMenu` cria uma [`Bootstrap`](https://getbootstrap.com/docs/) barra de navegação que demonstra como usar <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componentes:</span><span class="sxs-lookup"><span data-stu-id="b28b0-252">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b28b0-253">O `NavMenu` componente ( `NavMenu.razor` ) é fornecido na `Shared` pasta de um aplicativo gerado a partir dos Blazor modelos de projeto.</span><span class="sxs-lookup"><span data-stu-id="b28b0-253">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="b28b0-254">Há duas <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="b28b0-254">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="b28b0-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="b28b0-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="b28b0-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*padrão*): o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="b28b0-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="b28b0-257">No exemplo anterior, a página inicial <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="b28b0-257">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="b28b0-258">O segundo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recebe a `active` classe quando o usuário visita qualquer URL com um `component` prefixo (por exemplo, `https://localhost:5001/component` e `https://localhost:5001/component/another-segment` ).</span><span class="sxs-lookup"><span data-stu-id="b28b0-258">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="b28b0-259"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Atributos de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="b28b0-259">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="b28b0-260">No exemplo a seguir, o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="b28b0-260">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="b28b0-261">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="b28b0-261">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="b28b0-262">Devido à maneira que Blazor renderiza conteúdo filho, `NavLink` os componentes de renderização dentro de um `for` loop exigirão uma variável de índice local se a variável de loop de incremento for usada no `NavLink` conteúdo do componente (filho):</span><span class="sxs-lookup"><span data-stu-id="b28b0-262">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="b28b0-263">Usar uma variável de índice nesse cenário é um requisito para **qualquer** componente filho que usa uma variável de loop em seu [conteúdo filho](xref:blazor/components/index#child-content), não apenas o `NavLink` componente.</span><span class="sxs-lookup"><span data-stu-id="b28b0-263">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="b28b0-264">Como alternativa, use um `foreach` loop com <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="b28b0-264">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="b28b0-265">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b28b0-265">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="b28b0-266">*Esta seção se aplica somente a Blazor Server aplicativos.*</span><span class="sxs-lookup"><span data-stu-id="b28b0-266">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="b28b0-267">Blazor Server é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="b28b0-267">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="b28b0-268">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com o <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b28b0-268">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="b28b0-269">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b28b0-269">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="b28b0-270">A configuração típica é rotear todas as solicitações para uma Razor página, que atua como o host para a parte do lado do servidor do Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b28b0-270">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="b28b0-271">Por convenção, a página *host* geralmente é nomeada `_Host.cshtml` na `Pages` pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b28b0-271">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="b28b0-272">A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota.</span><span class="sxs-lookup"><span data-stu-id="b28b0-272">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="b28b0-273">A rota de fallback é usada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="b28b0-273">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="b28b0-274">Isso permite que o aplicativo use outros controladores e páginas sem interferir no roteamento de componentes no Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b28b0-274">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="b28b0-275">Para obter informações sobre como configurar <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> a hospedagem de servidor de URL não raiz, consulte <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="b28b0-275">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
