---
title: BlazorVirtualização de componente ASP.NET Core
author: guardrex
description: Saiba como usar a virtualização de componentes em Blazor aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 706564bb8607d0bb25c092c31a72e5790c825ee4
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024672"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="52809-103">BlazorVirtualização de componente ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52809-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="52809-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="52809-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="52809-105">Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura.</span><span class="sxs-lookup"><span data-stu-id="52809-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="52809-106">A virtualização é uma técnica para limitar a renderização da interface do usuário apenas às partes visíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="52809-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="52809-107">Por exemplo, a virtualização é útil quando o aplicativo deve renderizar uma longa lista de itens e apenas um subconjunto de itens é necessário para ser visível em um determinado momento.</span><span class="sxs-lookup"><span data-stu-id="52809-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="52809-108">Blazorfornece o [ `Virtualize` componente](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) que pode ser usado para adicionar a virtualização aos componentes de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="52809-108">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="52809-109">O `Virtualize` componente pode ser usado quando:</span><span class="sxs-lookup"><span data-stu-id="52809-109">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="52809-110">Renderizando um conjunto de itens de dados em um loop.</span><span class="sxs-lookup"><span data-stu-id="52809-110">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="52809-111">A maioria dos itens não está visível devido à rolagem.</span><span class="sxs-lookup"><span data-stu-id="52809-111">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="52809-112">Os itens processados são exatamente do mesmo tamanho.</span><span class="sxs-lookup"><span data-stu-id="52809-112">The rendered items are exactly the same size.</span></span> <span data-ttu-id="52809-113">Quando o usuário rola para um ponto arbitrário, o componente pode calcular os itens visíveis a serem mostrados.</span><span class="sxs-lookup"><span data-stu-id="52809-113">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="52809-114">Sem a virtualização, uma lista típica pode usar um [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop C# para renderizar cada item na lista:</span><span class="sxs-lookup"><span data-stu-id="52809-114">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="52809-115">Se a lista contiver milhares de itens, a renderização da lista poderá levar muito tempo.</span><span class="sxs-lookup"><span data-stu-id="52809-115">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="52809-116">O usuário pode experimentar um atraso de interface do usuário perceptível.</span><span class="sxs-lookup"><span data-stu-id="52809-116">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="52809-117">Em vez de renderizar cada item na lista todos de uma vez, substitua o [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop pelo `Virtualize` componente e especifique uma fonte de item fixa com <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="52809-117">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="52809-118">Somente os itens visíveis no momento são renderizados:</span><span class="sxs-lookup"><span data-stu-id="52809-118">Only the items that are currently visible are rendered:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="52809-119">Se não especificar um contexto para o componente com `Context` , use o `context` valor ( `context.{PROPERTY}` / `@context.{PROPERTY}` ) no modelo de conteúdo do item:</span><span class="sxs-lookup"><span data-stu-id="52809-119">If not specifying a context to the component with `Context`, use the `context` value (`context.{PROPERTY}`/`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="52809-120">O processo de mapeamento de objetos de modelo para elementos e componentes pode ser controlado com o `@key` atributo de diretiva [] [xref: MVC/views/Razor # key].</span><span class="sxs-lookup"><span data-stu-id="52809-120">The mapping process of model objects to elements and components can be controlled with the [`@key`][xref:mvc/views/razor#key] directive attribute.</span></span> <span data-ttu-id="52809-121">`@key` faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave.</span><span class="sxs-lookup"><span data-stu-id="52809-121">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="52809-122">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="52809-122">For more information, see the following articles:</span></span>
>
> <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> <xref:mvc/views/razor#key>

<span data-ttu-id="52809-123">O `Virtualize` componente:</span><span class="sxs-lookup"><span data-stu-id="52809-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="52809-124">Calcula quantos itens processar com base na altura do contêiner e no tamanho dos itens renderizados.</span><span class="sxs-lookup"><span data-stu-id="52809-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="52809-125">Recalcula e rerenderiza os itens à medida que o usuário rola.</span><span class="sxs-lookup"><span data-stu-id="52809-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="52809-126">Busca apenas a fatia de registros de uma API externa que corresponda à região visível atual, em vez de baixar todos os dados da coleção.</span><span class="sxs-lookup"><span data-stu-id="52809-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="52809-127">O conteúdo do item para o `Virtualize` componente pode incluir:</span><span class="sxs-lookup"><span data-stu-id="52809-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="52809-128">HTML e código sem formatação Razor , como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="52809-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="52809-129">Um ou mais Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="52809-129">One or more Razor components.</span></span>
* <span data-ttu-id="52809-130">Uma combinação de HTML/ Razor e Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="52809-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="52809-131">Representante do provedor de item</span><span class="sxs-lookup"><span data-stu-id="52809-131">Item provider delegate</span></span>

<span data-ttu-id="52809-132">Se você não quiser carregar todos os itens na memória, poderá especificar um método delegado do provedor de itens para o parâmetro do componente <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> que recupera de forma assíncrona os itens solicitados sob demanda:</span><span class="sxs-lookup"><span data-stu-id="52809-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="52809-133">O provedor de itens recebe um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , que especifica o número necessário de itens começando em um índice de início específico.</span><span class="sxs-lookup"><span data-stu-id="52809-133">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="52809-134">O provedor de itens recupera os itens solicitados de um banco de dados ou outro serviço e os retorna como um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> juntamente com uma contagem do total de itens.</span><span class="sxs-lookup"><span data-stu-id="52809-134">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="52809-135">O provedor de itens pode optar por recuperar os itens com cada solicitação ou armazená-los em cache para que estejam prontamente disponíveis.</span><span class="sxs-lookup"><span data-stu-id="52809-135">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="52809-136">Um `Virtualize` componente só pode aceitar **uma fonte** de itens de seus parâmetros, portanto, não tente usar um provedor de itens simultaneamente e atribuir uma coleção ao `Items` .</span><span class="sxs-lookup"><span data-stu-id="52809-136">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="52809-137">Se ambos forem atribuídos, um <xref:System.InvalidOperationException> será gerado quando os parâmetros do componente forem definidos em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="52809-137">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="52809-138">O exemplo a seguir carrega os funcionários de um `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="52809-138">The following example loads employees from an `EmployeeService`:</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<span data-ttu-id="52809-139"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instrui o componente a resolicitar dados do seu <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> .</span><span class="sxs-lookup"><span data-stu-id="52809-139"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="52809-140">Isso é útil quando dados externos são alterados.</span><span class="sxs-lookup"><span data-stu-id="52809-140">This is useful when external data changes.</span></span> <span data-ttu-id="52809-141">Não é necessário chamá-lo ao usar o <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .</span><span class="sxs-lookup"><span data-stu-id="52809-141">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="52809-142">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="52809-142">Placeholder</span></span>

<span data-ttu-id="52809-143">Como a solicitação de itens de uma fonte de dados remota pode levar algum tempo, você tem a opção de renderizar um espaço reservado com o conteúdo do item:</span><span class="sxs-lookup"><span data-stu-id="52809-143">Because requesting items from a remote data source might take some time, you have the option to render a placeholder  with item content:</span></span>

* <span data-ttu-id="52809-144">Use um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ) para exibir conteúdo até que os dados do item estejam disponíveis.</span><span class="sxs-lookup"><span data-stu-id="52809-144">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="52809-145">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> para definir o modelo de item para a lista.</span><span class="sxs-lookup"><span data-stu-id="52809-145">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="52809-146">Tamanho do item</span><span class="sxs-lookup"><span data-stu-id="52809-146">Item size</span></span>

<span data-ttu-id="52809-147">O tamanho de cada item em pixels pode ser definido com <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (padrão: 50px):</span><span class="sxs-lookup"><span data-stu-id="52809-147">The size of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="52809-148">Contagem de sobreverificações</span><span class="sxs-lookup"><span data-stu-id="52809-148">Overscan count</span></span>

<span data-ttu-id="52809-149"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> Determina quantos itens adicionais são renderizados antes e depois da região visível.</span><span class="sxs-lookup"><span data-stu-id="52809-149"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="52809-150">Essa configuração ajuda a reduzir a frequência de renderização durante a rolagem.</span><span class="sxs-lookup"><span data-stu-id="52809-150">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="52809-151">No entanto, valores mais altos resultam em mais elementos renderizados na página (padrão: 3):</span><span class="sxs-lookup"><span data-stu-id="52809-151">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="52809-152">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="52809-152">State changes</span></span>

<span data-ttu-id="52809-153">Ao fazer alterações em itens renderizados pelo `Virtualize` componente, chame <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forçar reavaliação e reprocessamento do componente.</span><span class="sxs-lookup"><span data-stu-id="52809-153">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
