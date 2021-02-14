---
title: BlazorRenderização de componente ASP.NET Core
author: guardrex
description: Saiba mais sobre a Razor renderização de componentes em Blazor aplicativos ASP.NET Core, incluindo quando chamar StateHasChanged.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: e1222981d4af3f4e233cdc0c57bb96a71972af15
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280047"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="49b16-103">BlazorRenderização de componente ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b16-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="49b16-104">Os componentes *devem* ser renderizados quando são adicionados pela primeira vez à hierarquia de componentes por seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="49b16-104">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="49b16-105">Essa é a única vez que um componente deve ser renderizado estritamente.</span><span class="sxs-lookup"><span data-stu-id="49b16-105">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="49b16-106">Os componentes *podem* optar por renderizar a qualquer outro momento de acordo com sua própria lógica e convenções.</span><span class="sxs-lookup"><span data-stu-id="49b16-106">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="49b16-107">Convenções para `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="49b16-107">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="49b16-108">Por padrão, os Razor componentes ( `.razor` ) herdam da <xref:Microsoft.AspNetCore.Components.ComponentBase> classe base, que contém a lógica para disparar a rerenderização nos seguintes horários:</span><span class="sxs-lookup"><span data-stu-id="49b16-108">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="49b16-109">Depois de aplicar um conjunto atualizado de parâmetros de um componente pai.</span><span class="sxs-lookup"><span data-stu-id="49b16-109">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="49b16-110">Depois de aplicar um valor atualizado para um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="49b16-110">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="49b16-111">Após a notificação de um evento e a invocação de um de seus próprios manipuladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="49b16-111">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="49b16-112">Após uma chamada para seu próprio <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> método.</span><span class="sxs-lookup"><span data-stu-id="49b16-112">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="49b16-113">Componentes herdados de <xref:Microsoft.AspNetCore.Components.ComponentBase> ignorar rerenderizações devido a atualizações de parâmetros se uma das seguintes opções for verdadeira:</span><span class="sxs-lookup"><span data-stu-id="49b16-113">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="49b16-114">Todos os valores de parâmetro são de tipos primitivos imutáveis conhecidos (por exemplo,,, `int` `string` `DateTime` ) e não foram alterados desde que o conjunto de parâmetros anterior foi definido.</span><span class="sxs-lookup"><span data-stu-id="49b16-114">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="49b16-115">O método do componente <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> retorna `false` .</span><span class="sxs-lookup"><span data-stu-id="49b16-115">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="49b16-116">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, consulte <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span><span class="sxs-lookup"><span data-stu-id="49b16-116">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="49b16-117">Controlar o fluxo de renderização</span><span class="sxs-lookup"><span data-stu-id="49b16-117">Control the rendering flow</span></span>

<span data-ttu-id="49b16-118">Na maioria dos casos, as <xref:Microsoft.AspNetCore.Components.ComponentBase> convenções resultam no subconjunto correto de rerenderizações do componente após a ocorrência de um evento.</span><span class="sxs-lookup"><span data-stu-id="49b16-118">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="49b16-119">Normalmente, os desenvolvedores não precisam fornecer uma lógica manual para informar ao Framework quais componentes serão reprocessados e quando reprocessá-los.</span><span class="sxs-lookup"><span data-stu-id="49b16-119">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="49b16-120">O efeito geral das convenções da estrutura é que o componente que recebe um evento se reaplica, o que dispara recursivamente a rerenderização de componentes descendentes cujos valores de parâmetro podem ter sido alterados.</span><span class="sxs-lookup"><span data-stu-id="49b16-120">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="49b16-121">Para obter mais informações sobre as implicações de desempenho das convenções da estrutura e como otimizar a hierarquia de componentes de um aplicativo, consulte <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> .</span><span class="sxs-lookup"><span data-stu-id="49b16-121">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="49b16-122">Quando chamar `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="49b16-122">When to call `StateHasChanged`</span></span>

<span data-ttu-id="49b16-123">O <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> método permite disparar um processamento a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="49b16-123">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="49b16-124">No entanto, tenha cuidado para não chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> desnecessariamente, que é um erro comum, pois impõe custos de renderização desnecessários.</span><span class="sxs-lookup"><span data-stu-id="49b16-124">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="49b16-125">Você *não* precisará chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> quando:</span><span class="sxs-lookup"><span data-stu-id="49b16-125">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="49b16-126">A manipulação rotineira de eventos, seja de forma síncrona ou assíncrona, desde que <xref:Microsoft.AspNetCore.Components.ComponentBase> dispare uma renderização para a maioria dos manipuladores de eventos de rotina.</span><span class="sxs-lookup"><span data-stu-id="49b16-126">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="49b16-127">Implementar uma lógica de ciclo de vida típica, como [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) ou [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) , seja synchonrously ou assincronamente, desde que <xref:Microsoft.AspNetCore.Components.ComponentBase> dispare uma renderização para eventos de ciclo de vida típicos.</span><span class="sxs-lookup"><span data-stu-id="49b16-127">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="49b16-128">No entanto, pode fazer sentido chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> nos casos descritos pelas seções a seguir:</span><span class="sxs-lookup"><span data-stu-id="49b16-128">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="49b16-129">Um manipulador assíncrono envolve várias fases assíncronas</span><span class="sxs-lookup"><span data-stu-id="49b16-129">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="49b16-130">Recebendo uma chamada de algo externo para o Blazor sistema de processamento e manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="49b16-130">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="49b16-131">Para renderizar componente fora da subárvore que é rerenderizada por um evento específico</span><span class="sxs-lookup"><span data-stu-id="49b16-131">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="49b16-132">Um manipulador assíncrono envolve várias fases assíncronas</span><span class="sxs-lookup"><span data-stu-id="49b16-132">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="49b16-133">Considere o seguinte `Counter` componente, que atualiza a contagem quatro vezes em cada clique.</span><span class="sxs-lookup"><span data-stu-id="49b16-133">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="49b16-134">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="49b16-134">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="49b16-135">Devido à maneira como as tarefas são definidas no .NET, um receptor de um <xref:System.Threading.Tasks.Task> só pode observar sua conclusão final, não os Estados assíncronos intermediários.</span><span class="sxs-lookup"><span data-stu-id="49b16-135">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="49b16-136">Portanto, o <xref:Microsoft.AspNetCore.Components.ComponentBase> só pode disparar a rerenderização quando o <xref:System.Threading.Tasks.Task> é retornado pela primeira vez e quando o <xref:System.Threading.Tasks.Task> finalmente é concluído.</span><span class="sxs-lookup"><span data-stu-id="49b16-136">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="49b16-137">Não é possível saber reprocessar em outros pontos intermediários.</span><span class="sxs-lookup"><span data-stu-id="49b16-137">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="49b16-138">Se você quiser reprocessar em pontos intermediários, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="49b16-138">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="49b16-139">Recebendo uma chamada de algo externo para o Blazor sistema de processamento e manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="49b16-139">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="49b16-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> Só sabe sobre seus próprios métodos de ciclo de vida e Blazor eventos disparados.</span><span class="sxs-lookup"><span data-stu-id="49b16-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="49b16-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> Não sabe sobre outros eventos que podem ocorrer em seu código.</span><span class="sxs-lookup"><span data-stu-id="49b16-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="49b16-142">Por exemplo, qualquer evento C# gerado por um repositório de dados personalizado é desconhecido para Blazor .</span><span class="sxs-lookup"><span data-stu-id="49b16-142">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="49b16-143">Para que tais eventos disparem a rerenderização para exibir valores atualizados na interface do usuário, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="49b16-143">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="49b16-144">Em outro caso de uso, considere o seguinte `Counter` componente que o usa <xref:System.Timers.Timer?displayProperty=fullName> para atualizar a contagem em um intervalo regular e chamadas <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para atualizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="49b16-144">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="49b16-145">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="49b16-145">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="49b16-146">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="49b16-146">In the preceding example:</span></span>

* <span data-ttu-id="49b16-147">`OnTimerCallback` é necessário chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> porque Blazor não está ciente das alterações `currentCount` no retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="49b16-147">`OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="49b16-148">`OnTimerCallback` é executado fora de qualquer Blazor fluxo de renderização gerenciado ou notificação de eventos.</span><span class="sxs-lookup"><span data-stu-id="49b16-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>
* <span data-ttu-id="49b16-149">O componente implementa <xref:System.IDisposable> , onde o <xref:System.Timers.Timer> é Descartado quando a estrutura chama o `Dispose` método.</span><span class="sxs-lookup"><span data-stu-id="49b16-149">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="49b16-150">Para obter mais informações, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="49b16-150">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="49b16-151">Da mesma forma, como o retorno de chamada é invocado no Blazor contexto de sincronização de fora, é necessário encapsular a lógica no <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> para movê-lo para o contexto de sincronização do renderizador.</span><span class="sxs-lookup"><span data-stu-id="49b16-151">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="49b16-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Só pode ser chamado a partir do contexto de sincronização do renderizador e gera uma exceção de outra forma.</span><span class="sxs-lookup"><span data-stu-id="49b16-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="49b16-153">Isso é equivalente ao empacotamento para o thread de interface do usuário em outras estruturas de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="49b16-153">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="49b16-154">Para renderizar um componente fora da subárvore que é rerenderizada por um evento específico</span><span class="sxs-lookup"><span data-stu-id="49b16-154">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="49b16-155">Sua interface do usuário pode envolver a expedição de um evento para um componente, a alteração de algum estado e a necessidade de reprocessar um componente completamente diferente que não seja um descendente do que recebe o evento.</span><span class="sxs-lookup"><span data-stu-id="49b16-155">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="49b16-156">Uma maneira de lidar com esse cenário é ter uma classe de *Gerenciamento de estado* , talvez como um serviço de di, injetada em vários componentes.</span><span class="sxs-lookup"><span data-stu-id="49b16-156">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="49b16-157">Quando um componente chama um método no Gerenciador de estado, o Gerenciador de estado pode gerar um evento C# que é recebido por um componente independente.</span><span class="sxs-lookup"><span data-stu-id="49b16-157">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="49b16-158">Como esses eventos C# estão fora do Blazor pipeline de renderização, chame <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> em outros componentes que você deseja renderizar em resposta aos eventos do Gerenciador de estado.</span><span class="sxs-lookup"><span data-stu-id="49b16-158">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="49b16-159">Isso é semelhante ao caso anterior com <xref:System.Timers.Timer?displayProperty=fullName> na seção da [seção anterior](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) .</span><span class="sxs-lookup"><span data-stu-id="49b16-159">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="49b16-160">Como a pilha de chamadas de execução normalmente permanece no contexto de sincronização do processador, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> normalmente não é necessária.</span><span class="sxs-lookup"><span data-stu-id="49b16-160">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="49b16-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> Só será necessário se a lógica escapar o contexto de sincronização, como chamar <xref:System.Threading.Tasks.Task.ContinueWith%2A> em um <xref:System.Threading.Tasks.Task> ou aguardando um <xref:System.Threading.Tasks.Task> com [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) .</span><span class="sxs-lookup"><span data-stu-id="49b16-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
