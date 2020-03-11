---
title: ASP.NET Core Blazor componentes modelados
author: guardrex
description: Saiba como os componentes modelados podem aceitar um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b64d6a731e540b13c50b2c6108f75efd0ac9290c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657713"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="97d85-103">ASP.NET Core Blazor componentes modelados</span><span class="sxs-lookup"><span data-stu-id="97d85-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="97d85-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="97d85-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="97d85-105">Componentes modelo são componentes que aceitam um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="97d85-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="97d85-106">Os componentes de modelo permitem que você crie componentes de nível superior que são mais reutilizáveis do que os componentes normais.</span><span class="sxs-lookup"><span data-stu-id="97d85-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="97d85-107">Alguns exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="97d85-107">A couple of examples include:</span></span>

* <span data-ttu-id="97d85-108">Um componente de tabela que permite que um usuário especifique modelos para o cabeçalho, as linhas e o rodapé da tabela.</span><span class="sxs-lookup"><span data-stu-id="97d85-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="97d85-109">Um componente de lista que permite que um usuário especifique um modelo para renderizar itens em uma lista.</span><span class="sxs-lookup"><span data-stu-id="97d85-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="97d85-110">Parâmetros do Modelo</span><span class="sxs-lookup"><span data-stu-id="97d85-110">Template parameters</span></span>

<span data-ttu-id="97d85-111">Um componente modelo é definido especificando um ou mais parâmetros de componente do tipo `RenderFragment` ou `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="97d85-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="97d85-112">Um fragmento de renderização representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="97d85-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="97d85-113">`RenderFragment<T>` usa um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.</span><span class="sxs-lookup"><span data-stu-id="97d85-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="97d85-114">`TableTemplate` componente:</span><span class="sxs-lookup"><span data-stu-id="97d85-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="97d85-115">Ao usar um componente modelo, os parâmetros do modelo podem ser especificados usando elementos filho que correspondem aos nomes dos parâmetros (`TableHeader` e `RowTemplate` no exemplo a seguir):</span><span class="sxs-lookup"><span data-stu-id="97d85-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="template-context-parameters"></a><span data-ttu-id="97d85-116">Parâmetros de contexto de modelo</span><span class="sxs-lookup"><span data-stu-id="97d85-116">Template context parameters</span></span>

<span data-ttu-id="97d85-117">Os argumentos de componente do tipo `RenderFragment<T>` passados como elementos têm um parâmetro implícito denominado `context` (por exemplo, do exemplo de código anterior, `@context.PetId`), mas você pode alterar o nome do parâmetro usando o atributo `Context` no elemento filho.</span><span class="sxs-lookup"><span data-stu-id="97d85-117">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="97d85-118">No exemplo a seguir, o atributo `Context` do elemento `RowTemplate` especifica o parâmetro `pet`:</span><span class="sxs-lookup"><span data-stu-id="97d85-118">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="97d85-119">Como alternativa, você pode especificar o atributo `Context` no elemento Component.</span><span class="sxs-lookup"><span data-stu-id="97d85-119">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="97d85-120">O atributo `Context` especificado se aplica a todos os parâmetros de modelo especificados.</span><span class="sxs-lookup"><span data-stu-id="97d85-120">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="97d85-121">Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícito (sem qualquer elemento filho de disposição).</span><span class="sxs-lookup"><span data-stu-id="97d85-121">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="97d85-122">No exemplo a seguir, o atributo `Context` aparece no elemento `TableTemplate` e se aplica a todos os parâmetros de modelo:</span><span class="sxs-lookup"><span data-stu-id="97d85-122">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="97d85-123">Componentes de tipo genérico</span><span class="sxs-lookup"><span data-stu-id="97d85-123">Generic-typed components</span></span>

<span data-ttu-id="97d85-124">Os componentes modelo são geralmente digitados genericamente.</span><span class="sxs-lookup"><span data-stu-id="97d85-124">Templated components are often generically typed.</span></span> <span data-ttu-id="97d85-125">Por exemplo, um componente de `ListViewTemplate` genérico pode ser usado para processar `IEnumerable<T>` valores.</span><span class="sxs-lookup"><span data-stu-id="97d85-125">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="97d85-126">Para definir um componente genérico, use a diretiva [`@typeparam`](xref:mvc/views/razor#typeparam) para especificar parâmetros de tipo:</span><span class="sxs-lookup"><span data-stu-id="97d85-126">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="97d85-127">Ao usar componentes de tipos genéricos, o parâmetro de tipo é inferido, se possível:</span><span class="sxs-lookup"><span data-stu-id="97d85-127">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="97d85-128">Caso contrário, o parâmetro de tipo deve ser especificado explicitamente usando um atributo que corresponda ao nome do parâmetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="97d85-128">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="97d85-129">No exemplo a seguir, `TItem="Pet"` especifica o tipo:</span><span class="sxs-lookup"><span data-stu-id="97d85-129">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```