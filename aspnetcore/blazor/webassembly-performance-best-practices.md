---
title: Blazor WebAssemblyPráticas recomendadas de desempenho de ASP.NET Core
author: pranavkm
description: Dicas para aumentar o desempenho em Blazor WebAssembly aplicativos ASP.NET Core e evitar problemas comuns de desempenho.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 64eb69ba597b1e48489733275ac3bed96c6c3241
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975229"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="35cb3-103">Blazor WebAssemblyPráticas recomendadas de desempenho de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35cb3-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="35cb3-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm) e [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="35cb3-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="35cb3-105">Blazor WebAssembly é cuidadosamente projetado e otimizado para permitir alto desempenho na maioria dos cenários de interface do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb3-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="35cb3-106">No entanto, a produção dos melhores resultados depende dos desenvolvedores que usam os padrões e recursos certos.</span><span class="sxs-lookup"><span data-stu-id="35cb3-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="35cb3-107">Considere os seguintes aspectos:</span><span class="sxs-lookup"><span data-stu-id="35cb3-107">Consider the following aspects:</span></span>

* <span data-ttu-id="35cb3-108">**Taxa de transferência de tempo de execução**: o código .net é executado em um intérprete no tempo de execução do Webassembly, portanto, a taxa de transferência da CPU é limitada</span><span class="sxs-lookup"><span data-stu-id="35cb3-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="35cb3-109">Em cenários exigentes, o aplicativo se beneficia da [otimização da velocidade de renderização](#optimize-rendering-speed).</span><span class="sxs-lookup"><span data-stu-id="35cb3-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="35cb3-110">**Tempo de inicialização**: o aplicativo transfere um tempo de execução do .net para o navegador, portanto, é importante usar recursos que [minimizem o tamanho do download do aplicativo](#minimize-app-download-size).</span><span class="sxs-lookup"><span data-stu-id="35cb3-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="35cb3-111">Otimizar a velocidade de renderização</span><span class="sxs-lookup"><span data-stu-id="35cb3-111">Optimize rendering speed</span></span>

<span data-ttu-id="35cb3-112">As seções a seguir fornecem recomendações para minimizar a carga de trabalho de renderização e melhorar a capacidade de resposta da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="35cb3-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="35cb3-113">Seguir esse aviso pode facilmente fazer uma *melhoria de dez vezes ou mais* em velocidades de renderização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="35cb3-114">Evitar renderização desnecessária de subárvores de componentes</span><span class="sxs-lookup"><span data-stu-id="35cb3-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="35cb3-115">Em tempo de execução, os componentes existem como uma hierarquia.</span><span class="sxs-lookup"><span data-stu-id="35cb3-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="35cb3-116">Um componente raiz tem componentes filhos.</span><span class="sxs-lookup"><span data-stu-id="35cb3-116">A root component has child components.</span></span> <span data-ttu-id="35cb3-117">Por sua vez, os filhos da raiz têm seus próprios componentes filho e assim por diante.</span><span class="sxs-lookup"><span data-stu-id="35cb3-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="35cb3-118">Quando ocorre um evento, como um usuário selecionando um botão, é assim Blazor que decide quais componentes serão renderizados novamente:</span><span class="sxs-lookup"><span data-stu-id="35cb3-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

1. <span data-ttu-id="35cb3-119">O evento em si é expedido para qualquer componente que renderizasse o manipulador do evento.</span><span class="sxs-lookup"><span data-stu-id="35cb3-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="35cb3-120">Depois de executar o manipulador de eventos, esse componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-120">After executing the event handler, that component is rerendered.</span></span>
1. <span data-ttu-id="35cb3-121">Sempre que qualquer componente for rerenderizado, ele fornecerá uma nova cópia dos valores de parâmetro para cada um de seus componentes filho.</span><span class="sxs-lookup"><span data-stu-id="35cb3-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
1. <span data-ttu-id="35cb3-122">Ao receber um novo conjunto de valores de parâmetro, cada componente escolhe se deseja rerenderizar.</span><span class="sxs-lookup"><span data-stu-id="35cb3-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="35cb3-123">Por padrão, os componentes são reprocessados se os valores de parâmetro podem ter sido alterados (por exemplo, se forem objetos mutáveis).</span><span class="sxs-lookup"><span data-stu-id="35cb3-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="35cb3-124">As duas últimas etapas dessa sequência continuam recursivamente a hierarquia de componentes.</span><span class="sxs-lookup"><span data-stu-id="35cb3-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="35cb3-125">Em muitos casos, toda a subárvore é reprocessada.</span><span class="sxs-lookup"><span data-stu-id="35cb3-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="35cb3-126">Isso significa que os eventos que visam componentes de alto nível podem causar processos dispendiosos de reprocessamento porque tudo abaixo desse ponto deve ser reprocessado.</span><span class="sxs-lookup"><span data-stu-id="35cb3-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="35cb3-127">Se você quiser interromper esse processo e evitar a recursão de renderização em uma subárvore específica, poderá:</span><span class="sxs-lookup"><span data-stu-id="35cb3-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

* <span data-ttu-id="35cb3-128">Verifique se todos os parâmetros para um determinado componente são de tipos irmutáveis primitivos (por exemplo,,,, `string` `int` `bool` `DateTime` e outros).</span><span class="sxs-lookup"><span data-stu-id="35cb3-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="35cb3-129">A lógica interna para detectar alterações automaticamente ignora a rerenderização se nenhum desses valores de parâmetro foram alterados.</span><span class="sxs-lookup"><span data-stu-id="35cb3-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="35cb3-130">Se você renderizar um componente filho com `<Customer CustomerId="@item.CustomerId" />` , em que `CustomerId` é um `int` valor, ele não será renderizado, exceto quando houver `item.CustomerId` alterações.</span><span class="sxs-lookup"><span data-stu-id="35cb3-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
* <span data-ttu-id="35cb3-131">Se você precisar aceitar valores de parâmetro não primitivos, como tipos de modelo personalizados, retornos de chamada de evento ou <xref:Microsoft.AspNetCore.Components.RenderFragment> valores, você poderá substituir <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para controlar a decisão sobre se deve renderizar, que é descrito no [uso da `ShouldRender` ](#use-of-shouldrender) seção.</span><span class="sxs-lookup"><span data-stu-id="35cb3-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="35cb3-132">Ao ignorar a rerenderização de subárvores inteiras, você poderá remover a grande maioria do custo de renderização quando ocorrer um evento.</span><span class="sxs-lookup"><span data-stu-id="35cb3-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="35cb3-133">Talvez você queira fatorar os componentes filho especificamente para que possa ignorar a rerenderização dessa parte da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="35cb3-134">Essa é uma maneira válida de reduzir o custo de renderização de um componente pai.</span><span class="sxs-lookup"><span data-stu-id="35cb3-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="35cb3-135">Uso de `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="35cb3-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="35cb3-136">Se criar um componente somente de interface do usuário que nunca é alterado após a renderização inicial (independentemente de quaisquer valores de parâmetro), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para retornar `false` :</span><span class="sxs-lookup"><span data-stu-id="35cb3-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="35cb3-137">Se o componente requer apenas a rerenderização quando seus valores de parâmetro se modificam de formas específicas, você pode usar campos privados para controlar as informações necessárias para detectar alterações.</span><span class="sxs-lookup"><span data-stu-id="35cb3-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="35cb3-138">No exemplo a seguir, `shouldRender` é baseado na verificação de qualquer tipo de alteração ou mutação que deve solicitar uma rerenderização.</span><span class="sxs-lookup"><span data-stu-id="35cb3-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="35cb3-139">`prevOutboundFlightId` e `prevInboundFlightId` rastreie informações para a próxima atualização em potencial:</span><span class="sxs-lookup"><span data-stu-id="35cb3-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override bool ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="35cb3-140">No código anterior, um manipulador de eventos também pode ser definido `shouldRender` como para `true` que o componente seja reprocessado após o evento.</span><span class="sxs-lookup"><span data-stu-id="35cb3-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="35cb3-141">Para a maioria dos componentes, esse nível de controle manual não é necessário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="35cb3-142">Você só deve se preocupar em ignorar as subárvores de renderização se essas subárvores forem particularmente caras de renderizar e estiverem causando atraso na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="35cb3-143">Para obter mais informações, consulte <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="35cb3-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="35cb3-144">Virtualização</span><span class="sxs-lookup"><span data-stu-id="35cb3-144">Virtualization</span></span>

<span data-ttu-id="35cb3-145">Ao renderizar grandes quantidades de interface do usuário dentro de um loop, por exemplo, uma lista ou grade com milhares de entradas, a quantidade enorme de operações de renderização pode levar a um retardo na renderização da interface do usuário e, portanto, uma experiência ruim para os usuários.</span><span class="sxs-lookup"><span data-stu-id="35cb3-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="35cb3-146">Considerando que o usuário só pode ver um pequeno número de elementos de uma só vez sem rolagem, parece que é dispendioso gastar muito tempo renderizando elementos que não estão visíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="35cb3-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="35cb3-147">Para resolver isso, Blazor o fornece o `Virtualize` componente que cria os comportamentos de aparência e rolagem de uma lista arbitrariamente grande, mas só renderiza os itens de lista que estão dentro do visor de rolagem atual.</span><span class="sxs-lookup"><span data-stu-id="35cb3-147">To address this, Blazor provides the `Virtualize` component that creates the appearance and scroll behaviors of an arbitrarily-large list but only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="35cb3-148">Por exemplo, isso significa que o aplicativo pode ter uma lista com 100.000 entradas, mas apenas pagar o custo de renderização de 20 itens visíveis a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="35cb3-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="35cb3-149">O uso do `Virtualize` componente pode escalar verticalmente o desempenho da interface do usuário em ordens de magnitude.</span><span class="sxs-lookup"><span data-stu-id="35cb3-149">Use of the `Virtualize` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="35cb3-150">Para obter mais informações, consulte <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="35cb3-150">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="35cb3-151">Crie componentes leves e otimizados</span><span class="sxs-lookup"><span data-stu-id="35cb3-151">Create lightweight, optimized components</span></span>

<span data-ttu-id="35cb3-152">A maioria dos Blazor componentes não exige esforços agressivos de otimização.</span><span class="sxs-lookup"><span data-stu-id="35cb3-152">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="35cb3-153">Isso ocorre porque a maioria dos componentes geralmente não se repete na interface do usuário e não reprocessa em alta frequência.</span><span class="sxs-lookup"><span data-stu-id="35cb3-153">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="35cb3-154">Por exemplo, `@page` componentes e componentes que representam partes de interface de usuário de alto nível, como caixas de diálogo ou formulários, provavelmente aparecem apenas um de cada vez e apenas rerenderizam em resposta a um gesto de usuário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-154">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="35cb3-155">Esses componentes não criam uma carga de trabalho de renderização alta, portanto, você pode usar livremente qualquer combinação de recursos de estrutura que desejar sem se preocupar muito com o desempenho de renderização.</span><span class="sxs-lookup"><span data-stu-id="35cb3-155">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="35cb3-156">No entanto, também há cenários comuns em que você cria componentes que precisam ser repetidos em escala.</span><span class="sxs-lookup"><span data-stu-id="35cb3-156">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="35cb3-157">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="35cb3-157">For example:</span></span>

* <span data-ttu-id="35cb3-158">Formulários aninhados grandes podem ter centenas de entradas, rótulos e outros elementos individuais.</span><span class="sxs-lookup"><span data-stu-id="35cb3-158">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
* <span data-ttu-id="35cb3-159">As grades podem ter milhares de células.</span><span class="sxs-lookup"><span data-stu-id="35cb3-159">Grids may have thousands of cells.</span></span>
* <span data-ttu-id="35cb3-160">As plotagens de dispersão podem ter milhões de pontos de dados.</span><span class="sxs-lookup"><span data-stu-id="35cb3-160">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="35cb3-161">Se modelar cada unidade como instâncias de componente separadas, haverá muitos deles que o desempenho de renderização se torna crítico.</span><span class="sxs-lookup"><span data-stu-id="35cb3-161">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="35cb3-162">Esta seção fornece conselhos sobre como tornar esses componentes leves para que a interface do usuário permaneça rápida e responsiva.</span><span class="sxs-lookup"><span data-stu-id="35cb3-162">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="35cb3-163">Evite milhares de instâncias de componentes</span><span class="sxs-lookup"><span data-stu-id="35cb3-163">Avoid thousands of component instances</span></span>

<span data-ttu-id="35cb3-164">Cada componente é uma ilha separada que pode ser renderizada independentemente de seus pais e filhos.</span><span class="sxs-lookup"><span data-stu-id="35cb3-164">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="35cb3-165">Ao escolher como dividir a interface do usuário em uma hierarquia de componentes, você está tomando controle sobre a granularidade da renderização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-165">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="35cb3-166">Isso pode ser bom ou ruim para o desempenho.</span><span class="sxs-lookup"><span data-stu-id="35cb3-166">This can be either good or bad for performance.</span></span>

* <span data-ttu-id="35cb3-167">Dividindo a interface do usuário em mais componentes, você pode ter partes menores da rerenderização da interface do usuário quando ocorrem eventos.</span><span class="sxs-lookup"><span data-stu-id="35cb3-167">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="35cb3-168">Por exemplo, quando um usuário clica em um botão em uma linha de tabela, você pode ter apenas a rerenderização de linha única em vez de toda a página ou tabela.</span><span class="sxs-lookup"><span data-stu-id="35cb3-168">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
* <span data-ttu-id="35cb3-169">No entanto, cada componente extra envolve alguma sobrecarga extra de memória e CPU para lidar com seu estado independente e ciclo de vida de renderização.</span><span class="sxs-lookup"><span data-stu-id="35cb3-169">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="35cb3-170">Ao ajustar o desempenho do Blazor WebAssembly no .NET 5, medimos uma sobrecarga de renderização de cerca de 0, 6 ms por instância de componente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-170">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="35cb3-171">Isso se baseia em um componente simples que aceita três parâmetros em execução em um laptop típico.</span><span class="sxs-lookup"><span data-stu-id="35cb3-171">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="35cb3-172">Internamente, a sobrecarga está em grande parte devido à recuperação do estado por componente de dicionários e passagem e recebimento de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="35cb3-172">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="35cb3-173">Por multiplicação, você pode ver que a adição de 2.000 instâncias de componente extras adicionará 0,12 segundos ao tempo de renderização e a interface do usuário começaria a ficar lenta para os usuários.</span><span class="sxs-lookup"><span data-stu-id="35cb3-173">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="35cb3-174">É possível tornar os componentes mais leves para que você possa ter mais deles, mas, em geral, a técnica mais poderosa não tem tantos componentes.</span><span class="sxs-lookup"><span data-stu-id="35cb3-174">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="35cb3-175">As seções a seguir descrevem duas abordagens.</span><span class="sxs-lookup"><span data-stu-id="35cb3-175">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="35cb3-176">Componentes filho embutidos em seus pais</span><span class="sxs-lookup"><span data-stu-id="35cb3-176">Inline child components into their parents</span></span>

<span data-ttu-id="35cb3-177">Considere o seguinte componente que renderiza uma sequência de componentes filho:</span><span class="sxs-lookup"><span data-stu-id="35cb3-177">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="35cb3-178">Para o código de exemplo anterior, o `<ChatMessageDisplay>` componente é definido em um arquivo `ChatMessageDisplay.razor` que contém:</span><span class="sxs-lookup"><span data-stu-id="35cb3-178">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="35cb3-179">O exemplo anterior funciona bem e tem um bom desempenho, desde que milhares de mensagens não sejam mostradas ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="35cb3-179">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="35cb3-180">Para mostrar milhares de mensagens de uma vez, considere *não* fatorar o `ChatMessageDisplay` componente separado.</span><span class="sxs-lookup"><span data-stu-id="35cb3-180">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="35cb3-181">Em vez disso, embutimos a renderização diretamente no pai:</span><span class="sxs-lookup"><span data-stu-id="35cb3-181">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="35cb3-182">Isso evita a sobrecarga por componente da renderização de forma que muitos componentes filho tenham o custo de não serem capazes de reprocessar cada um deles de forma independente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-182">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="35cb3-183">Definir reutilizável `RenderFragments` no código</span><span class="sxs-lookup"><span data-stu-id="35cb3-183">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="35cb3-184">Você pode estar decompondo componentes filho puramente como uma maneira de reutilizar a lógica de renderização.</span><span class="sxs-lookup"><span data-stu-id="35cb3-184">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="35cb3-185">Se esse for o caso, ainda é possível reutilizar a lógica de renderização sem declarar os componentes reais.</span><span class="sxs-lookup"><span data-stu-id="35cb3-185">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="35cb3-186">No bloco de qualquer componente `@code` , você pode definir um <xref:Microsoft.AspNetCore.Components.RenderFragment> que emita a interface do usuário e que possa ser chamado de qualquer lugar:</span><span class="sxs-lookup"><span data-stu-id="35cb3-186">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    private RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="35cb3-187">Como demonstated no exemplo anterior, os componentes podem emitir marcação a partir do código dentro de seu `@code` bloco e fora dele.</span><span class="sxs-lookup"><span data-stu-id="35cb3-187">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="35cb3-188">Essa abordagem define um <xref:Microsoft.AspNetCore.Components.RenderFragment> delegado que você pode renderizar dentro da saída de renderização normal do componente, opcionalmente em vários lugares.</span><span class="sxs-lookup"><span data-stu-id="35cb3-188">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="35cb3-189">É necessário que o delegado aceite um parâmetro chamado `__builder` do tipo <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para que o Razor compilador possa produzir instruções de renderização para ele.</span><span class="sxs-lookup"><span data-stu-id="35cb3-189">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="35cb3-190">Se você quiser tornar isso reutilizável em vários componentes, considere declará-lo como um `public static` membro:</span><span class="sxs-lookup"><span data-stu-id="35cb3-190">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="35cb3-191">Agora, isso pode ser invocado a partir de um componente não relacionado.</span><span class="sxs-lookup"><span data-stu-id="35cb3-191">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="35cb3-192">Essa técnica é útil para criar bibliotecas de trechos de marcação reutilizáveis que são renderizados sem nenhuma sobrecarga por componente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-192">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="35cb3-193"><xref:Microsoft.AspNetCore.Components.RenderFragment> os delegados também podem aceitar parâmetros.</span><span class="sxs-lookup"><span data-stu-id="35cb3-193"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="35cb3-194">Para criar o equivalente do `ChatMessageDisplay` componente do exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="35cb3-194">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @ChatMessageDisplay(message)
    }
</div>

@code {
    private RenderFragment<ChatMessage> ChatMessageDisplay = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="35cb3-195">Essa abordagem fornece o benefício de reutilizar a lógica de renderização sem sobrecarga por componente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-195">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="35cb3-196">No entanto, ele não tem o benefício de ser capaz de atualizar sua subárvore da interface do usuário de forma independente, nem tem a capacidade de ignorar o processamento dessa subárvore da interface do usuário quando seu pai é renderizado, já que não há nenhum limite de componente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-196">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

<span data-ttu-id="35cb3-197">Para um campo, método ou propriedade não estática que não pode ser referenciado por um inicializador de campo, como `TitleTemplate` no exemplo a seguir, use uma propriedade em vez de um campo para <xref:Microsoft.AspNetCore.Components.RenderFragment> :</span><span class="sxs-lookup"><span data-stu-id="35cb3-197">For a non-static field, method, or property that can't be referenced by a field initializer, such as `TitleTemplate` in the following example, use a property instead of a field for the <xref:Microsoft.AspNetCore.Components.RenderFragment>:</span></span>

```csharp
protected RenderFragment DisplayTitle => __builder =>
{
    <div>
        @TitleTemplate
    </div>   
};
```

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="35cb3-198">Não receber muitos parâmetros</span><span class="sxs-lookup"><span data-stu-id="35cb3-198">Don't receive too many parameters</span></span>

<span data-ttu-id="35cb3-199">Se um componente se repetir de forma extremamente frequente, por exemplo, centenas ou milhares de vezes, lembre-se de que a sobrecarga de passar e receber cada parâmetro se baseia.</span><span class="sxs-lookup"><span data-stu-id="35cb3-199">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="35cb3-200">É raro que muitos parâmetros restringem seriamente o desempenho, mas pode ser um fator.</span><span class="sxs-lookup"><span data-stu-id="35cb3-200">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="35cb3-201">Para um `<TableCell>` componente que renderiza 1.000 vezes em uma grade, cada parâmetro extra passado para ele poderia adicionar cerca de 15 ms ao custo total de renderização.</span><span class="sxs-lookup"><span data-stu-id="35cb3-201">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="35cb3-202">Se cada célula aceitasse 10 parâmetros, a passagem de parâmetro leva cerca de 150 ms por renderização de componente e, portanto, talvez 150.000 MS (150 segundos) e, por sua própria causa, uma interface de usuário com retardo.</span><span class="sxs-lookup"><span data-stu-id="35cb3-202">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="35cb3-203">Para reduzir essa carga, você pode reunir vários parâmetros por meio de classes personalizadas.</span><span class="sxs-lookup"><span data-stu-id="35cb3-203">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="35cb3-204">Por exemplo, um `<TableCell>` componente pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="35cb3-204">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="35cb3-205">No exemplo anterior, `Data` é diferente para cada célula, mas `Options` é comum em todos eles.</span><span class="sxs-lookup"><span data-stu-id="35cb3-205">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="35cb3-206">É claro que pode ser uma melhoria não ter um `<TableCell>` componente e, em vez disso, embutir sua lógica no componente pai.</span><span class="sxs-lookup"><span data-stu-id="35cb3-206">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="35cb3-207">Garantir que os parâmetros em cascata sejam corrigidos</span><span class="sxs-lookup"><span data-stu-id="35cb3-207">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="35cb3-208">O `<CascadingValue>` componente tem um parâmetro opcional chamado `IsFixed` .</span><span class="sxs-lookup"><span data-stu-id="35cb3-208">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

* <span data-ttu-id="35cb3-209">Se o `IsFixed` valor for `false` (o padrão), cada destinatário do valor em cascata configurará uma assinatura para receber notificações de alteração.</span><span class="sxs-lookup"><span data-stu-id="35cb3-209">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="35cb3-210">Nesse caso, cada `[CascadingParameter]` um é **substancialmente mais caro** do que `[Parameter]` o normal devido ao acompanhamento da assinatura.</span><span class="sxs-lookup"><span data-stu-id="35cb3-210">In this case, each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
* <span data-ttu-id="35cb3-211">Se o `IsFixed` valor for `true` (por exemplo, `<CascadingValue Value="@someValue" IsFixed="true">` ), destinatários receberá o valor inicial, mas *não* configurará nenhuma assinatura para receber atualizações.</span><span class="sxs-lookup"><span data-stu-id="35cb3-211">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="35cb3-212">Nesse caso, cada `[CascadingParameter]` uma é leve e **não mais cara** do que uma normal `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="35cb3-212">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="35cb3-213">Sempre que possível, você deve usar `IsFixed="true"` em valores em cascata.</span><span class="sxs-lookup"><span data-stu-id="35cb3-213">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="35cb3-214">Você pode fazer isso sempre que o valor que está sendo fornecido não for alterado ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="35cb3-214">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="35cb3-215">No padrão comum em que um componente passa `this` como um valor em cascata, você deve usar `IsFixed="true"` :</span><span class="sxs-lookup"><span data-stu-id="35cb3-215">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="35cb3-216">Isso faz uma enorme diferença se houver um grande número de outros componentes que recebam o valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="35cb3-216">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="35cb3-217">Para obter mais informações, consulte <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="35cb3-217">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="35cb3-218">Evite o atributo nivelamento com `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="35cb3-218">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="35cb3-219">Os componentes podem optar por receber valores de parâmetro "não correspondentes" usando o <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> sinalizador:</span><span class="sxs-lookup"><span data-stu-id="35cb3-219">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="35cb3-220">Essa abordagem permite passar atributos adicionais arbitrários para o elemento.</span><span class="sxs-lookup"><span data-stu-id="35cb3-220">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="35cb3-221">No entanto, também é muito caro porque o renderizador deve:</span><span class="sxs-lookup"><span data-stu-id="35cb3-221">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="35cb3-222">Corresponder a todos os parâmetros fornecidos em relação ao conjunto de parâmetros conhecidos para criar um dicionário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-222">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="35cb3-223">Mantenha o controle de como várias cópias do mesmo atributo substituem umas às outras.</span><span class="sxs-lookup"><span data-stu-id="35cb3-223">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="35cb3-224">Sinta-se à vontade para usar <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> em componentes sem desempenho crítico, como aqueles que não são repetidos com frequência.</span><span class="sxs-lookup"><span data-stu-id="35cb3-224">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="35cb3-225">No entanto, para componentes que são renderizados em escala, como cada item em uma grande lista ou células em uma grade, tente evitar o atributo nivelamento.</span><span class="sxs-lookup"><span data-stu-id="35cb3-225">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="35cb3-226">Para obter mais informações, consulte <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="35cb3-226">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="35cb3-227">Implementar `SetParametersAsync` manualmente</span><span class="sxs-lookup"><span data-stu-id="35cb3-227">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="35cb3-228">Um dos principais aspectos da sobrecarga de renderização por componente é gravar valores de parâmetro de entrada nas `[Parameter]` Propriedades.</span><span class="sxs-lookup"><span data-stu-id="35cb3-228">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="35cb3-229">O renderizador precisa usar a reflexão para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="35cb3-229">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="35cb3-230">Embora isso seja um pouco otimizado, a ausência de suporte JIT no tempo de execução do Webassembly impõe limites.</span><span class="sxs-lookup"><span data-stu-id="35cb3-230">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="35cb3-231">Em alguns casos extremos, talvez você queira evitar a reflexão e implementar sua própria lógica de configuração de parâmetro manualmente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-231">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="35cb3-232">Isso pode ser aplicável quando:</span><span class="sxs-lookup"><span data-stu-id="35cb3-232">This may be applicable when:</span></span>

* <span data-ttu-id="35cb3-233">Você tem um componente que renderiza extremamente frequentemente (por exemplo, há centenas ou milhares de cópias dele na interface do usuário).</span><span class="sxs-lookup"><span data-stu-id="35cb3-233">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
* <span data-ttu-id="35cb3-234">Ele aceita muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="35cb3-234">It accepts many parameters.</span></span>
* <span data-ttu-id="35cb3-235">Você descobre que a sobrecarga dos parâmetros de recebimento tem um impacto observável na capacidade de resposta da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-235">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="35cb3-236">Nesses casos, você pode substituir o método virtual do componente <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> e implementar sua própria lógica específica do componente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-236">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="35cb3-237">O exemplo a seguir evita deliberadamente quaisquer pesquisas de dicionário:</span><span class="sxs-lookup"><span data-stu-id="35cb3-237">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="35cb3-238">No código anterior, retornar a classe base <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> executa os métodos de ciclo de vida normais sem atribuir parâmetros novamente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-238">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="35cb3-239">Como você pode ver no código anterior, substituir <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> e fornecer lógica personalizada é complicado e trabalhoso, portanto, não recomendamos essa abordagem em geral.</span><span class="sxs-lookup"><span data-stu-id="35cb3-239">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="35cb3-240">Em casos extremos, ele pode melhorar o desempenho de renderização em 20-25%, mas você só deve considerar essa abordagem nos cenários listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-240">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="35cb3-241">Não dispare eventos muito rapidamente</span><span class="sxs-lookup"><span data-stu-id="35cb3-241">Don't trigger events too rapidly</span></span>

<span data-ttu-id="35cb3-242">Alguns eventos de navegador são acionados com muita frequência, por exemplo, `onmousemove` e `onscroll` , que podem ser lançados dezenas ou centenas de vezes por segundo.</span><span class="sxs-lookup"><span data-stu-id="35cb3-242">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="35cb3-243">Na maioria dos casos, você não precisa executar atualizações da interface do usuário com frequência.</span><span class="sxs-lookup"><span data-stu-id="35cb3-243">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="35cb3-244">Se você tentar fazer isso, poderá prejudicar a capacidade de resposta da interface do usuário ou consumir tempo excessivo de CPU.</span><span class="sxs-lookup"><span data-stu-id="35cb3-244">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="35cb3-245">Em vez de usar `@onmousemove` `@onscroll` eventos nativos, você pode preferir usar a interoperabilidade js para registrar um retorno de chamada que é acionado com menos frequência.</span><span class="sxs-lookup"><span data-stu-id="35cb3-245">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="35cb3-246">Por exemplo, o componente a seguir ( `MyComponent.razor` ) exibe a posição do mouse, mas só atualiza no máximo uma vez a cada 500 MS:</span><span class="sxs-lookup"><span data-stu-id="35cb3-246">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="35cb3-247">O código JavaScript correspondente, que pode ser colocado na `index.html` página ou carregado como um módulo ES6, registra o ouvinte de eventos dom real.</span><span class="sxs-lookup"><span data-stu-id="35cb3-247">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="35cb3-248">Neste exemplo, o ouvinte de eventos usa a [ `throttle` função do Lodash](https://lodash.com/docs/4.17.15#throttle) para limitar a taxa de invocações:</span><span class="sxs-lookup"><span data-stu-id="35cb3-248">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="35cb3-249">Essa técnica pode ser ainda mais importante para Blazor Server o, já que cada invocação de evento envolve a entrega de uma mensagem pela rede.</span><span class="sxs-lookup"><span data-stu-id="35cb3-249">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="35cb3-250">É valioso Blazor WebAssembly porque pode reduzir muito a quantidade de trabalho de renderização.</span><span class="sxs-lookup"><span data-stu-id="35cb3-250">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="35cb3-251">Otimizar a velocidade de interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="35cb3-251">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="35cb3-252">Chamadas entre .NET e JavaScript envolvem alguma sobrecarga adicional porque:</span><span class="sxs-lookup"><span data-stu-id="35cb3-252">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

* <span data-ttu-id="35cb3-253">Por padrão, as chamadas são assíncronas.</span><span class="sxs-lookup"><span data-stu-id="35cb3-253">By default, calls are asynchronous.</span></span>
* <span data-ttu-id="35cb3-254">Por padrão, os parâmetros e os valores de retorno são serializados em JSON.</span><span class="sxs-lookup"><span data-stu-id="35cb3-254">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="35cb3-255">Isso é para fornecer um mecanismo de conversão fácil de entender entre os tipos .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35cb3-255">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="35cb3-256">Além disso Blazor Server , essas chamadas são passadas pela rede.</span><span class="sxs-lookup"><span data-stu-id="35cb3-256">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="35cb3-257">Evite chamadas refinadas excessivamente</span><span class="sxs-lookup"><span data-stu-id="35cb3-257">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="35cb3-258">Como cada chamada envolve alguma sobrecarga, pode ser importante reduzir o número de chamadas.</span><span class="sxs-lookup"><span data-stu-id="35cb3-258">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="35cb3-259">Considere o código a seguir, que armazena uma coleção de itens na loja do navegador `localStorage` :</span><span class="sxs-lookup"><span data-stu-id="35cb3-259">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="35cb3-260">O exemplo anterior faz uma chamada de interoperabilidade do JS separada para cada item.</span><span class="sxs-lookup"><span data-stu-id="35cb3-260">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="35cb3-261">Em vez disso, a seguinte abordagem reduz a interoperabilidade de JS para uma única chamada:</span><span class="sxs-lookup"><span data-stu-id="35cb3-261">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="35cb3-262">A função JavaScript correspondente definida da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="35cb3-262">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="35cb3-263">Para Blazor WebAssembly o, isso geralmente só é importante se você estiver fazendo um grande número de chamadas de interoperabilidade js.</span><span class="sxs-lookup"><span data-stu-id="35cb3-263">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="35cb3-264">Considere fazer chamadas síncronas</span><span class="sxs-lookup"><span data-stu-id="35cb3-264">Consider making synchronous calls</span></span>

<span data-ttu-id="35cb3-265">As chamadas de interoperabilidade do JavaScript são assíncronas por padrão, independentemente de o código que está sendo chamado ser síncrono ou assíncrono.</span><span class="sxs-lookup"><span data-stu-id="35cb3-265">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="35cb3-266">Isso é para garantir que os componentes sejam compatíveis com o Blazor WebAssembly e o Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="35cb3-266">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="35cb3-267">Em Blazor Server , todas as chamadas de interoperabilidade do JavaScript devem ser assíncronas porque são enviadas por uma conexão de rede.</span><span class="sxs-lookup"><span data-stu-id="35cb3-267">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="35cb3-268">Se você tiver certeza de que seu aplicativo só é executado Blazor WebAssembly , você pode optar por fazer chamadas de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35cb3-268">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="35cb3-269">Isso tem um pouco menos de sobrecarga do que fazer chamadas assíncronas e pode resultar em menos ciclos de processamento porque não há um estado intermediário ao aguardar os resultados.</span><span class="sxs-lookup"><span data-stu-id="35cb3-269">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="35cb3-270">Para fazer uma chamada síncrona do .NET para o JavaScript, converta <xref:Microsoft.JSInterop.IJSRuntime> em <xref:Microsoft.JSInterop.IJSInProcessRuntime> :</span><span class="sxs-lookup"><span data-stu-id="35cb3-270">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="35cb3-271">Ao trabalhar com `IJSObjectReference` o, você pode fazer uma chamada síncrona por meio da conversão para `IJSInProcessObjectReference` .</span><span class="sxs-lookup"><span data-stu-id="35cb3-271">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="35cb3-272">Para fazer uma chamada síncrona do JavaScript para o .NET, use `DotNet.invokeMethod` em vez de `DotNet.invokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="35cb3-272">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="35cb3-273">Chamadas síncronas funcionam se:</span><span class="sxs-lookup"><span data-stu-id="35cb3-273">Synchronous calls work if:</span></span>

* <span data-ttu-id="35cb3-274">O aplicativo está sendo executado em Blazor WebAssembly , não Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="35cb3-274">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="35cb3-275">A função chamada retorna um valor de forma síncrona (não é um `async` método e não retorna um .NET <xref:System.Threading.Tasks.Task> ou JavaScript `Promise` ).</span><span class="sxs-lookup"><span data-stu-id="35cb3-275">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="35cb3-276">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="35cb3-276">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="35cb3-277">Considere fazer chamadas não marshalled</span><span class="sxs-lookup"><span data-stu-id="35cb3-277">Consider making unmarshalled calls</span></span>

<span data-ttu-id="35cb3-278">Ao executar em Blazor WebAssembly , é possível fazer chamadas não Marshalling de .net para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35cb3-278">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="35cb3-279">Essas são chamadas síncronas que não executam a serialização JSON de argumentos ou valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="35cb3-279">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="35cb3-280">Todos os aspectos do gerenciamento de memória e as traduções entre as representações do .NET e do JavaScript são deixados para o desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="35cb3-280">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="35cb3-281">Embora o uso do `IJSUnmarshalledRuntime` tenha a menor sobrecarga das abordagens de interoperabilidade do js, as APIs de JavaScript necessárias para interagir com essas APIs não estão documentadas no momento e estão sujeitas a alterações significativas em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="35cb3-281">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="35cb3-282">Minimizar o tamanho do download do aplicativo</span><span class="sxs-lookup"><span data-stu-id="35cb3-282">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="35cb3-283">Corte de IL (linguagem intermediária)</span><span class="sxs-lookup"><span data-stu-id="35cb3-283">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="35cb3-284">A corte de assemblies não utilizados de um Blazor WebAssembly aplicativo reduz o tamanho do aplicativo removendo o código não utilizado nos binários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb3-284">Trimming unused assemblies from a Blazor WebAssembly app reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="35cb3-285">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="35cb3-285">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="35cb3-286">Vinculação de IL (linguagem intermediária)</span><span class="sxs-lookup"><span data-stu-id="35cb3-286">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="35cb3-287">A [vinculação de um Blazor WebAssembly aplicativo](xref:blazor/host-and-deploy/configure-linker) reduz o tamanho do aplicativo ao cortar o código não utilizado nos binários do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="35cb3-287">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="35cb3-288">Por padrão, o vinculador de linguagem intermediária (IL) só é habilitado durante a criação da `Release` configuração.</span><span class="sxs-lookup"><span data-stu-id="35cb3-288">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="35cb3-289">Para se beneficiar disso, publique o aplicativo para implantação usando o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a opção de [configuração-c |--](/dotnet/core/tools/dotnet-publish#options) definida como `Release` :</span><span class="sxs-lookup"><span data-stu-id="35cb3-289">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

::: moniker-end

### <a name="use-systemtextjson"></a><span data-ttu-id="35cb3-290">Usar System.Text.Jsem</span><span class="sxs-lookup"><span data-stu-id="35cb3-290">Use System.Text.Json</span></span>

<span data-ttu-id="35cb3-291">Blazora implementação de interoperabilidade do JS depende de <xref:System.Text.Json> , que é uma biblioteca de SERIALIZAÇÃO JSON de alto desempenho com alocação de memória insuficiente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-291">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="35cb3-292">O uso <xref:System.Text.Json> do não resulta em tamanho de carga de aplicativo adicional ao adicionar uma ou mais bibliotecas JSON alternativas.</span><span class="sxs-lookup"><span data-stu-id="35cb3-292">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="35cb3-293">Para obter diretrizes de migração, consulte [como migrar do `Newtonsoft.Json` para `System.Text.Json` o ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="35cb3-293">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="35cb3-294">Assemblies de carga lenta</span><span class="sxs-lookup"><span data-stu-id="35cb3-294">Lazy load assemblies</span></span>

<span data-ttu-id="35cb3-295">Carregar assemblies em tempo de execução quando os assemblies são exigidos por uma rota.</span><span class="sxs-lookup"><span data-stu-id="35cb3-295">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="35cb3-296">Para obter mais informações, consulte <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="35cb3-296">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="35cb3-297">Compactação</span><span class="sxs-lookup"><span data-stu-id="35cb3-297">Compression</span></span>

<span data-ttu-id="35cb3-298">Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="35cb3-298">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="35cb3-299">Blazor depende do servidor para executar negotation de conteúdo e fornecer arquivos compactados estaticamente.</span><span class="sxs-lookup"><span data-stu-id="35cb3-299">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="35cb3-300">Depois que um aplicativo for implantado, verifique se o aplicativo serve arquivos compactados.</span><span class="sxs-lookup"><span data-stu-id="35cb3-300">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="35cb3-301">Inspecione a guia rede no Ferramentas para Desenvolvedores de um navegador e verifique se os arquivos são servidos com `Content-Encoding: br` ou `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="35cb3-301">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="35cb3-302">Se o host não estiver servindo arquivos compactados, siga as instruções em <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="35cb3-302">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="35cb3-303">Desabilitar recursos não utilizados</span><span class="sxs-lookup"><span data-stu-id="35cb3-303">Disable unused features</span></span>

<span data-ttu-id="35cb3-304">Blazor WebAssemblyo tempo de execução do inclui os seguintes recursos do .NET que podem ser desabilitados se o aplicativo não exigir um tamanho de carga menor:</span><span class="sxs-lookup"><span data-stu-id="35cb3-304">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="35cb3-305">Um arquivo de dados está incluído para tornar as informações de fuso horário corretas.</span><span class="sxs-lookup"><span data-stu-id="35cb3-305">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="35cb3-306">Se o aplicativo não exigir esse recurso, considere a possibilidade de desabilitá-lo definindo a `BlazorEnableTimeZoneSupport` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :</span><span class="sxs-lookup"><span data-stu-id="35cb3-306">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="35cb3-307">Por padrão, Blazor WebAssembly o transporta os recursos de globalização necessários para exibir valores, como datas e moedas, na cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="35cb3-307">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="35cb3-308">Se o aplicativo não exigir localização, você poderá [Configurar o aplicativo para dar suporte à cultura invariável](xref:blazor/globalization-localization), que se baseia na `en-US` cultura:</span><span class="sxs-lookup"><span data-stu-id="35cb3-308">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="35cb3-309">As informações de agrupamento estão incluídas para fazer APIs como o <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionamento correto.</span><span class="sxs-lookup"><span data-stu-id="35cb3-309">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="35cb3-310">Se você tiver certeza de que o aplicativo não exige os dados de agrupamento, considere desabilitá-lo definindo a `BlazorWebAssemblyPreserveCollationData` Propriedade MSBuild no arquivo de projeto do aplicativo como `false` :</span><span class="sxs-lookup"><span data-stu-id="35cb3-310">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
