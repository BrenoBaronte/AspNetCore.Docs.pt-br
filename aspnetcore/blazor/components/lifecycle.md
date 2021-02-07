---
title: BlazorCiclo de vida ASP.NET Core
author: guardrex
description: Saiba como usar Razor métodos de ciclo de vida do componente em ASP.NET Core Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: f19d25006009723a8e69f24af92155f65c2195fe
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804455"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="42651-103">BlazorCiclo de vida ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42651-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="42651-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="42651-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="42651-105">A Blazor estrutura inclui métodos de ciclo de vida síncronos e assíncronos.</span><span class="sxs-lookup"><span data-stu-id="42651-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="42651-106">Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="42651-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="42651-107">Os diagramas a seguir ilustram o Blazor ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="42651-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="42651-108">Os métodos de ciclo de vida são definidos com exemplos nas seções a seguir deste artigo.</span><span class="sxs-lookup"><span data-stu-id="42651-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="42651-109">Eventos de ciclo de vida do componente:</span><span class="sxs-lookup"><span data-stu-id="42651-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="42651-110">Se o componente estiver renderizando pela primeira vez em uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="42651-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="42651-111">Crie a instância do componente.</span><span class="sxs-lookup"><span data-stu-id="42651-111">Create the component's instance.</span></span>
   * <span data-ttu-id="42651-112">Execute a injeção de propriedade.</span><span class="sxs-lookup"><span data-stu-id="42651-112">Perform property injection.</span></span> <span data-ttu-id="42651-113">Executar [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="42651-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="42651-114">Chame [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="42651-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="42651-115">Se um <xref:System.Threading.Tasks.Task> for retornado, o <xref:System.Threading.Tasks.Task> será esperado e o componente será renderizado.</span><span class="sxs-lookup"><span data-stu-id="42651-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="42651-116">Se um <xref:System.Threading.Tasks.Task> não for retornado, o componente será renderizado.</span><span class="sxs-lookup"><span data-stu-id="42651-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="42651-117">Chame [`OnParametersSet{Async}`](#after-parameters-are-set) e processe o componente.</span><span class="sxs-lookup"><span data-stu-id="42651-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="42651-118">Se um <xref:System.Threading.Tasks.Task> for retornado de `OnParametersSetAsync` , o <xref:System.Threading.Tasks.Task> será aguardado e, em seguida, o componente será renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="42651-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Eventos de ciclo de vida do componente de um::: no-Loc (Razor)::: componente em::: no-Loc (mais alto):::](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="42651-120">Processamento de eventos de Modelo de Objeto do Documento (DOM):</span><span class="sxs-lookup"><span data-stu-id="42651-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="42651-121">O manipulador de eventos é executado.</span><span class="sxs-lookup"><span data-stu-id="42651-121">The event handler is run.</span></span>
1. <span data-ttu-id="42651-122">Se um <xref:System.Threading.Tasks.Task> for retornado, o <xref:System.Threading.Tasks.Task> será esperado e, em seguida, o componente será renderizado.</span><span class="sxs-lookup"><span data-stu-id="42651-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="42651-123">Se um <xref:System.Threading.Tasks.Task> não for retornado, o componente será renderizado.</span><span class="sxs-lookup"><span data-stu-id="42651-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Processamento de eventos de Modelo de Objeto do Documento (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="42651-125">O `Render` ciclo de vida:</span><span class="sxs-lookup"><span data-stu-id="42651-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="42651-126">Evite outras operações de renderização no componente:</span><span class="sxs-lookup"><span data-stu-id="42651-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="42651-127">Após o primeiro processamento.</span><span class="sxs-lookup"><span data-stu-id="42651-127">After the first render.</span></span>
   * <span data-ttu-id="42651-128">Quando [`ShouldRender`](#suppress-ui-refreshing) é `false` .</span><span class="sxs-lookup"><span data-stu-id="42651-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="42651-129">Crie a comparação da árvore de renderização (diferença) e processe o componente.</span><span class="sxs-lookup"><span data-stu-id="42651-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="42651-130">Aguardar o DOM para atualizar.</span><span class="sxs-lookup"><span data-stu-id="42651-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="42651-131">Chame [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="42651-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Ciclo de vida de renderização](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="42651-133">Chamadas de desenvolvedor para [`StateHasChanged`](#state-changes) resultar em uma renderização.</span><span class="sxs-lookup"><span data-stu-id="42651-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="42651-134">Para obter mais informações, consulte <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="42651-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="42651-135">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="42651-135">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="42651-136">Antes de os parâmetros serem definidos</span><span class="sxs-lookup"><span data-stu-id="42651-136">Before parameters are set</span></span>

<span data-ttu-id="42651-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> define os parâmetros fornecidos pelo pai do componente na árvore de renderização ou nos parâmetros de rota.</span><span class="sxs-lookup"><span data-stu-id="42651-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="42651-138">Ao substituir o método, o código do desenvolvedor pode interagir diretamente com os <xref:Microsoft.AspNetCore.Components.ParameterView> parâmetros do.</span><span class="sxs-lookup"><span data-stu-id="42651-138">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="42651-139">No exemplo a seguir, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> atribui o `Param` valor do parâmetro para se a `value` análise de um parâmetro de rota para `Param` for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="42651-139">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="42651-140">Quando `value` não estiver `null` , o valor será exibido pelo componente.</span><span class="sxs-lookup"><span data-stu-id="42651-140">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="42651-141">Embora a [correspondência de parâmetro de rota não diferencia maiúsculas de minúsculas](xref:blazor/fundamentals/routing#route-parameters), o <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> corresponde apenas a nomes de parâmetro com diferenciação de maiúsculas e minúsculas</span><span class="sxs-lookup"><span data-stu-id="42651-141">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="42651-142">O exemplo a seguir é necessário para usar `/{Param?}` , não `/{param?}` , para obter o valor.</span><span class="sxs-lookup"><span data-stu-id="42651-142">The following example is required to use `/{Param?}`, not `/{param?}`, in order to get the value.</span></span> <span data-ttu-id="42651-143">Se `/{param?}` é usado neste cenário, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> retorna `false` e `message` não é definido como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="42651-143">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either string.</span></span>

<span data-ttu-id="42651-144">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="42651-144">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="42651-145"><xref:Microsoft.AspNetCore.Components.ParameterView> contém o conjunto de valores de parâmetro para o componente cada vez que <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> é chamado.</span><span class="sxs-lookup"><span data-stu-id="42651-145"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="42651-146">A implementação padrão de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> define o valor de cada propriedade com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo ou que tem um valor correspondente no <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="42651-146">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="42651-147">Os parâmetros que não têm um valor correspondente em <xref:Microsoft.AspNetCore.Components.ParameterView> são deixados inalterados.</span><span class="sxs-lookup"><span data-stu-id="42651-147">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="42651-148">Se [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária.</span><span class="sxs-lookup"><span data-stu-id="42651-148">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="42651-149">Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="42651-149">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="42651-150">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="42651-150">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="42651-151">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="42651-151">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="42651-152">Métodos de inicialização de componente</span><span class="sxs-lookup"><span data-stu-id="42651-152">Component initialization methods</span></span>

<span data-ttu-id="42651-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai no <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="42651-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="42651-154">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="42651-154">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="42651-155">Para uma operação síncrona, substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="42651-155">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="42651-156">Para executar uma operação assíncrona, substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> e use o [`await`](/dotnet/csharp/language-reference/operators/await) operador na operação:</span><span class="sxs-lookup"><span data-stu-id="42651-156">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="42651-157">Blazor Server aplicativos que preparam [sua chamada de conteúdo](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="42651-157">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="42651-158">Uma vez quando o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="42651-158">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="42651-159">Uma segunda vez quando o navegador estabelece uma conexão de volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="42651-159">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="42651-160">Para impedir que o código <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> do desenvolvedor seja executado duas vezes, consulte a seção [reconexão com estado após o pré-processamento](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="42651-160">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="42651-161">Embora um Blazor Server aplicativo esteja sendo renderizado, determinadas ações, como chamar em JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="42651-161">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="42651-162">Os componentes podem precisar ser renderizados de forma diferente quando renderizados.</span><span class="sxs-lookup"><span data-stu-id="42651-162">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="42651-163">Para obter mais informações, consulte a seção [detectar quando o aplicativo está sendo processado](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="42651-163">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="42651-164">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="42651-164">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="42651-165">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="42651-165">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="42651-166">Depois que os parâmetros são definidos</span><span class="sxs-lookup"><span data-stu-id="42651-166">After parameters are set</span></span>

<span data-ttu-id="42651-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> são chamados:</span><span class="sxs-lookup"><span data-stu-id="42651-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="42651-168">Depois que o componente é inicializado no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ou no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="42651-168">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="42651-169">Quando o componente pai é renderizado novamente e fornece:</span><span class="sxs-lookup"><span data-stu-id="42651-169">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="42651-170">Somente tipos irmutáveis de primitivo conhecidos dos quais pelo menos um parâmetro foi alterado.</span><span class="sxs-lookup"><span data-stu-id="42651-170">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="42651-171">Qualquer parâmetro de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="42651-171">Any complex-typed parameters.</span></span> <span data-ttu-id="42651-172">A estrutura não pode saber se os valores de um parâmetro de tipo complexo foram modificados internamente e, portanto, trata o conjunto de parâmetros como alterado.</span><span class="sxs-lookup"><span data-stu-id="42651-172">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="42651-173">O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> evento de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="42651-173">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="42651-174">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="42651-174">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="42651-175">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="42651-175">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="42651-176">Após renderização de componente</span><span class="sxs-lookup"><span data-stu-id="42651-176">After component render</span></span>

<span data-ttu-id="42651-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> são chamados após a conclusão da renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="42651-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="42651-178">Referências de elemento e componente são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="42651-178">Element and component references are populated at this point.</span></span> <span data-ttu-id="42651-179">Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="42651-179">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="42651-180">O `firstRender` parâmetro para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="42651-180">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="42651-181">É definido como `true` a primeira vez que a instância do componente é renderizada.</span><span class="sxs-lookup"><span data-stu-id="42651-181">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="42651-182">Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="42651-182">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="42651-183">O trabalho assíncrono imediatamente após a renderização deve ocorrer durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> evento do ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="42651-183">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="42651-184">Mesmo que você retorne um <xref:System.Threading.Tasks.Task> do <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída.</span><span class="sxs-lookup"><span data-stu-id="42651-184">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="42651-185">Isso é para evitar um loop de renderização infinito.</span><span class="sxs-lookup"><span data-stu-id="42651-185">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="42651-186">Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.</span><span class="sxs-lookup"><span data-stu-id="42651-186">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="42651-187"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *não são chamados durante o processo de pré-processamento no servidor*.</span><span class="sxs-lookup"><span data-stu-id="42651-187"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="42651-188">Os métodos são chamados quando o componente é renderizado interativamente após a conclusão do pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="42651-188">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="42651-189">Quando o aplicativo é renderizado:</span><span class="sxs-lookup"><span data-stu-id="42651-189">When the app prerenders:</span></span>

1. <span data-ttu-id="42651-190">O componente é executado no servidor para produzir alguma marcação HTML estática na resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="42651-190">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="42651-191">Durante essa fase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> não são chamados.</span><span class="sxs-lookup"><span data-stu-id="42651-191">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="42651-192">Quando `blazor.server.js` ou `blazor.webassembly.js` inicia no navegador, o componente é reiniciado em um modo de renderização interativo.</span><span class="sxs-lookup"><span data-stu-id="42651-192">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="42651-193">Depois que um componente é reiniciado, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **são** chamados porque o aplicativo não está dentro da fase de pré-processamento mais.</span><span class="sxs-lookup"><span data-stu-id="42651-193">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="42651-194">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="42651-194">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="42651-195">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="42651-195">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="42651-196">Suprimir atualização da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="42651-196">Suppress UI refreshing</span></span>

<span data-ttu-id="42651-197">Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="42651-197">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="42651-198">Se a implementação retornar `true` , a interface do usuário será atualizada:</span><span class="sxs-lookup"><span data-stu-id="42651-198">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="42651-199"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> é chamado cada vez que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="42651-199"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="42651-200">Mesmo se <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> for substituído, o componente sempre será renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="42651-200">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="42651-201">Para obter mais informações, consulte <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="42651-201">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="42651-202">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="42651-202">State changes</span></span>

<span data-ttu-id="42651-203"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Notifica o componente de que seu estado foi alterado.</span><span class="sxs-lookup"><span data-stu-id="42651-203"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="42651-204">Quando aplicável, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> a chamada faz com que o componente seja rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="42651-204">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="42651-205"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> é chamado automaticamente para <xref:Microsoft.AspNetCore.Components.EventCallback> métodos.</span><span class="sxs-lookup"><span data-stu-id="42651-205"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="42651-206">Para obter mais informações, consulte <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="42651-206">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="42651-207">Para obter mais informações, consulte <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="42651-207">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="42651-208">Tratar ações assíncronas incompletas no processamento</span><span class="sxs-lookup"><span data-stu-id="42651-208">Handle incomplete async actions at render</span></span>

<span data-ttu-id="42651-209">Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="42651-209">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="42651-210">Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução.</span><span class="sxs-lookup"><span data-stu-id="42651-210">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="42651-211">Forneça a lógica de renderização para confirmar que os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="42651-211">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="42651-212">Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto objetos são `null` .</span><span class="sxs-lookup"><span data-stu-id="42651-212">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="42651-213">No `FetchData` componente dos Blazor modelos, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> é substituído para receber dados de previsão de forma assíncrona ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="42651-213">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="42651-214">Quando `forecasts` é `null` , uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="42651-214">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="42651-215">Depois que o `Task` retornado por <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> for concluído, o componente será rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="42651-215">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="42651-216">`Pages/FetchData.razor` no Blazor Server modelo:</span><span class="sxs-lookup"><span data-stu-id="42651-216">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="42651-217">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="42651-217">Handle errors</span></span>

<span data-ttu-id="42651-218">Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="42651-218">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="42651-219">Reconexão com estado após o pré-processamento</span><span class="sxs-lookup"><span data-stu-id="42651-219">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="42651-220">Em um Blazor Server aplicativo quando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> é <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="42651-220">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="42651-221">Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente* e o componente agora é interativo.</span><span class="sxs-lookup"><span data-stu-id="42651-221">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="42651-222">Se o [`OnInitialized{Async}`](#component-initialization-methods) método de ciclo de vida para inicializar o componente estiver presente, o método será executado *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="42651-222">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="42651-223">Quando o componente é renderizado estaticamente.</span><span class="sxs-lookup"><span data-stu-id="42651-223">When the component is prerendered statically.</span></span>
* <span data-ttu-id="42651-224">Depois que a conexão do servidor tiver sido estabelecida.</span><span class="sxs-lookup"><span data-stu-id="42651-224">After the server connection has been established.</span></span>

<span data-ttu-id="42651-225">Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.</span><span class="sxs-lookup"><span data-stu-id="42651-225">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="42651-226">Para evitar o cenário de processamento duplo em um Blazor Server aplicativo:</span><span class="sxs-lookup"><span data-stu-id="42651-226">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="42651-227">Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="42651-227">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="42651-228">Use o identificador durante o pré-processamento para salvar o estado do componente.</span><span class="sxs-lookup"><span data-stu-id="42651-228">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="42651-229">Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="42651-229">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="42651-230">O código a seguir demonstra uma atualização `WeatherForecastService` em um aplicativo baseado em modelo Blazor Server que evita a renderização dupla:</span><span class="sxs-lookup"><span data-stu-id="42651-230">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="42651-231">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , consulte <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="42651-231">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="42651-232">Detectar quando o aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="42651-232">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="42651-233">Descarte de componentes com `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="42651-233">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="42651-234">Se um componente implementa <xref:System.IDisposable> , a estrutura chama o [método de alienação](/dotnet/standard/garbage-collection/implementing-dispose) quando o componente é removido da interface do usuário, onde recursos não gerenciados podem ser liberados.</span><span class="sxs-lookup"><span data-stu-id="42651-234">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="42651-235">A alienação pode ocorrer a qualquer momento, incluindo durante a [inicialização do componente](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="42651-235">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="42651-236">O componente a seguir implementa <xref:System.IDisposable> com a [`@implements`](xref:mvc/views/razor#implements) Razor diretiva:</span><span class="sxs-lookup"><span data-stu-id="42651-236">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

<span data-ttu-id="42651-237">Para tarefas de descarte assíncrono, use `DisposeAsync` em vez de `Dispose` no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="42651-237">For asynchronous disposal tasks, use `DisposeAsync` instead of `Dispose` in the preceding example:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="42651-238">A chamada <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> no `Dispose` não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="42651-238">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="42651-239"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.</span><span class="sxs-lookup"><span data-stu-id="42651-239"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="42651-240">Cancele a assinatura de manipuladores de eventos de eventos .NET.</span><span class="sxs-lookup"><span data-stu-id="42651-240">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="42651-241">Os exemplos de [ Blazor formulário](xref:blazor/forms-validation) a seguir mostram como desvincular um manipulador de eventos no `Dispose` método:</span><span class="sxs-lookup"><span data-stu-id="42651-241">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="42651-242">Campo privado e abordagem lambda</span><span class="sxs-lookup"><span data-stu-id="42651-242">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="42651-243">Abordagem do método privado</span><span class="sxs-lookup"><span data-stu-id="42651-243">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]
  
<span data-ttu-id="42651-244">Para obter mais informações, consulte [limpando recursos não gerenciados](/dotnet/standard/garbage-collection/unmanaged) e os tópicos que o seguem na implementação `Dispose` dos `DisposeAsync` métodos e.</span><span class="sxs-lookup"><span data-stu-id="42651-244">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="42651-245">Trabalho em segundo plano cancelável</span><span class="sxs-lookup"><span data-stu-id="42651-245">Cancelable background work</span></span>

<span data-ttu-id="42651-246">Os componentes geralmente executam trabalho em segundo plano de longa execução, como fazer chamadas de rede ( <xref:System.Net.Http.HttpClient> ) e interagir com bancos de dados.</span><span class="sxs-lookup"><span data-stu-id="42651-246">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="42651-247">É desejável interromper o trabalho em segundo plano para conservar recursos do sistema em várias situações.</span><span class="sxs-lookup"><span data-stu-id="42651-247">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="42651-248">Por exemplo, as operações assíncronas em segundo plano não são interrompidas automaticamente quando um usuário sai de um componente.</span><span class="sxs-lookup"><span data-stu-id="42651-248">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="42651-249">Outros motivos pelos quais os itens de trabalho em segundo plano podem exigir cancelamento incluem:</span><span class="sxs-lookup"><span data-stu-id="42651-249">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="42651-250">Uma tarefa em execução em segundo plano foi iniciada com dados de entrada ou parâmetros de processamento com falha.</span><span class="sxs-lookup"><span data-stu-id="42651-250">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="42651-251">O conjunto atual de itens de trabalho em segundo plano em execução deve ser substituído por um novo conjunto de itens de trabalho.</span><span class="sxs-lookup"><span data-stu-id="42651-251">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="42651-252">A prioridade das tarefas em execução no momento deve ser alterada.</span><span class="sxs-lookup"><span data-stu-id="42651-252">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="42651-253">O aplicativo deve ser desligado para reimplantá-lo no servidor.</span><span class="sxs-lookup"><span data-stu-id="42651-253">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="42651-254">Os recursos do servidor se tornam limitados, exigindo o reagendamento de itens de trabalho em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="42651-254">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="42651-255">Para implementar um padrão de trabalho de segundo plano cancelável em um componente:</span><span class="sxs-lookup"><span data-stu-id="42651-255">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="42651-256">Use um <xref:System.Threading.CancellationTokenSource> e <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="42651-256">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="42651-257">Na [alienação do componente](#component-disposal-with-idisposable) e a qualquer cancelamento do ponto, é desejável cancelar manualmente o token, chamar [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) para sinalizar que o trabalho em segundo plano deve ser cancelado.</span><span class="sxs-lookup"><span data-stu-id="42651-257">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="42651-258">Após a chamada assíncrona retornar, chame <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> no token.</span><span class="sxs-lookup"><span data-stu-id="42651-258">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="42651-259">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="42651-259">In the following example:</span></span>

* <span data-ttu-id="42651-260">`await Task.Delay(5000, cts.Token);` representa o trabalho em segundo plano assíncrono de execução longa.</span><span class="sxs-lookup"><span data-stu-id="42651-260">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="42651-261">`BackgroundResourceMethod` representa um método de plano de fundo de execução longa que não deve iniciar se o `Resource` for descartado antes de o método ser chamado.</span><span class="sxs-lookup"><span data-stu-id="42651-261">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="42651-262">Blazor Server eventos de reconexão</span><span class="sxs-lookup"><span data-stu-id="42651-262">Blazor Server reconnection events</span></span>

<span data-ttu-id="42651-263">Os eventos de ciclo de vida do componente abordados neste artigo operam separadamente dos [ Blazor Server manipuladores de eventos de reconexão](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="42651-263">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="42651-264">Quando um Blazor Server aplicativo perde sua SignalR conexão com o cliente, somente as atualizações da interface do usuário são interrompidas.</span><span class="sxs-lookup"><span data-stu-id="42651-264">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="42651-265">As atualizações da interface do usuário são retomadas quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="42651-265">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="42651-266">Para obter mais informações sobre eventos e configurações do manipulador de circuitos, consulte <xref:blazor/fundamentals/additional-scenarios> .</span><span class="sxs-lookup"><span data-stu-id="42651-266">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
