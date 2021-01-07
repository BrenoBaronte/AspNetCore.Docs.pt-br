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
ms.openlocfilehash: 051721b62397b582f1ffdaba08ffefe5d0c9ae03
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972009"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="0ea5a-103">BlazorVirtualização de componente ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ea5a-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="0ea5a-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0ea5a-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="0ea5a-105">Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="0ea5a-106">A virtualização é uma técnica para limitar a renderização da interface do usuário apenas às partes visíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="0ea5a-107">Por exemplo, a virtualização é útil quando o aplicativo deve renderizar uma longa lista de itens e apenas um subconjunto de itens é necessário para ser visível em um determinado momento.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="0ea5a-108">Blazorfornece o [ `Virtualize` componente](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) que pode ser usado para adicionar a virtualização aos componentes de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-108">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="0ea5a-109">Sem a virtualização, uma lista típica pode usar um [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop C# para renderizar cada item na lista:</span><span class="sxs-lookup"><span data-stu-id="0ea5a-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="0ea5a-110">Se a lista contiver milhares de itens, a renderização da lista poderá levar muito tempo.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="0ea5a-111">O usuário pode experimentar um atraso de interface do usuário perceptível.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="0ea5a-112">Em vez de renderizar cada item na lista todos de uma vez, substitua o [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop pelo `Virtualize` componente e especifique uma fonte de item fixa com <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0ea5a-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0ea5a-113">Somente os itens visíveis no momento são renderizados:</span><span class="sxs-lookup"><span data-stu-id="0ea5a-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="0ea5a-114">Se não especificar um contexto para o componente com `Context` , use o `context` valor ( `@context.{PROPERTY}` ) no modelo de conteúdo do item:</span><span class="sxs-lookup"><span data-stu-id="0ea5a-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="0ea5a-115">O `Virtualize` componente calcula quantos itens processar com base na altura do contêiner e no tamanho dos itens renderizados.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="0ea5a-116">O conteúdo do item para o `Virtualize` componente pode incluir:</span><span class="sxs-lookup"><span data-stu-id="0ea5a-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="0ea5a-117">HTML e código sem formatação Razor , como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="0ea5a-118">Um ou mais Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-118">One or more Razor components.</span></span>
* <span data-ttu-id="0ea5a-119">Uma combinação de HTML/ Razor e Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="0ea5a-120">Representante do provedor de item</span><span class="sxs-lookup"><span data-stu-id="0ea5a-120">Item provider delegate</span></span>

<span data-ttu-id="0ea5a-121">Se você não quiser carregar todos os itens na memória, poderá especificar um método delegado do provedor de itens para o parâmetro do componente <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> que recupera de forma assíncrona os itens solicitados sob demanda:</span><span class="sxs-lookup"><span data-stu-id="0ea5a-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="0ea5a-122">O provedor de itens recebe um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , que especifica o número necessário de itens começando em um índice de início específico.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-122">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="0ea5a-123">O provedor de itens recupera os itens solicitados de um banco de dados ou outro serviço e os retorna como um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> juntamente com uma contagem do total de itens.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-123">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="0ea5a-124">O provedor de itens pode optar por recuperar os itens com cada solicitação ou armazená-los em cache para que estejam prontamente disponíveis.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="0ea5a-125">Um `Virtualize` componente só pode aceitar **uma fonte** de itens de seus parâmetros, portanto, não tente usar um provedor de itens simultaneamente e atribuir uma coleção ao `Items` .</span><span class="sxs-lookup"><span data-stu-id="0ea5a-125">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="0ea5a-126">Se ambos forem atribuídos, um <xref:System.InvalidOperationException> será gerado quando os parâmetros do componente forem definidos em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-126">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="0ea5a-127">O exemplo a seguir carrega os funcionários de um `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="0ea5a-127">The following example loads employees from an `EmployeeService`:</span></span>

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

<span data-ttu-id="0ea5a-128"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instrui o componente a resolicitar dados do seu <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> .</span><span class="sxs-lookup"><span data-stu-id="0ea5a-128"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="0ea5a-129">Isso é útil quando dados externos são alterados.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-129">This is useful when external data changes.</span></span> <span data-ttu-id="0ea5a-130">Não é necessário chamá-lo ao usar o <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .</span><span class="sxs-lookup"><span data-stu-id="0ea5a-130">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="0ea5a-131">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="0ea5a-131">Placeholder</span></span>

<span data-ttu-id="0ea5a-132">Como a solicitação de itens de uma fonte de dados remota pode levar algum tempo, você tem a opção de renderizar um espaço reservado com o conteúdo do item:</span><span class="sxs-lookup"><span data-stu-id="0ea5a-132">Because requesting items from a remote data source might take some time, you have the option to render a placeholder  with item content:</span></span>

* <span data-ttu-id="0ea5a-133">Use um <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ) para exibir conteúdo até que os dados do item estejam disponíveis.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-133">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="0ea5a-134">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> para definir o modelo de item para a lista.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-134">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="0ea5a-135">Tamanho do item</span><span class="sxs-lookup"><span data-stu-id="0ea5a-135">Item size</span></span>

<span data-ttu-id="0ea5a-136">O tamanho de cada item em pixels pode ser definido com <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (padrão: 50px):</span><span class="sxs-lookup"><span data-stu-id="0ea5a-136">The size of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="0ea5a-137">Contagem de sobreverificações</span><span class="sxs-lookup"><span data-stu-id="0ea5a-137">Overscan count</span></span>

<span data-ttu-id="0ea5a-138"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> Determina quantos itens adicionais são renderizados antes e depois da região visível.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-138"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="0ea5a-139">Essa configuração ajuda a reduzir a frequência de renderização durante a rolagem.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-139">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="0ea5a-140">No entanto, valores mais altos resultam em mais elementos renderizados na página (padrão: 3):</span><span class="sxs-lookup"><span data-stu-id="0ea5a-140">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="0ea5a-141">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="0ea5a-141">State changes</span></span>

<span data-ttu-id="0ea5a-142">Ao fazer alterações em itens renderizados pelo `Virtualize` componente, chame <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> para forçar reavaliação e reprocessamento do componente.</span><span class="sxs-lookup"><span data-stu-id="0ea5a-142">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
