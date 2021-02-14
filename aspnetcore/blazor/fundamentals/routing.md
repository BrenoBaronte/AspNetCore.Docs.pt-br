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
ms.openlocfilehash: 55e2cbc01af7352facad7121c05c754e9d438ae3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279885"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="b12bc-103">Roteamento de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b12bc-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="b12bc-104">Neste artigo, saiba como gerenciar o roteamento de solicitações e como usar o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente para criar links de navegação em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b12bc-104">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="b12bc-105">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="b12bc-105">Route templates</span></span>

<span data-ttu-id="b12bc-106">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite o roteamento para Razor componentes em um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b12bc-106">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="b12bc-107">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente é usado no `App` componente de Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b12bc-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="b12bc-108">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-108">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

<span data-ttu-id="b12bc-109">Quando um Razor componente ( `.razor` ) com uma [ `@page` diretiva](xref:mvc/views/razor#page) é compilado, a classe de componente gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota do componente.</span><span class="sxs-lookup"><span data-stu-id="b12bc-109">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="b12bc-110">Quando o aplicativo é iniciado, o assembly especificado como o roteador `AppAssembly` é verificado para coletar informações de rota para os componentes do aplicativo que têm um <xref:Microsoft.AspNetCore.Components.RouteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-110">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="b12bc-111">Em tempo de execução, o <xref:Microsoft.AspNetCore.Components.RouteView> componente:</span><span class="sxs-lookup"><span data-stu-id="b12bc-111">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="b12bc-112">Recebe o <xref:Microsoft.AspNetCore.Components.RouteData> do <xref:Microsoft.AspNetCore.Components.Routing.Router> junto com quaisquer parâmetros de rota.</span><span class="sxs-lookup"><span data-stu-id="b12bc-112">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="b12bc-113">Renderiza o componente especificado com seu [layout](xref:blazor/layouts), incluindo outros layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="b12bc-113">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="b12bc-114">Opcionalmente, especifique um <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parâmetro com uma classe de layout para componentes que não especificam um layout com a [ `@layout` diretiva](xref:blazor/layouts#specify-a-layout-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="b12bc-114">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="b12bc-115">Os modelos de Blazor projeto da estrutura especificam o `MainLayout` componente ( `Shared/MainLayout.razor` ) como o layout padrão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b12bc-115">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="b12bc-116">Para obter mais informações sobre layouts, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-116">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="b12bc-117">Os componentes dão suporte a vários modelos de rota usando várias [ `@page` diretivas](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="b12bc-117">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="b12bc-118">O componente de exemplo a seguir carrega em solicitações para `/BlazorRoute` e `/DifferentBlazorRoute` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-118">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="b12bc-119">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-119">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="b12bc-120">Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma `<base>` marca em seu `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ) com o caminho base do aplicativo especificado no `href` atributo.</span><span class="sxs-lookup"><span data-stu-id="b12bc-120">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="b12bc-121">Para obter mais informações, consulte <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="b12bc-121">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="b12bc-122">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="b12bc-122">Provide custom content when content isn't found</span></span>

<span data-ttu-id="b12bc-123">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="b12bc-123">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="b12bc-124">No `App` componente, defina conteúdo personalizado no <xref:Microsoft.AspNetCore.Components.Routing.Router> modelo do componente <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-124">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="b12bc-125">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-125">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="b12bc-126">Há suporte para itens arbitrários como conteúdo das `<NotFound>` marcas, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="b12bc-126">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="b12bc-127">Para aplicar um layout padrão ao <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, consulte <xref:blazor/layouts#default-layout> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-127">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="b12bc-128">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="b12bc-128">Route to components from multiple assemblies</span></span>

<span data-ttu-id="b12bc-129">Use o <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parâmetro para especificar assemblies adicionais para o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="b12bc-129">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="b12bc-130">Assemblies adicionais são verificados além do assembly especificado para `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-130">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="b12bc-131">No exemplo a seguir, `Component1` é um componente roteável definido em uma [biblioteca de classes de componente](xref:blazor/components/class-libraries)referenciada.</span><span class="sxs-lookup"><span data-stu-id="b12bc-131">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="b12bc-132">O exemplo a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> resulta no suporte de roteamento para `Component1` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-132">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="b12bc-133">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-133">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="b12bc-134">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="b12bc-134">Route parameters</span></span>

<span data-ttu-id="b12bc-135">O roteador usa parâmetros de rota para popular os [parâmetros de componente](xref:blazor/components/index#component-parameters) correspondentes com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="b12bc-135">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="b12bc-136">Os nomes de parâmetros de rota não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b12bc-136">Route parameter names are case insensitive.</span></span> <span data-ttu-id="b12bc-137">No exemplo a seguir, o `text` parâmetro atribui o valor do segmento de rota à propriedade do componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-137">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b12bc-138">Quando uma solicitação é feita para `/RouteParameter/amazing` o, o `<h1>` conteúdo da marca é renderizado como `Blazor is amazing!` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-138">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="b12bc-139">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-139">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b12bc-140">Há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="b12bc-140">Optional parameters are supported.</span></span> <span data-ttu-id="b12bc-141">No exemplo a seguir, o `text` parâmetro opcional atribui o valor do segmento de rota à propriedade do componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-141">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b12bc-142">Se o segmento não estiver presente, o valor de `Text` será definido como `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-142">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="b12bc-143">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-143">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b12bc-144">Não há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="b12bc-144">Optional parameters aren't supported.</span></span> <span data-ttu-id="b12bc-145">No exemplo a seguir, duas [ `@page` diretivas](xref:mvc/views/razor#page) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="b12bc-145">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="b12bc-146">A primeira diretiva permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b12bc-146">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b12bc-147">A segunda diretiva atribui o valor do `{text}` parâmetro de rota à propriedade do componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-147">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="b12bc-148">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-148">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b12bc-149">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) em vez de [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) para permitir a navegação do aplicativo para o mesmo componente com um valor de parâmetro opcional diferente.</span><span class="sxs-lookup"><span data-stu-id="b12bc-149">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="b12bc-150">Com base no exemplo anterior, use `OnParametersSet` quando o usuário deve ser capaz de navegar de `/RouteParameter` para `/RouteParameter/amazing` ou de `/RouteParameter/amazing` `/RouteParameter` :</span><span class="sxs-lookup"><span data-stu-id="b12bc-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="b12bc-151">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="b12bc-151">Route constraints</span></span>

<span data-ttu-id="b12bc-152">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="b12bc-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="b12bc-153">No exemplo a seguir, a rota para o `User` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="b12bc-153">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="b12bc-154">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b12bc-154">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="b12bc-155">O `Id` segmento é um tipo inteiro ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="b12bc-155">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="b12bc-156">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-156">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="b12bc-157">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b12bc-157">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="b12bc-158">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b12bc-158">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="b12bc-159">Constraint</span><span class="sxs-lookup"><span data-stu-id="b12bc-159">Constraint</span></span> | <span data-ttu-id="b12bc-160">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b12bc-160">Example</span></span>           | <span data-ttu-id="b12bc-161">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="b12bc-161">Example Matches</span></span>                                                                  | <span data-ttu-id="b12bc-162">Constante</span><span class="sxs-lookup"><span data-stu-id="b12bc-162">Invariant</span></span><br><span data-ttu-id="b12bc-163">culture</span><span class="sxs-lookup"><span data-stu-id="b12bc-163">culture</span></span><br><span data-ttu-id="b12bc-164">correspondência</span><span class="sxs-lookup"><span data-stu-id="b12bc-164">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="b12bc-165">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="b12bc-165">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="b12bc-166">Não</span><span class="sxs-lookup"><span data-stu-id="b12bc-166">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="b12bc-167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="b12bc-167">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="b12bc-168">Sim</span><span class="sxs-lookup"><span data-stu-id="b12bc-168">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="b12bc-169">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="b12bc-169">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="b12bc-170">Sim</span><span class="sxs-lookup"><span data-stu-id="b12bc-170">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="b12bc-171">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b12bc-171">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b12bc-172">Sim</span><span class="sxs-lookup"><span data-stu-id="b12bc-172">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="b12bc-173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b12bc-173">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b12bc-174">Sim</span><span class="sxs-lookup"><span data-stu-id="b12bc-174">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="b12bc-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="b12bc-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="b12bc-176">Não</span><span class="sxs-lookup"><span data-stu-id="b12bc-176">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="b12bc-177">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b12bc-177">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b12bc-178">Sim</span><span class="sxs-lookup"><span data-stu-id="b12bc-178">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="b12bc-179">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b12bc-179">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b12bc-180">Sim</span><span class="sxs-lookup"><span data-stu-id="b12bc-180">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="b12bc-181">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou <xref:System.DateTime>) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="b12bc-181">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="b12bc-182">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="b12bc-182">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="b12bc-183">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="b12bc-183">Routing with URLs that contain dots</span></span>

<span data-ttu-id="b12bc-184">Para hospedado Blazor WebAssembly e Blazor Server aplicativos, o modelo de rota padrão do lado do servidor pressupõe que se o último segmento de uma URL de solicitação contiver um ponto ( `.` ) que um arquivo é solicitado.</span><span class="sxs-lookup"><span data-stu-id="b12bc-184">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="b12bc-185">Por exemplo, a URL `https://localhost.com:5001/example/some.thing` é interpretada pelo roteador como uma solicitação de um arquivo chamado `some.thing` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-185">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="b12bc-186">Sem configuração adicional, um aplicativo retornará uma resposta *404-não encontrada* se `some.thing` o tiver sido direcionado para rotear para um componente com uma [ `@page` diretiva](xref:mvc/views/razor#page) e `some.thing` for um valor de parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="b12bc-186">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="b12bc-187">Para usar uma rota com um ou mais parâmetros que contêm um ponto, o aplicativo deve configurar a rota com um modelo personalizado.</span><span class="sxs-lookup"><span data-stu-id="b12bc-187">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="b12bc-188">Considere o seguinte `Example` componente que pode receber um parâmetro de rota do último segmento da URL.</span><span class="sxs-lookup"><span data-stu-id="b12bc-188">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="b12bc-189">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-189">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b12bc-190">Para permitir que o *`Server`* aplicativo de uma Blazor WebAssembly solução hospedada encaminhe a solicitação com um ponto no `param` parâmetro de rota, adicione um modelo de rota de arquivo de fallback com o parâmetro opcional no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-190">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b12bc-191">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-191">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="b12bc-192">Para configurar um Blazor Server aplicativo para rotear a solicitação com um ponto no `param` parâmetro de rota, adicione um modelo de rota de página de fallback com o parâmetro opcional em `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-192">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b12bc-193">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-193">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="b12bc-194">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b12bc-194">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="b12bc-195">Capturar todos os parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="b12bc-195">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b12bc-196">Catch-todos os parâmetros de rota, que capturam caminhos entre vários limites de pasta, têm suporte em componentes.</span><span class="sxs-lookup"><span data-stu-id="b12bc-196">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="b12bc-197">Catch-todos os parâmetros de rota são:</span><span class="sxs-lookup"><span data-stu-id="b12bc-197">Catch-all route parameters are:</span></span>

* <span data-ttu-id="b12bc-198">Chamado para corresponder ao nome do segmento de rota.</span><span class="sxs-lookup"><span data-stu-id="b12bc-198">Named to match the route segment name.</span></span> <span data-ttu-id="b12bc-199">A nomeação não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b12bc-199">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="b12bc-200">Um tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="b12bc-200">A `string` type.</span></span> <span data-ttu-id="b12bc-201">A estrutura não fornece a conversão automática.</span><span class="sxs-lookup"><span data-stu-id="b12bc-201">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="b12bc-202">No final da URL.</span><span class="sxs-lookup"><span data-stu-id="b12bc-202">At the end of the URL.</span></span>

<span data-ttu-id="b12bc-203">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-203">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

<span data-ttu-id="b12bc-204">Para a URL `/catch-all/this/is/a/test` com um modelo de rota de `/catch-all/{*pageRoute}` , o valor de `PageRoute` é definido como `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-204">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="b12bc-205">As barras e os segmentos do caminho capturado são decodificados.</span><span class="sxs-lookup"><span data-stu-id="b12bc-205">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="b12bc-206">Para um modelo de rota do `/catch-all/{*pageRoute}` , a URL `/catch-all/this/is/a%2Ftest%2A` produz `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-206">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b12bc-207">Catch-todos os parâmetros de rota têm suporte no ASP.NET Core 5,0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="b12bc-207">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="b12bc-208">Para obter mais informações, selecione a versão 5,0 deste artigo.</span><span class="sxs-lookup"><span data-stu-id="b12bc-208">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="b12bc-209">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="b12bc-209">URI and navigation state helpers</span></span>

<span data-ttu-id="b12bc-210">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para gerenciar URIs e navegação em código C#.</span><span class="sxs-lookup"><span data-stu-id="b12bc-210">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="b12bc-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="b12bc-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="b12bc-212">Membro</span><span class="sxs-lookup"><span data-stu-id="b12bc-212">Member</span></span> | <span data-ttu-id="b12bc-213">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="b12bc-213">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="b12bc-214">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="b12bc-214">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="b12bc-215">Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="b12bc-215">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="b12bc-216">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde ao `href` atributo no elemento do documento `<base>` em `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="b12bc-216">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="b12bc-217">Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="b12bc-217">Navigates to the specified URI.</span></span> <span data-ttu-id="b12bc-218">Se `forceLoad` for `true` :</span><span class="sxs-lookup"><span data-stu-id="b12bc-218">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="b12bc-219">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="b12bc-219">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="b12bc-220">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b12bc-220">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="b12bc-221">Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="b12bc-221">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="b12bc-222">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="b12bc-222">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="b12bc-223">Dado um URI de base (por exemplo, um URI retornado anteriormente pelo <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="b12bc-223">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="b12bc-224">Para o <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> evento, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fornece as seguintes informações sobre eventos de navegação:</span><span class="sxs-lookup"><span data-stu-id="b12bc-224">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="b12bc-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: A URL do novo local.</span><span class="sxs-lookup"><span data-stu-id="b12bc-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="b12bc-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor interceptou a navegação do navegador.</span><span class="sxs-lookup"><span data-stu-id="b12bc-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="b12bc-227">Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> fez com que a navegação ocorresse.</span><span class="sxs-lookup"><span data-stu-id="b12bc-227">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="b12bc-228">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="b12bc-228">The following component:</span></span>

* <span data-ttu-id="b12bc-229">Navega para o componente do aplicativo `Counter` ( `Pages/Counter.razor` ) quando o botão é selecionado usando <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-229">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="b12bc-230">Manipula o evento de alteração de local assinando <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-230">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="b12bc-231">O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework.</span><span class="sxs-lookup"><span data-stu-id="b12bc-231">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="b12bc-232">A desconexão do método permite a coleta de lixo do componente.</span><span class="sxs-lookup"><span data-stu-id="b12bc-232">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="b12bc-233">A implementação do agente registra em log as seguintes informações quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="b12bc-233">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="b12bc-234">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-234">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

<span data-ttu-id="b12bc-235">Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-235">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="b12bc-236">Cadeia de caracteres de consulta e parâmetros de análise</span><span class="sxs-lookup"><span data-stu-id="b12bc-236">Query string and parse parameters</span></span>

<span data-ttu-id="b12bc-237">A cadeia de caracteres de consulta de uma solicitação é obtida da <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="b12bc-237">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="b12bc-238">Para analisar os parâmetros de uma cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="b12bc-238">To parse a query string's parameters:</span></span>

* <span data-ttu-id="b12bc-239">Um aplicativo pode usar a <xref:Microsoft.AspNetCore.WebUtilities> API.</span><span class="sxs-lookup"><span data-stu-id="b12bc-239">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="b12bc-240">Se a API não estiver disponível para o aplicativo, adicione uma referência de pacote no arquivo de projeto do aplicativo para [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="b12bc-240">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="b12bc-241">Obtenha o valor depois de analisar a cadeia de caracteres de consulta com <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-241">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b12bc-242">O exemplo de componente a seguir `ParseQueryString` analisa uma chave de parâmetro de cadeia de caracteres de consulta chamada `ship` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-242">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="b12bc-243">Por exemplo, o par chave-valor da cadeia de caracteres de consulta de URL `?ship=Tardis` captura o valor `Tardis` em `queryValue` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-243">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="b12bc-244">Para o exemplo a seguir, navegue até o aplicativo com a URL `https://localhost:5001/parse-query-string?ship=Tardis` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-244">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="b12bc-245">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-245">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="b12bc-246">`NavLink` e `NavMenu` componentes do</span><span class="sxs-lookup"><span data-stu-id="b12bc-246">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="b12bc-247">Use um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente no lugar de elementos de hiperlink HTML ( `<a>` ) ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="b12bc-247">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="b12bc-248">Um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="b12bc-248">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="b12bc-249">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="b12bc-249">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="b12bc-250">Opcionalmente, atribua um nome de classe CSS ao <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> para aplicar uma classe CSS personalizada ao link renderizado quando a rota atual corresponder ao `href` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-250">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="b12bc-251">O componente a seguir `NavMenu` cria uma [`Bootstrap`](https://getbootstrap.com/docs/) barra de navegação que demonstra como usar <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componentes:</span><span class="sxs-lookup"><span data-stu-id="b12bc-251">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b12bc-252">O `NavMenu` componente ( `NavMenu.razor` ) é fornecido na `Shared` pasta de um aplicativo gerado a partir dos Blazor modelos de projeto.</span><span class="sxs-lookup"><span data-stu-id="b12bc-252">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="b12bc-253">Há duas <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="b12bc-253">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="b12bc-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="b12bc-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="b12bc-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*padrão*): o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="b12bc-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="b12bc-256">No exemplo anterior, a página inicial <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="b12bc-256">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="b12bc-257">O segundo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recebe a `active` classe quando o usuário visita qualquer URL com um `component` prefixo (por exemplo, `https://localhost:5001/component` e `https://localhost:5001/component/another-segment` ).</span><span class="sxs-lookup"><span data-stu-id="b12bc-257">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="b12bc-258"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Atributos de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="b12bc-258">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="b12bc-259">No exemplo a seguir, o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="b12bc-259">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="b12bc-260">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="b12bc-260">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="b12bc-261">Devido à maneira que Blazor renderiza conteúdo filho, `NavLink` os componentes de renderização dentro de um `for` loop exigirão uma variável de índice local se a variável de loop de incremento for usada no `NavLink` conteúdo do componente (filho):</span><span class="sxs-lookup"><span data-stu-id="b12bc-261">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="b12bc-262">Usar uma variável de índice nesse cenário é um requisito para **qualquer** componente filho que usa uma variável de loop em seu [conteúdo filho](xref:blazor/components/index#child-content), não apenas o `NavLink` componente.</span><span class="sxs-lookup"><span data-stu-id="b12bc-262">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="b12bc-263">Como alternativa, use um `foreach` loop com <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="b12bc-263">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="b12bc-264">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b12bc-264">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="b12bc-265">*Esta seção se aplica somente a Blazor Server aplicativos.*</span><span class="sxs-lookup"><span data-stu-id="b12bc-265">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="b12bc-266">Blazor Server é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="b12bc-266">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="b12bc-267">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com o <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b12bc-267">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="b12bc-268">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b12bc-268">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

<span data-ttu-id="b12bc-269">A configuração típica é rotear todas as solicitações para uma Razor página, que atua como o host para a parte do lado do servidor do Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b12bc-269">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="b12bc-270">Por convenção, a página *host* geralmente é nomeada `_Host.cshtml` na `Pages` pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b12bc-270">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="b12bc-271">A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota.</span><span class="sxs-lookup"><span data-stu-id="b12bc-271">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="b12bc-272">A rota de fallback é usada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="b12bc-272">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="b12bc-273">Isso permite que o aplicativo use outros controladores e páginas sem interferir no roteamento de componentes no Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b12bc-273">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="b12bc-274">Para obter informações sobre como configurar <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> a hospedagem de servidor de URL não raiz, consulte <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="b12bc-274">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
