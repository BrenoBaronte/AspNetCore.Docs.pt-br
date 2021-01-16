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
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253911"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a>BlazorRenderização de componente ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

Os componentes *devem* ser renderizados quando são adicionados pela primeira vez à hierarquia de componentes por seu componente pai. Essa é a única vez que um componente deve ser renderizado estritamente.

Os componentes *podem* optar por renderizar a qualquer outro momento de acordo com sua própria lógica e convenções.

## <a name="conventions-for-componentbase"></a>Convenções para `ComponentBase`

Por padrão, os Razor componentes ( `.razor` ) herdam da <xref:Microsoft.AspNetCore.Components.ComponentBase> classe base, que contém a lógica para disparar a rerenderização nos seguintes horários:

* Depois de aplicar um conjunto atualizado de parâmetros de um componente pai.
* Depois de aplicar um valor atualizado para um parâmetro em cascata.
* Após a notificação de um evento e a invocação de um de seus próprios manipuladores de eventos.
* Após uma chamada para seu próprio <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> método.

Componentes herdados de <xref:Microsoft.AspNetCore.Components.ComponentBase> ignorar rerenderizações devido a atualizações de parâmetros se uma das seguintes opções for verdadeira:

* Todos os valores de parâmetro são de tipos primitivos imutáveis conhecidos (por exemplo,,, `int` `string` `DateTime` ) e não foram alterados desde que o conjunto de parâmetros anterior foi definido.
* O método do componente <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> retorna `false` .

Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, consulte <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.

## <a name="control-the-rendering-flow"></a>Controlar o fluxo de renderização

Na maioria dos casos, as <xref:Microsoft.AspNetCore.Components.ComponentBase> convenções resultam no subconjunto correto de rerenderizações do componente após a ocorrência de um evento. Normalmente, os desenvolvedores não precisam fornecer uma lógica manual para informar ao Framework quais componentes serão reprocessados e quando reprocessá-los. O efeito geral das convenções da estrutura é que o componente que recebe um evento se reaplica, o que dispara recursivamente a rerenderização de componentes descendentes cujos valores de parâmetro podem ter sido alterados.

Para obter mais informações sobre as implicações de desempenho das convenções da estrutura e como otimizar a hierarquia de componentes de um aplicativo, consulte <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> .

## <a name="when-to-call-statehaschanged"></a>Quando chamar `StateHasChanged`

O <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> método permite disparar um processamento a qualquer momento. No entanto, tenha cuidado para não chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> desnecessariamente, que é um erro comum, pois impõe custos de renderização desnecessários.

Você *não* precisará chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> quando:

* A manipulação rotineira de eventos, seja de forma síncrona ou assíncrona, desde que <xref:Microsoft.AspNetCore.Components.ComponentBase> dispare uma renderização para a maioria dos manipuladores de eventos de rotina.
* Implementar uma lógica de ciclo de vida típica, como [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) ou [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) , seja synchonrously ou assincronamente, desde que <xref:Microsoft.AspNetCore.Components.ComponentBase> dispare uma renderização para eventos de ciclo de vida típicos.

No entanto, pode fazer sentido chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> nos casos descritos pelas seções a seguir:

* [Um manipulador assíncrono envolve várias fases assíncronas](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [Recebendo uma chamada de algo externo para o Blazor sistema de processamento e manipulação de eventos](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [Para renderizar componente fora da subárvore que é rerenderizada por um evento específico](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>Um manipulador assíncrono envolve várias fases assíncronas

Considere o seguinte `Counter` componente, que atualiza a contagem quatro vezes em cada clique.

`Pages/Counter.razor`:

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

Devido à maneira como as tarefas são definidas no .NET, um receptor de um <xref:System.Threading.Tasks.Task> só pode observar sua conclusão final, não os Estados assíncronos intermediários. Portanto, o <xref:Microsoft.AspNetCore.Components.ComponentBase> só pode disparar a rerenderização quando o <xref:System.Threading.Tasks.Task> é retornado pela primeira vez e quando o <xref:System.Threading.Tasks.Task> finalmente é concluído. Não é possível saber reprocessar em outros pontos intermediários. Se você quiser reprocessar em pontos intermediários, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a>Recebendo uma chamada de algo externo para o Blazor sistema de processamento e manipulação de eventos

<xref:Microsoft.AspNetCore.Components.ComponentBase> Só sabe sobre seus próprios métodos de ciclo de vida e Blazor eventos disparados. <xref:Microsoft.AspNetCore.Components.ComponentBase> Não sabe sobre outros eventos que podem ocorrer em seu código. Por exemplo, qualquer evento C# gerado por um repositório de dados personalizado é desconhecido para Blazor . Para que tais eventos disparem a rerenderização para exibir valores atualizados na interface do usuário, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

Em outro caso de uso, considere o seguinte `Counter` componente que o usa <xref:System.Timers.Timer?displayProperty=fullName> para atualizar a contagem em um intervalo regular e chamadas <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para atualizar a interface do usuário.

`Pages/Counter.razor`:

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

No exemplo anterior, `OnTimerCallback` deve chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> porque Blazor não está ciente das alterações `currentCount` no retorno de chamada. `OnTimerCallback` é executado fora de qualquer Blazor fluxo de renderização gerenciado ou notificação de eventos.

Da mesma forma, como o retorno de chamada é invocado no Blazor contexto de sincronização de fora, é necessário encapsular a lógica no <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> para movê-lo para o contexto de sincronização do renderizador. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> Só pode ser chamado a partir do contexto de sincronização do renderizador e gera uma exceção de outra forma. Isso é equivalente ao empacotamento para o thread de interface do usuário em outras estruturas de interface do usuário.

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>Para renderizar um componente fora da subárvore que é rerenderizada por um evento específico

Sua interface do usuário pode envolver a expedição de um evento para um componente, a alteração de algum estado e a necessidade de reprocessar um componente completamente diferente que não seja um descendente do que recebe o evento.

Uma maneira de lidar com esse cenário é ter uma classe de *Gerenciamento de estado* , talvez como um serviço de di, injetada em vários componentes. Quando um componente chama um método no Gerenciador de estado, o Gerenciador de estado pode gerar um evento C# que é recebido por um componente independente.

Como esses eventos C# estão fora do Blazor pipeline de renderização, chame <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> em outros componentes que você deseja renderizar em resposta aos eventos do Gerenciador de estado.

Isso é semelhante ao caso anterior com <xref:System.Timers.Timer?displayProperty=fullName> na seção da [seção anterior](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) . Como a pilha de chamadas de execução normalmente permanece no contexto de sincronização do processador, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> normalmente não é necessária. <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> Só será necessário se a lógica escapar o contexto de sincronização, como chamar <xref:System.Threading.Tasks.Task.ContinueWith%2A> em um <xref:System.Threading.Tasks.Task> ou aguardando um <xref:System.Threading.Tasks.Task> com [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) .
