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
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor valores e parâmetros em cascata

*Valores em cascata e parâmetros* fornecem uma maneira convienent de fluir dados para baixo em uma hierarquia de componente de um componente ancestral para qualquer número de componentes decendent. Diferentemente dos [parâmetros de componente](xref:blazor/components/index#component-parameters), valores em cascata e parâmetros não exigem uma atribuição de atributo para cada componente descendente em que os dados são consumidos. Valores em cascata e parâmetros também permitem que os componentes se coordenem entre si em uma hierarquia de componentes.

## <a name="cascadingvalue-component"></a>componente `CascadingValue`

Um componente ancestral fornece um valor em cascata usando o Blazor componente da estrutura [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) , que encapsula uma subárvore de uma hierarquia de componentes e fornece um único valor para todos os componentes dentro de sua subárvore.

O exemplo a seguir demonstra o fluxo de informações de tema para baixo na hierarquia de componentes de um componente de layout para fornecer uma classe de estilo CSS para botões em componentes filho.

A `ThemeInfo` classe C# a seguir é colocada em uma pasta chamada `UIThemeClasses` e especifica as informações do tema.

> [!NOTE]
> Para os exemplos nesta seção, o namespace do aplicativo é `BlazorSample` . Ao experimentar o código em seu próprio aplicativo de exemplo, altere o namespace do aplicativo para o namespace do aplicativo de exemplo.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

O [componente de layout](xref:blazor/layouts) a seguir especifica informações de tema ( `ThemeInfo` ) como um valor em cascata para todos os componentes que compõem o corpo do layout da <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriedade. `ButtonClass` é atribuído um valor de [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) , que é um estilo de botão de inicialização. Qualquer componente descendente na hierarquia de componentes pode usar a `ButtonClass` Propriedade por meio do `ThemeInfo` valor em cascata.

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a>Atributo `[CascadingParameter]`

Para fazer uso de valores em cascata, os componentes descendentes declaram parâmetros em cascata usando o [ `[CascadingParameter]` atributo](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute). Os valores em cascata são associados a parâmetros em cascata **por tipo**. A cascata de vários valores do mesmo tipo é abordada na seção [em cascata de vários valores](#cascade-multiple-values) , mais adiante neste artigo.

O componente a seguir associa o `ThemeInfo` valor em cascata a um parâmetro em cascata, opcionalmente usando o mesmo nome de `ThemeInfo` . O parâmetro é usado para definir a classe CSS para o **`Increment Counter (Themed)`** botão.

`Pages/ThemedCounter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a>Propagar vários valores

Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> cadeia de caracteres exclusiva para cada [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) componente e seus [ `[CascadingParameter]` atributos](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)correspondentes.

No exemplo a seguir, dois [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) componentes em cascata diferentes instâncias de `CascadingType` :

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

Em um componente descendente, os parâmetros em cascata recebem seus valores em cascata do componente ancestral da <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> :

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a>Passar dados em uma hierarquia de componentes

Os parâmetros em cascata também permitem que os componentes passem dados em uma hierarquia de componentes. Considere o seguinte exemplo de conjunto de guias de interface do usuário, em que um componente de conjunto de guias mantém uma série de guias individuais.

> [!NOTE]
> Para os exemplos nesta seção, o namespace do aplicativo é `BlazorSample` . Ao experimentar o código em seu próprio aplicativo de exemplo, altere o namespace para o namespace do aplicativo de exemplo.

Crie uma `ITab` interface que as guias implementam em uma pasta chamada `UIInterfaces` .

`UIInterfaces/ITab.cs`:

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

O componente a seguir `TabSet` mantém um conjunto de guias. Os componentes do conjunto de guias `Tab` , que são criados mais adiante nesta seção, fornecem os itens da lista ( `<li>...</li>` ) para a lista ( `<ul>...</ul>` ).

`Tab`Os componentes filho não são passados explicitamente como parâmetros para `TabSet` . Em vez disso, os `Tab` componentes filho fazem parte do conteúdo filho do `TabSet` . No entanto, o `TabSet` ainda precisa de uma referência a cada `Tab` componente para que possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o `TabSet` componente *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes descendentes `Tab` .

`Shared/TabSet.razor`:

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

`Tab`Os componentes descendentes capturam o que contém `TabSet` como um parâmetro em cascata. Os `Tab` componentes se adicionam à `TabSet` coordenada e para definir a guia ativa.

`Shared/Tab.razor`:

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

O componente a seguir `ExampleTabSet` usa o `TabSet` componente, que contém três `Tab` componentes.

`Pages/ExampleTabSet.razor`:

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
