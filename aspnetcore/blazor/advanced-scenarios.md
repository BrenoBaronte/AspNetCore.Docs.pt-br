---
title: ASP.NET Core Blazor cenários avançados
author: guardrex
description: Saiba mais sobre cenários avançados no Blazor , incluindo como incorporar a lógica de RenderTreeBuilder manual em um aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: ba2bf91f3318225383ec9d164c34be9124aa311b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280851"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="3bdbe-103">ASP.NET Core Blazor cenários avançados</span><span class="sxs-lookup"><span data-stu-id="3bdbe-103">ASP.NET Core Blazor advanced scenarios</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="3bdbe-104">Blazor Server manipulador de circuito</span><span class="sxs-lookup"><span data-stu-id="3bdbe-104">Blazor Server circuit handler</span></span>

<span data-ttu-id="3bdbe-105">Blazor Server permite que o código defina um *manipulador de circuito*, que permite a execução de código em alterações no estado do circuito de um usuário.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-105">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="3bdbe-106">Um manipulador de circuito é implementado derivando de `CircuitHandler` e registrando a classe no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-106">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="3bdbe-107">O exemplo a seguir de um manipulador de circuito rastreia SignalR conexões abertas:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-107">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="3bdbe-108">Os manipuladores de circuito são registrados usando DI.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-108">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="3bdbe-109">Instâncias com escopo são criadas por instância de um circuito.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-109">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="3bdbe-110">Usando o `TrackingCircuitHandler` no exemplo anterior, um serviço singleton é criado porque o estado de todos os circuitos deve ser acompanhado:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-110">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="3bdbe-111">Se os métodos de um manipulador de circuitos personalizados lançarem uma exceção sem tratamento, a exceção será fatal para o Blazor Server circuito.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-111">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="3bdbe-112">Para tolerar exceções no código de um manipulador ou em métodos chamados, empacote o código em uma ou mais [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruções com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-112">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="3bdbe-113">Quando um circuito termina porque um usuário se desconectou e a estrutura está limpando o estado do circuito, a estrutura descarta o escopo de DI do circuito.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-113">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="3bdbe-114">Descartar o escopo descarta todos os serviços de DI no escopo do circuito que implementam o <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="3bdbe-114">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="3bdbe-115">Se qualquer serviço de DI lançar uma exceção sem tratamento durante a alienação, a estrutura registrará a exceção.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-115">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="3bdbe-116">Lógica RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="3bdbe-116">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="3bdbe-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes em código C#.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="3bdbe-118">O uso do <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para criar componentes é um cenário avançado.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-118">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="3bdbe-119">Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-119">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="3bdbe-120">Considere o seguinte `PetDetails` componente, que pode ser compilado manualmente em outro componente:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-120">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="3bdbe-121">No exemplo a seguir, o loop no `CreateComponent` método gera três `PetDetails` componentes.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-121">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="3bdbe-122">Em <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> métodos com um número de sequência, os números de sequência são números de linha de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-122">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="3bdbe-123">O Blazor algoritmo de diferença depende dos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-123">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="3bdbe-124">Ao criar um componente com <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> métodos, codifique os argumentos para números de sequência.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-124">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="3bdbe-125">**O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.**</span><span class="sxs-lookup"><span data-stu-id="3bdbe-125">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="3bdbe-126">Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="3bdbe-126">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="3bdbe-127">`BuiltContent` componente</span><span class="sxs-lookup"><span data-stu-id="3bdbe-127">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="3bdbe-128">Os tipos no <xref:Microsoft.AspNetCore.Components.RenderTree> permitem o processamento dos *resultados* das operações de renderização.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-128">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="3bdbe-129">Esses são detalhes internos da Blazor implementação da estrutura.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-129">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="3bdbe-130">Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-130">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="3bdbe-131">Números de sequência se relacionam a números de linha de código e não a ordem de execução</span><span class="sxs-lookup"><span data-stu-id="3bdbe-131">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="3bdbe-132">Razor os arquivos de componente ( `.razor` ) são sempre compilados.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-132">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="3bdbe-133">A compilação é uma vantagem potencial sobre a interpretação do código, pois a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-133">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="3bdbe-134">Um exemplo importante desses aprimoramentos envolve *números de sequência*.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-134">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="3bdbe-135">Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-135">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="3bdbe-136">O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-136">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="3bdbe-137">Considere o seguinte Razor arquivo de componente ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="3bdbe-137">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="3bdbe-138">O código anterior é compilado para algo semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-138">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="3bdbe-139">Quando o código é executado pela primeira vez, se `someFlag` for `true` , o Construtor receberá:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-139">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="3bdbe-140">Sequência</span><span class="sxs-lookup"><span data-stu-id="3bdbe-140">Sequence</span></span> | <span data-ttu-id="3bdbe-141">Digite</span><span class="sxs-lookup"><span data-stu-id="3bdbe-141">Type</span></span>      | <span data-ttu-id="3bdbe-142">Dados</span><span class="sxs-lookup"><span data-stu-id="3bdbe-142">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="3bdbe-143">0</span><span class="sxs-lookup"><span data-stu-id="3bdbe-143">0</span></span>        | <span data-ttu-id="3bdbe-144">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="3bdbe-144">Text node</span></span> | <span data-ttu-id="3bdbe-145">Primeiro</span><span class="sxs-lookup"><span data-stu-id="3bdbe-145">First</span></span>  |
| <span data-ttu-id="3bdbe-146">1</span><span class="sxs-lookup"><span data-stu-id="3bdbe-146">1</span></span>        | <span data-ttu-id="3bdbe-147">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="3bdbe-147">Text node</span></span> | <span data-ttu-id="3bdbe-148">Segundo</span><span class="sxs-lookup"><span data-stu-id="3bdbe-148">Second</span></span> |

<span data-ttu-id="3bdbe-149">Imagine que `someFlag` se torna `false` e a marcação é renderizada novamente.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-149">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="3bdbe-150">Desta vez, o Construtor recebe:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-150">This time, the builder receives:</span></span>

| <span data-ttu-id="3bdbe-151">Sequência</span><span class="sxs-lookup"><span data-stu-id="3bdbe-151">Sequence</span></span> | <span data-ttu-id="3bdbe-152">Digite</span><span class="sxs-lookup"><span data-stu-id="3bdbe-152">Type</span></span>       | <span data-ttu-id="3bdbe-153">Dados</span><span class="sxs-lookup"><span data-stu-id="3bdbe-153">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="3bdbe-154">1</span><span class="sxs-lookup"><span data-stu-id="3bdbe-154">1</span></span>        | <span data-ttu-id="3bdbe-155">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="3bdbe-155">Text node</span></span>  | <span data-ttu-id="3bdbe-156">Segundo</span><span class="sxs-lookup"><span data-stu-id="3bdbe-156">Second</span></span> |

<span data-ttu-id="3bdbe-157">Quando o tempo de execução executa uma comparação, ele vê que o item na sequência `0` foi removido e, portanto, gera o seguinte *script de edição* trivial:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-157">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="3bdbe-158">Remova o primeiro nó de texto.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-158">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="3bdbe-159">O problema com a geração de números de sequência programaticamente</span><span class="sxs-lookup"><span data-stu-id="3bdbe-159">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="3bdbe-160">Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-160">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="3bdbe-161">Agora, a primeira saída é:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-161">Now, the first output is:</span></span>

| <span data-ttu-id="3bdbe-162">Sequência</span><span class="sxs-lookup"><span data-stu-id="3bdbe-162">Sequence</span></span> | <span data-ttu-id="3bdbe-163">Digite</span><span class="sxs-lookup"><span data-stu-id="3bdbe-163">Type</span></span>      | <span data-ttu-id="3bdbe-164">Dados</span><span class="sxs-lookup"><span data-stu-id="3bdbe-164">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="3bdbe-165">0</span><span class="sxs-lookup"><span data-stu-id="3bdbe-165">0</span></span>        | <span data-ttu-id="3bdbe-166">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="3bdbe-166">Text node</span></span> | <span data-ttu-id="3bdbe-167">Primeiro</span><span class="sxs-lookup"><span data-stu-id="3bdbe-167">First</span></span>  |
| <span data-ttu-id="3bdbe-168">1</span><span class="sxs-lookup"><span data-stu-id="3bdbe-168">1</span></span>        | <span data-ttu-id="3bdbe-169">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="3bdbe-169">Text node</span></span> | <span data-ttu-id="3bdbe-170">Segundo</span><span class="sxs-lookup"><span data-stu-id="3bdbe-170">Second</span></span> |

<span data-ttu-id="3bdbe-171">Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-171">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="3bdbe-172">`someFlag` está `false` no segundo processamento e a saída é:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-172">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="3bdbe-173">Sequência</span><span class="sxs-lookup"><span data-stu-id="3bdbe-173">Sequence</span></span> | <span data-ttu-id="3bdbe-174">Digite</span><span class="sxs-lookup"><span data-stu-id="3bdbe-174">Type</span></span>      | <span data-ttu-id="3bdbe-175">Dados</span><span class="sxs-lookup"><span data-stu-id="3bdbe-175">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="3bdbe-176">0</span><span class="sxs-lookup"><span data-stu-id="3bdbe-176">0</span></span>        | <span data-ttu-id="3bdbe-177">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="3bdbe-177">Text node</span></span> | <span data-ttu-id="3bdbe-178">Segundo</span><span class="sxs-lookup"><span data-stu-id="3bdbe-178">Second</span></span> |

<span data-ttu-id="3bdbe-179">Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:</span><span class="sxs-lookup"><span data-stu-id="3bdbe-179">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="3bdbe-180">Altere o valor do primeiro nó de texto para `Second` .</span><span class="sxs-lookup"><span data-stu-id="3bdbe-180">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="3bdbe-181">Remova o segundo nó de texto.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-181">Remove the second text node.</span></span>

<span data-ttu-id="3bdbe-182">A geração de números de sequência perdeu todas as informações úteis sobre onde os `if/else` branches e loops estavam presentes no código original.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-182">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="3bdbe-183">Isso resulta em uma comparação **duas vezes mais longa** do que antes.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-183">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="3bdbe-184">Esse é um exemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-184">This is a trivial example.</span></span> <span data-ttu-id="3bdbe-185">Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é geralmente mais alto.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-185">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="3bdbe-186">Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-186">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="3bdbe-187">Isso geralmente resulta na criação de scripts de edição mais longos porque o algoritmo diff é informado inconscientemente sobre como as estruturas antigas e novas se relacionam entre si.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-187">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="3bdbe-188">Diretrizes e conclusões</span><span class="sxs-lookup"><span data-stu-id="3bdbe-188">Guidance and conclusions</span></span>

* <span data-ttu-id="3bdbe-189">O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-189">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="3bdbe-190">A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-190">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="3bdbe-191">Não grave blocos longos de lógica implementada manualmente <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3bdbe-191">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="3bdbe-192">Prefira `.razor` arquivos e permita que o compilador lide com os números de sequência.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-192">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="3bdbe-193">Se não for possível evitar lógica manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> , divida blocos longos de código em partes menores encapsuladas em <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> chamadas.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-193">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="3bdbe-194">Cada região tem seu próprio espaço separado de números de sequência, para que você possa reiniciar de zero (ou qualquer outro número arbitrário) dentro de cada região.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-194">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="3bdbe-195">Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-195">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="3bdbe-196">O valor inicial e as lacunas são irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-196">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="3bdbe-197">Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial).</span><span class="sxs-lookup"><span data-stu-id="3bdbe-197">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="3bdbe-198">Blazor usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-198">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="3bdbe-199">A comparação é muito mais rápida quando números de sequência são usados e Blazor tem a vantagem de uma etapa de compilação que lida com números de sequência automaticamente para desenvolvedores que criam `.razor` arquivos.</span><span class="sxs-lookup"><span data-stu-id="3bdbe-199">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
