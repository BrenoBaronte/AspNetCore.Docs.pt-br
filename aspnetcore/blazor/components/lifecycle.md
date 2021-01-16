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
ms.openlocfilehash: acaa276efda9fb4d09a5c1b1ca59c6abde1b64ec
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252378"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="ce2ae-103">BlazorCiclo de vida ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce2ae-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="ce2ae-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ce2ae-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ce2ae-105">A Blazor estrutura inclui métodos de ciclo de vida síncronos e assíncronos.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="ce2ae-106">Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="ce2ae-107">Os diagramas a seguir ilustram o Blazor ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="ce2ae-108">Os métodos de ciclo de vida são definidos com exemplos nas seções a seguir deste artigo.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="ce2ae-109">Eventos de ciclo de vida do componente:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="ce2ae-110">Se o componente estiver renderizando pela primeira vez em uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="ce2ae-111">Crie a instância do componente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-111">Create the component's instance.</span></span>
   * <span data-ttu-id="ce2ae-112">Execute a injeção de propriedade.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-112">Perform property injection.</span></span> <span data-ttu-id="ce2ae-113">Executar [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="ce2ae-114">Chame [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="ce2ae-115">Se um <xref:System.Threading.Tasks.Task> for retornado, o <xref:System.Threading.Tasks.Task> será esperado e o componente será renderizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="ce2ae-116">Se um <xref:System.Threading.Tasks.Task> não for retornado, o componente será renderizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="ce2ae-117">Chame [`OnParametersSet{Async}`](#after-parameters-are-set) e processe o componente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="ce2ae-118">Se um <xref:System.Threading.Tasks.Task> for retornado de `OnParametersSetAsync` , o <xref:System.Threading.Tasks.Task> será aguardado e, em seguida, o componente será renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Eventos de ciclo de vida do componente de um::: no-Loc (Razor)::: componente em::: no-Loc (mais alto):::](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="ce2ae-120">Processamento de eventos de Modelo de Objeto do Documento (DOM):</span><span class="sxs-lookup"><span data-stu-id="ce2ae-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="ce2ae-121">O manipulador de eventos é executado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-121">The event handler is run.</span></span>
1. <span data-ttu-id="ce2ae-122">Se um <xref:System.Threading.Tasks.Task> for retornado, o <xref:System.Threading.Tasks.Task> será esperado e, em seguida, o componente será renderizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="ce2ae-123">Se um <xref:System.Threading.Tasks.Task> não for retornado, o componente será renderizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Processamento de eventos de Modelo de Objeto do Documento (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="ce2ae-125">O `Render` ciclo de vida:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="ce2ae-126">Evite outras operações de renderização no componente:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="ce2ae-127">Após o primeiro processamento.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-127">After the first render.</span></span>
   * <span data-ttu-id="ce2ae-128">Quando [`ShouldRender`](#suppress-ui-refreshing) é `false` .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="ce2ae-129">Crie a comparação da árvore de renderização (diferença) e processe o componente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="ce2ae-130">Aguardar o DOM para atualizar.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="ce2ae-131">Chame [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Ciclo de vida de renderização](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="ce2ae-133">Chamadas de desenvolvedor para [`StateHasChanged`](#state-changes) resultar em uma renderização.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="ce2ae-134">Para obter mais informações, consulte <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="ce2ae-135">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="ce2ae-135">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="ce2ae-136">Antes de os parâmetros serem definidos</span><span class="sxs-lookup"><span data-stu-id="ce2ae-136">Before parameters are set</span></span>

<span data-ttu-id="ce2ae-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> define os parâmetros fornecidos pelo pai do componente na árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="ce2ae-138"><xref:Microsoft.AspNetCore.Components.ParameterView> contém o conjunto de valores de parâmetro para o componente cada vez que <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> é chamado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-138"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="ce2ae-139">A implementação padrão de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> define o valor de cada propriedade com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributo ou que tem um valor correspondente no <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-139">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="ce2ae-140">Os parâmetros que não têm um valor correspondente em <xref:Microsoft.AspNetCore.Components.ParameterView> são deixados inalterados.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-140">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="ce2ae-141">Se [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-141">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="ce2ae-142">Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-142">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="ce2ae-143">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-143">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ce2ae-144">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-144">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="ce2ae-145">Métodos de inicialização de componente</span><span class="sxs-lookup"><span data-stu-id="ce2ae-145">Component initialization methods</span></span>

<span data-ttu-id="ce2ae-146"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai no <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-146"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="ce2ae-147">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-147">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="ce2ae-148">Para uma operação síncrona, substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="ce2ae-148">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="ce2ae-149">Para executar uma operação assíncrona, substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> e use o [`await`](/dotnet/csharp/language-reference/operators/await) operador na operação:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-149">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="ce2ae-150">Blazor Server aplicativos que preparam [sua chamada de conteúdo](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-150">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="ce2ae-151">Uma vez quando o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-151">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="ce2ae-152">Uma segunda vez quando o navegador estabelece uma conexão de volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-152">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="ce2ae-153">Para impedir que o código <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> do desenvolvedor seja executado duas vezes, consulte a seção [reconexão com estado após o pré-processamento](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-153">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="ce2ae-154">Embora um Blazor Server aplicativo esteja sendo renderizado, determinadas ações, como chamar em JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-154">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="ce2ae-155">Os componentes podem precisar ser renderizados de forma diferente quando renderizados.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-155">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="ce2ae-156">Para obter mais informações, consulte a seção [detectar quando o aplicativo está sendo processado](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-156">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="ce2ae-157">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-157">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ce2ae-158">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-158">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="ce2ae-159">Depois que os parâmetros são definidos</span><span class="sxs-lookup"><span data-stu-id="ce2ae-159">After parameters are set</span></span>

<span data-ttu-id="ce2ae-160"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ou <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> são chamados:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-160"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="ce2ae-161">Depois que o componente é inicializado no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ou no <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-161">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="ce2ae-162">Quando o componente pai é renderizado novamente e fornece:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-162">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="ce2ae-163">Somente tipos irmutáveis de primitivo conhecidos dos quais pelo menos um parâmetro foi alterado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-163">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="ce2ae-164">Qualquer parâmetro de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-164">Any complex-typed parameters.</span></span> <span data-ttu-id="ce2ae-165">A estrutura não pode saber se os valores de um parâmetro de tipo complexo foram modificados internamente e, portanto, trata o conjunto de parâmetros como alterado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-165">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="ce2ae-166">O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> evento de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-166">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="ce2ae-167">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-167">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ce2ae-168">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-168">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="ce2ae-169">Após renderização de componente</span><span class="sxs-lookup"><span data-stu-id="ce2ae-169">After component render</span></span>

<span data-ttu-id="ce2ae-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> são chamados após a conclusão da renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="ce2ae-171">Referências de elemento e componente são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-171">Element and component references are populated at this point.</span></span> <span data-ttu-id="ce2ae-172">Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-172">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="ce2ae-173">O `firstRender` parâmetro para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="ce2ae-173">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="ce2ae-174">É definido como `true` a primeira vez que a instância do componente é renderizada.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-174">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="ce2ae-175">Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-175">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="ce2ae-176">O trabalho assíncrono imediatamente após a renderização deve ocorrer durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> evento do ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-176">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="ce2ae-177">Mesmo que você retorne um <xref:System.Threading.Tasks.Task> do <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-177">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="ce2ae-178">Isso é para evitar um loop de renderização infinito.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-178">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="ce2ae-179">Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-179">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="ce2ae-180"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *não são chamados durante o processo de pré-processamento no servidor*.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-180"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="ce2ae-181">Os métodos são chamados quando o componente é renderizado interativamente após a conclusão do pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-181">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="ce2ae-182">Quando o aplicativo é renderizado:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-182">When the app prerenders:</span></span>

1. <span data-ttu-id="ce2ae-183">O componente é executado no servidor para produzir alguma marcação HTML estática na resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-183">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="ce2ae-184">Durante essa fase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> não são chamados.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-184">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="ce2ae-185">Quando `blazor.server.js` ou `blazor.webassembly.js` inicia no navegador, o componente é reiniciado em um modo de renderização interativo.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-185">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="ce2ae-186">Depois que um componente é reiniciado, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **são** chamados porque o aplicativo não está dentro da fase de pré-processamento mais.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-186">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="ce2ae-187">Se algum manipulador de eventos estiver configurado, desvincule-os na alienação.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-187">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ce2ae-188">Para obter mais informações, consulte a seção [descarte `IDisposable` de componentes com](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-188">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="ce2ae-189">Suprimir atualização da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="ce2ae-189">Suppress UI refreshing</span></span>

<span data-ttu-id="ce2ae-190">Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-190">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="ce2ae-191">Se a implementação retornar `true` , a interface do usuário será atualizada:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-191">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="ce2ae-192"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> é chamado cada vez que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-192"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="ce2ae-193">Mesmo se <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> for substituído, o componente sempre será renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-193">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="ce2ae-194">Para obter mais informações, consulte <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-194">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="ce2ae-195">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="ce2ae-195">State changes</span></span>

<span data-ttu-id="ce2ae-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Notifica o componente de que seu estado foi alterado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="ce2ae-197">Quando aplicável, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> a chamada faz com que o componente seja rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-197">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="ce2ae-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> é chamado automaticamente para <xref:Microsoft.AspNetCore.Components.EventCallback> métodos.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="ce2ae-199">Para obter mais informações, consulte <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-199">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="ce2ae-200">Para obter mais informações, consulte <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-200">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="ce2ae-201">Tratar ações assíncronas incompletas no processamento</span><span class="sxs-lookup"><span data-stu-id="ce2ae-201">Handle incomplete async actions at render</span></span>

<span data-ttu-id="ce2ae-202">Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-202">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="ce2ae-203">Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-203">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="ce2ae-204">Forneça a lógica de renderização para confirmar que os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-204">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="ce2ae-205">Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto objetos são `null` .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-205">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="ce2ae-206">No `FetchData` componente dos Blazor modelos, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> é substituído para receber dados de previsão de forma assíncrona ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="ce2ae-206">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="ce2ae-207">Quando `forecasts` é `null` , uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-207">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="ce2ae-208">Depois que o `Task` retornado por <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> for concluído, o componente será rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-208">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="ce2ae-209">`Pages/FetchData.razor` no Blazor Server modelo:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-209">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="ce2ae-210">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="ce2ae-210">Handle errors</span></span>

<span data-ttu-id="ce2ae-211">Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-211">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="ce2ae-212">Reconexão com estado após o pré-processamento</span><span class="sxs-lookup"><span data-stu-id="ce2ae-212">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="ce2ae-213">Em um Blazor Server aplicativo quando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> é <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-213">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="ce2ae-214">Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente* e o componente agora é interativo.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-214">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="ce2ae-215">Se o [`OnInitialized{Async}`](#component-initialization-methods) método de ciclo de vida para inicializar o componente estiver presente, o método será executado *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-215">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="ce2ae-216">Quando o componente é renderizado estaticamente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-216">When the component is prerendered statically.</span></span>
* <span data-ttu-id="ce2ae-217">Depois que a conexão do servidor tiver sido estabelecida.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-217">After the server connection has been established.</span></span>

<span data-ttu-id="ce2ae-218">Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-218">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="ce2ae-219">Para evitar o cenário de processamento duplo em um Blazor Server aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-219">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="ce2ae-220">Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-220">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="ce2ae-221">Use o identificador durante o pré-processamento para salvar o estado do componente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-221">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="ce2ae-222">Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-222">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="ce2ae-223">O código a seguir demonstra uma atualização `WeatherForecastService` em um aplicativo baseado em modelo Blazor Server que evita a renderização dupla:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-223">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="ce2ae-224">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , consulte <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-224">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="ce2ae-225">Detectar quando o aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="ce2ae-225">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="ce2ae-226">Descarte de componentes com IDisposable</span><span class="sxs-lookup"><span data-stu-id="ce2ae-226">Component disposal with IDisposable</span></span>

<span data-ttu-id="ce2ae-227">Se um componente implementa <xref:System.IDisposable> , o [ `Dispose` método](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-227">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="ce2ae-228">A alienação pode ocorrer a qualquer momento, incluindo durante a [inicialização do componente](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="ce2ae-228">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="ce2ae-229">O componente a seguir usa `@implements IDisposable` o e o `Dispose` método:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-229">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

> [!NOTE]
> <span data-ttu-id="ce2ae-230">A chamada <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> no `Dispose` não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-230">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="ce2ae-231"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-231"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="ce2ae-232">Cancele a assinatura de manipuladores de eventos de eventos .NET.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-232">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="ce2ae-233">Os exemplos de [ Blazor formulário](xref:blazor/forms-validation) a seguir mostram como desvincular um manipulador de eventos no `Dispose` método:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-233">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="ce2ae-234">Campo privado e abordagem lambda</span><span class="sxs-lookup"><span data-stu-id="ce2ae-234">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="ce2ae-235">Abordagem do método privado</span><span class="sxs-lookup"><span data-stu-id="ce2ae-235">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="ce2ae-236">Trabalho em segundo plano cancelável</span><span class="sxs-lookup"><span data-stu-id="ce2ae-236">Cancelable background work</span></span>

<span data-ttu-id="ce2ae-237">Os componentes geralmente executam trabalho em segundo plano de longa execução, como fazer chamadas de rede ( <xref:System.Net.Http.HttpClient> ) e interagir com bancos de dados.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-237">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="ce2ae-238">É desejável interromper o trabalho em segundo plano para conservar recursos do sistema em várias situações.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-238">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="ce2ae-239">Por exemplo, as operações assíncronas em segundo plano não são interrompidas automaticamente quando um usuário sai de um componente.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-239">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="ce2ae-240">Outros motivos pelos quais os itens de trabalho em segundo plano podem exigir cancelamento incluem:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-240">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="ce2ae-241">Uma tarefa em execução em segundo plano foi iniciada com dados de entrada ou parâmetros de processamento com falha.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-241">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="ce2ae-242">O conjunto atual de itens de trabalho em segundo plano em execução deve ser substituído por um novo conjunto de itens de trabalho.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-242">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="ce2ae-243">A prioridade das tarefas em execução no momento deve ser alterada.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-243">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="ce2ae-244">O aplicativo deve ser desligado para reimplantá-lo no servidor.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-244">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="ce2ae-245">Os recursos do servidor se tornam limitados, exigindo o reagendamento de itens de trabalho em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-245">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="ce2ae-246">Para implementar um padrão de trabalho de segundo plano cancelável em um componente:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-246">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="ce2ae-247">Use um <xref:System.Threading.CancellationTokenSource> e <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-247">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="ce2ae-248">Na [alienação do componente](#component-disposal-with-idisposable) e a qualquer cancelamento do ponto, é desejável cancelar manualmente o token, chamar [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) para sinalizar que o trabalho em segundo plano deve ser cancelado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-248">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="ce2ae-249">Após a chamada assíncrona retornar, chame <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> no token.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-249">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="ce2ae-250">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ce2ae-250">In the following example:</span></span>

* <span data-ttu-id="ce2ae-251">`await Task.Delay(5000, cts.Token);` representa o trabalho em segundo plano assíncrono de execução longa.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-251">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="ce2ae-252">`BackgroundResourceMethod` representa um método de plano de fundo de execução longa que não deve iniciar se o `Resource` for descartado antes de o método ser chamado.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-252">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="ce2ae-253">Blazor Server eventos de reconexão</span><span class="sxs-lookup"><span data-stu-id="ce2ae-253">Blazor Server reconnection events</span></span>

<span data-ttu-id="ce2ae-254">Os eventos de ciclo de vida do componente abordados neste artigo operam separadamente dos [ Blazor Server manipuladores de eventos de reconexão](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="ce2ae-254">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="ce2ae-255">Quando um Blazor Server aplicativo perde sua SignalR conexão com o cliente, somente as atualizações da interface do usuário são interrompidas.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-255">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="ce2ae-256">As atualizações da interface do usuário são retomadas quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="ce2ae-256">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="ce2ae-257">Para obter mais informações sobre eventos e configurações do manipulador de circuitos, consulte <xref:blazor/fundamentals/additional-scenarios> .</span><span class="sxs-lookup"><span data-stu-id="ce2ae-257">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
