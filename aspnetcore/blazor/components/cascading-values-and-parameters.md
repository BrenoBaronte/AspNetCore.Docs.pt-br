---
title: ASP.NET Core Blazor valores e parâmetros em cascata
author: guardrex
description: Saiba como fluir dados de um componente ancestral para componentes descendentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280232"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="02636-103">ASP.NET Core Blazor valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="02636-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="02636-104">*Valores em cascata e parâmetros* fornecem uma maneira convienent de fluir dados para baixo em uma hierarquia de componente de um componente ancestral para qualquer número de componentes decendent.</span><span class="sxs-lookup"><span data-stu-id="02636-104">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="02636-105">Diferentemente dos [parâmetros de componente](xref:blazor/components/index#component-parameters), valores em cascata e parâmetros não exigem uma atribuição de atributo para cada componente descendente em que os dados são consumidos.</span><span class="sxs-lookup"><span data-stu-id="02636-105">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="02636-106">Valores em cascata e parâmetros também permitem que os componentes se coordenem entre si em uma hierarquia de componentes.</span><span class="sxs-lookup"><span data-stu-id="02636-106">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="02636-107">componente `CascadingValue`</span><span class="sxs-lookup"><span data-stu-id="02636-107">`CascadingValue` component</span></span>

<span data-ttu-id="02636-108">Um componente ancestral fornece um valor em cascata usando o Blazor componente da estrutura [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) , que encapsula uma subárvore de uma hierarquia de componentes e fornece um único valor para todos os componentes dentro de sua subárvore.</span><span class="sxs-lookup"><span data-stu-id="02636-108">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="02636-109">O exemplo a seguir demonstra o fluxo de informações de tema para baixo na hierarquia de componentes de um componente de layout para fornecer uma classe de estilo CSS para botões em componentes filho.</span><span class="sxs-lookup"><span data-stu-id="02636-109">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="02636-110">A `ThemeInfo` classe C# a seguir é colocada em uma pasta chamada `UIThemeClasses` e especifica as informações do tema.</span><span class="sxs-lookup"><span data-stu-id="02636-110">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="02636-111">Para os exemplos nesta seção, o namespace do aplicativo é `BlazorSample` .</span><span class="sxs-lookup"><span data-stu-id="02636-111">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="02636-112">Ao experimentar o código em seu próprio aplicativo de exemplo, altere o namespace do aplicativo para o namespace do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="02636-112">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="02636-113">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="02636-113">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="02636-114">O [componente de layout](xref:blazor/layouts) a seguir especifica informações de tema ( `ThemeInfo` ) como um valor em cascata para todos os componentes que compõem o corpo do layout da <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriedade.</span><span class="sxs-lookup"><span data-stu-id="02636-114">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="02636-115">`ButtonClass` é atribuído um valor de [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) , que é um estilo de botão de inicialização.</span><span class="sxs-lookup"><span data-stu-id="02636-115">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="02636-116">Qualquer componente descendente na hierarquia de componentes pode usar a `ButtonClass` Propriedade por meio do `ThemeInfo` valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="02636-116">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="02636-117">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="02636-117">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="02636-118">Atributo `[CascadingParameter]`</span><span class="sxs-lookup"><span data-stu-id="02636-118">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="02636-119">Para fazer uso de valores em cascata, os componentes descendentes declaram parâmetros em cascata usando o [ `[CascadingParameter]` atributo](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="02636-119">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="02636-120">Os valores em cascata são associados a parâmetros em cascata **por tipo**.</span><span class="sxs-lookup"><span data-stu-id="02636-120">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="02636-121">A cascata de vários valores do mesmo tipo é abordada na seção [em cascata de vários valores](#cascade-multiple-values) , mais adiante neste artigo.</span><span class="sxs-lookup"><span data-stu-id="02636-121">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="02636-122">O componente a seguir associa o `ThemeInfo` valor em cascata a um parâmetro em cascata, opcionalmente usando o mesmo nome de `ThemeInfo` .</span><span class="sxs-lookup"><span data-stu-id="02636-122">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="02636-123">O parâmetro é usado para definir a classe CSS para o **`Increment Counter (Themed)`** botão.</span><span class="sxs-lookup"><span data-stu-id="02636-123">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="02636-124">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="02636-124">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="02636-125">Propagar vários valores</span><span class="sxs-lookup"><span data-stu-id="02636-125">Cascade multiple values</span></span>

<span data-ttu-id="02636-126">Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> cadeia de caracteres exclusiva para cada [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) componente e seus [ `[CascadingParameter]` atributos](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)correspondentes.</span><span class="sxs-lookup"><span data-stu-id="02636-126">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="02636-127">No exemplo a seguir, dois [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) componentes em cascata diferentes instâncias de `CascadingType` :</span><span class="sxs-lookup"><span data-stu-id="02636-127">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="02636-128">Em um componente descendente, os parâmetros em cascata recebem seus valores em cascata do componente ancestral da <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> :</span><span class="sxs-lookup"><span data-stu-id="02636-128">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="02636-129">Passar dados em uma hierarquia de componentes</span><span class="sxs-lookup"><span data-stu-id="02636-129">Pass data across a component hierarchy</span></span>

<span data-ttu-id="02636-130">Os parâmetros em cascata também permitem que os componentes passem dados em uma hierarquia de componentes.</span><span class="sxs-lookup"><span data-stu-id="02636-130">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="02636-131">Considere o seguinte exemplo de conjunto de guias de interface do usuário, em que um componente de conjunto de guias mantém uma série de guias individuais.</span><span class="sxs-lookup"><span data-stu-id="02636-131">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="02636-132">Para os exemplos nesta seção, o namespace do aplicativo é `BlazorSample` .</span><span class="sxs-lookup"><span data-stu-id="02636-132">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="02636-133">Ao experimentar o código em seu próprio aplicativo de exemplo, altere o namespace para o namespace do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="02636-133">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="02636-134">Crie uma `ITab` interface que as guias implementam em uma pasta chamada `UIInterfaces` .</span><span class="sxs-lookup"><span data-stu-id="02636-134">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="02636-135">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="02636-135">`UIInterfaces/ITab.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

<span data-ttu-id="02636-136">O componente a seguir `TabSet` mantém um conjunto de guias.</span><span class="sxs-lookup"><span data-stu-id="02636-136">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="02636-137">Os componentes do conjunto de guias `Tab` , que são criados mais adiante nesta seção, fornecem os itens da lista ( `<li>...</li>` ) para a lista ( `<ul>...</ul>` ).</span><span class="sxs-lookup"><span data-stu-id="02636-137">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="02636-138">`Tab`Os componentes filho não são passados explicitamente como parâmetros para `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="02636-138">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="02636-139">Em vez disso, os `Tab` componentes filho fazem parte do conteúdo filho do `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="02636-139">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="02636-140">No entanto, o `TabSet` ainda precisa de uma referência a cada `Tab` componente para que possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o `TabSet` componente *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes descendentes `Tab` .</span><span class="sxs-lookup"><span data-stu-id="02636-140">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="02636-141">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="02636-141">`Shared/TabSet.razor`:</span></span>

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="02636-142">`Tab`Os componentes descendentes capturam o que contém `TabSet` como um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="02636-142">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="02636-143">Os `Tab` componentes se adicionam à `TabSet` coordenada e para definir a guia ativa.</span><span class="sxs-lookup"><span data-stu-id="02636-143">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="02636-144">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="02636-144">`Shared/Tab.razor`:</span></span>

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

<span data-ttu-id="02636-145">O componente a seguir `ExampleTabSet` usa o `TabSet` componente, que contém três `Tab` componentes.</span><span class="sxs-lookup"><span data-stu-id="02636-145">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="02636-146">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="02636-146">`Pages/ExampleTabSet.razor`:</span></span>

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```
