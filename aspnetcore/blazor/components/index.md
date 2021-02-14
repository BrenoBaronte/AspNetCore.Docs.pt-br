---
title: Criar e usar componentes de ASP.NET Core Razor
author: guardrex
description: Saiba como criar e usar Razor componentes, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
uid: blazor/components/index
ms.openlocfilehash: 823c24620b369874fdbc3e314b5b08952df83c8b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280106"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="b5f04-103">Criar e usar componentes de ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="b5f04-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="b5f04-104">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5f04-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b5f04-105">Blazor os aplicativos são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="b5f04-105">Blazor apps are built using *components*.</span></span> <span data-ttu-id="b5f04-106">Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="b5f04-106">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="b5f04-107">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b5f04-107">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="b5f04-108">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="b5f04-108">Components are flexible and lightweight.</span></span> <span data-ttu-id="b5f04-109">Eles podem ser aninhados, reutilizados e compartilhados entre projetos.</span><span class="sxs-lookup"><span data-stu-id="b5f04-109">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="b5f04-110">Classes de componente</span><span class="sxs-lookup"><span data-stu-id="b5f04-110">Component classes</span></span>

<span data-ttu-id="b5f04-111">Os componentes são implementados em [Razor](xref:mvc/views/razor) arquivos de componente ( `.razor` ) usando uma combinação de C# e marcação HTML.</span><span class="sxs-lookup"><span data-stu-id="b5f04-111">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="b5f04-112">Um componente no Blazor é conhecido formalmente como um *Razor componente*.</span><span class="sxs-lookup"><span data-stu-id="b5f04-112">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="b5f04-113">Sintaxe de Razor</span><span class="sxs-lookup"><span data-stu-id="b5f04-113">Razor syntax</span></span>

<span data-ttu-id="b5f04-114">Razor os componentes em Blazor aplicativos usam extensivamente a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="b5f04-114">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="b5f04-115">Se você não estiver familiarizado com a Razor linguagem de marcação, é recomendável ler a [ Razor referência de sintaxe para ASP.NET Core](xref:mvc/views/razor) antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="b5f04-115">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="b5f04-116">Ao acessar o conteúdo em Razor sintaxe, preste atenção especial às seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="b5f04-116">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="b5f04-117">[Diretivas](xref:mvc/views/razor#directives): `@` -palavras-chave reservadas prefixadas que normalmente alteram a maneira como a marcação de componente é analisada ou funciona.</span><span class="sxs-lookup"><span data-stu-id="b5f04-117">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="b5f04-118">[Atributos de diretiva](xref:mvc/views/razor#directive-attributes): `@` -palavras-chave reservadas prefixadas que normalmente alteram a maneira como os elementos de componente são analisados ou funcionam.</span><span class="sxs-lookup"><span data-stu-id="b5f04-118">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="b5f04-119">Nomes</span><span class="sxs-lookup"><span data-stu-id="b5f04-119">Names</span></span>

<span data-ttu-id="b5f04-120">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-120">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="b5f04-121">Por exemplo, `MyCoolComponent.razor` é válido e `myCoolComponent.razor` é inválido.</span><span class="sxs-lookup"><span data-stu-id="b5f04-121">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="b5f04-122">Roteamento</span><span class="sxs-lookup"><span data-stu-id="b5f04-122">Routing</span></span>

<span data-ttu-id="b5f04-123">O roteamento no Blazor é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-123">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="b5f04-124">Quando um Razor arquivo com uma [`@page`][9] diretiva é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="b5f04-124">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="b5f04-125">Em tempo de execução, o roteador procura classes de componentes com um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="b5f04-125">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="b5f04-126">Para obter mais informações, consulte <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b5f04-126">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="b5f04-127">Marcação</span><span class="sxs-lookup"><span data-stu-id="b5f04-127">Markup</span></span>

<span data-ttu-id="b5f04-128">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="b5f04-128">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="b5f04-129">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe C# inserida chamada *Razor* .</span><span class="sxs-lookup"><span data-stu-id="b5f04-129">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="b5f04-130">Quando um aplicativo é compilado, a marcação HTML e a lógica de renderização C# são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-130">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="b5f04-131">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-131">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="b5f04-132">Os membros da classe de componente são definidos em um [`@code`][1] bloco.</span><span class="sxs-lookup"><span data-stu-id="b5f04-132">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="b5f04-133">No [`@code`][1] bloco, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-133">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="b5f04-134">Mais de um [`@code`][1] bloco é permitido.</span><span class="sxs-lookup"><span data-stu-id="b5f04-134">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="b5f04-135">Os membros do componente podem ser usados como parte da lógica de renderização do componente usando expressões C# que começam com `@` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-135">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="b5f04-136">Por exemplo, um campo C# é renderizado pela prefixação `@` para o nome do campo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-136">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="b5f04-137">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="b5f04-137">The following example evaluates and renders:</span></span>

* <span data-ttu-id="b5f04-138">`headingFontStyle` para o valor da propriedade de CSS para `font-style` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-138">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="b5f04-139">`headingText` para o conteúdo do `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="b5f04-139">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="b5f04-140">Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="b5f04-140">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="b5f04-141">Blazor em seguida, compara a nova árvore de renderização com a anterior e aplica quaisquer modificações ao Modelo de Objeto do Documento do navegador (DOM).</span><span class="sxs-lookup"><span data-stu-id="b5f04-141">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="b5f04-142">Detalhes adicionais são fornecidos no <xref:blazor/components/rendering> .</span><span class="sxs-lookup"><span data-stu-id="b5f04-142">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="b5f04-143">Os componentes são classes C# comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="b5f04-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="b5f04-144">Os componentes que produzem páginas da Web geralmente residem na `Pages` pasta.</span><span class="sxs-lookup"><span data-stu-id="b5f04-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="b5f04-145">Os componentes que não são de página são frequentemente colocados na `Shared` pasta ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="b5f04-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="b5f04-146">Namespaces</span><span class="sxs-lookup"><span data-stu-id="b5f04-146">Namespaces</span></span>

<span data-ttu-id="b5f04-147">Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e do local do componente (pasta) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="b5f04-148">Se o namespace raiz do aplicativo for `BlazorSample` e o `Counter` componente residir na `Pages` pasta:</span><span class="sxs-lookup"><span data-stu-id="b5f04-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="b5f04-149">O `Counter` namespace do componente é `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="b5f04-150">O nome do tipo totalmente qualificado do componente é `BlazorSample.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="b5f04-151">Para pastas personalizadas que contêm componentes, adicione uma [`@using`][2] diretiva ao componente pai ou ao arquivo do aplicativo `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="b5f04-152">O exemplo a seguir torna os componentes na `Components` pasta disponíveis:</span><span class="sxs-lookup"><span data-stu-id="b5f04-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="b5f04-153">Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não exige a [`@using`][2] diretiva:</span><span class="sxs-lookup"><span data-stu-id="b5f04-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="b5f04-154">O namespace de um componente criado com o Razor é baseado em (em ordem de prioridade):</span><span class="sxs-lookup"><span data-stu-id="b5f04-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="b5f04-155">[`@namespace`][8] designação na Razor marcação File ( `.razor` ) ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="b5f04-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="b5f04-156">O projeto `RootNamespace` no arquivo de projeto ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="b5f04-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="b5f04-157">O nome do projeto, extraído do nome de arquivo do arquivo de projeto ( `.csproj` ) e o caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="b5f04-158">Por exemplo, a estrutura resolve `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) para o namespace `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="b5f04-159">Os componentes seguem regras de associação de nome C#.</span><span class="sxs-lookup"><span data-stu-id="b5f04-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="b5f04-160">Para o `Index` componente neste exemplo, os componentes no escopo são todos os componentes:</span><span class="sxs-lookup"><span data-stu-id="b5f04-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="b5f04-161">Na mesma pasta, `Pages` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="b5f04-162">Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="b5f04-163">`global::`Não há suporte para a qualificação.</span><span class="sxs-lookup"><span data-stu-id="b5f04-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="b5f04-164">[`using`](/dotnet/csharp/language-reference/keywords/using-statement)Não há suporte para a importação de componentes com instruções com alias (por exemplo, `@using Foo = Bar` ).</span><span class="sxs-lookup"><span data-stu-id="b5f04-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="b5f04-165">Não há suporte para nomes parcialmente qualificados.</span><span class="sxs-lookup"><span data-stu-id="b5f04-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="b5f04-166">Por exemplo, `@using BlazorSample` não há suporte para adicionar e referenciar o `NavMenu` componente ( `NavMenu.razor` ) com `<Shared.NavMenu></Shared.NavMenu>` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="b5f04-167">Suporte de classe parcial</span><span class="sxs-lookup"><span data-stu-id="b5f04-167">Partial class support</span></span>

<span data-ttu-id="b5f04-168">Razor os componentes são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="b5f04-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="b5f04-169">Razor os componentes são criados usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="b5f04-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="b5f04-170">O código C# é definido em um [`@code`][1] bloco com marcação e Razor código HTML em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="b5f04-171">Blazor os modelos definem seus Razor componentes usando essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="b5f04-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="b5f04-172">O código C# é colocado em um arquivo code-behind definido como uma classe parcial.</span><span class="sxs-lookup"><span data-stu-id="b5f04-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="b5f04-173">O exemplo a seguir mostra o `Counter` componente padrão com um [`@code`][1] bloco em um aplicativo gerado a partir de um Blazor modelo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="b5f04-174">A marcação HTML, o Razor código e o código C# estão no mesmo arquivo:</span><span class="sxs-lookup"><span data-stu-id="b5f04-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="b5f04-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-175">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="b5f04-176">O `Counter` componente também pode ser criado usando um arquivo code-behind com uma classe parcial:</span><span class="sxs-lookup"><span data-stu-id="b5f04-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="b5f04-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="b5f04-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="b5f04-179">Adicione quaisquer namespaces necessários ao arquivo de classe parcial, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="b5f04-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="b5f04-180">Os namespaces típicos usados pelos Razor componentes incluem:</span><span class="sxs-lookup"><span data-stu-id="b5f04-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="b5f04-181">[`@using`][2] as diretivas no `_Imports.razor` arquivo são aplicadas somente a Razor arquivos ( `.razor` ), não a arquivos C# ( `.cs` ).</span><span class="sxs-lookup"><span data-stu-id="b5f04-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="b5f04-182">Especificar uma classe base</span><span class="sxs-lookup"><span data-stu-id="b5f04-182">Specify a base class</span></span>

<span data-ttu-id="b5f04-183">A [`@inherits`][6] diretiva pode ser usada para especificar uma classe base para um componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="b5f04-184">O exemplo a seguir mostra como um componente pode herdar uma classe base, `BlazorRocksBase` ,, para fornecer as propriedades e os métodos do componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="b5f04-185">A classe base deve derivar de <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="b5f04-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="b5f04-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="b5f04-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-187">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="b5f04-188">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="b5f04-188">Use components</span></span>

<span data-ttu-id="b5f04-189">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="b5f04-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="b5f04-190">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="b5f04-191">A seguinte marcação no `Pages/Index.razor` renderiza uma `HeadingComponent` instância:</span><span class="sxs-lookup"><span data-stu-id="b5f04-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="b5f04-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="b5f04-193">Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="b5f04-194">A adição de uma [`@using`][2] diretiva para o namespace do componente torna o componente disponível, o que resolve o aviso.</span><span class="sxs-lookup"><span data-stu-id="b5f04-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="b5f04-195">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="b5f04-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="b5f04-196">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="b5f04-196">Route parameters</span></span>

<span data-ttu-id="b5f04-197">Os componentes podem receber parâmetros de rota do modelo de rota fornecido na [`@page`][9] diretiva.</span><span class="sxs-lookup"><span data-stu-id="b5f04-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="b5f04-198">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.</span><span class="sxs-lookup"><span data-stu-id="b5f04-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b5f04-199">Há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="b5f04-199">Optional parameters are supported.</span></span> <span data-ttu-id="b5f04-200">No exemplo a seguir, o `text` parâmetro opcional atribui o valor do segmento de rota à propriedade do componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b5f04-201">Se o segmento não estiver presente, o valor de `Text` será definido como `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="b5f04-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b5f04-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="b5f04-204">Não há suporte para parâmetros opcionais, portanto, duas [`@page`][9] diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="b5f04-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="b5f04-205">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b5f04-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b5f04-206">A segunda [`@page`][9] diretiva recebe o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="b5f04-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="b5f04-207">Para obter informações sobre como capturar todos os parâmetros de rota ( `{*pageRoute}` ), que capturam caminhos entre vários limites de pasta, consulte <xref:blazor/fundamentals/routing#catch-all-route-parameters> .</span><span class="sxs-lookup"><span data-stu-id="b5f04-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="b5f04-208">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="b5f04-208">Component parameters</span></span>

<span data-ttu-id="b5f04-209">Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas simples ou complexas na classe de componente com o [ `[Parameter]` atributo](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="b5f04-209">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="b5f04-210">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="b5f04-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="b5f04-211">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-211">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="b5f04-212">Os parâmetros de componente podem ser atribuídos a um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="b5f04-212">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="b5f04-213">No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da `Title` Propriedade do `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-213">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="b5f04-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-214">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="b5f04-215">Por convenção, um valor de atributo que consiste em código C# é atribuído a um parâmetro usando o [ Razor `@` símbolo reservado do](xref:mvc/views/razor#razor-syntax):</span><span class="sxs-lookup"><span data-stu-id="b5f04-215">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="b5f04-216">Campo ou propriedade pai: `Title="@{FIELD OR PROPERTY}` , em que o espaço reservado `{FIELD OR PROPERTY}` é um campo ou propriedade C# do componente pai.</span><span class="sxs-lookup"><span data-stu-id="b5f04-216">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="b5f04-217">Resultado de um método: `Title="@{METHOD}"` , em que o espaço reservado `{METHOD}` é um método C# do componente pai.</span><span class="sxs-lookup"><span data-stu-id="b5f04-217">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="b5f04-218">[Expressão implícita ou explícita](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"` , em que o espaço reservado `{EXPRESSION}` é uma expressão C#.</span><span class="sxs-lookup"><span data-stu-id="b5f04-218">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="b5f04-219">Para obter mais informações, consulte [ Razor referência de sintaxe para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b5f04-219">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="b5f04-220">Não crie componentes que gravam em seus próprios *parâmetros de componente*, use um campo particular em vez disso.</span><span class="sxs-lookup"><span data-stu-id="b5f04-220">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="b5f04-221">Para obter mais informações, consulte a seção [parâmetros substituídos](#overwritten-parameters) .</span><span class="sxs-lookup"><span data-stu-id="b5f04-221">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="b5f04-222">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="b5f04-222">Child content</span></span>

<span data-ttu-id="b5f04-223">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-223">Components can set the content of another component.</span></span> <span data-ttu-id="b5f04-224">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.</span><span class="sxs-lookup"><span data-stu-id="b5f04-224">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="b5f04-225">No exemplo a seguir, o `ChildComponent` tem uma `ChildContent` propriedade que representa um <xref:Microsoft.AspNetCore.Components.RenderFragment> , que representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-225">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="b5f04-226">O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-226">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="b5f04-227">O valor de `ChildContent` é recebido do componente pai e renderizado dentro do painel de inicialização `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-227">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="b5f04-228">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-228">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="b5f04-229">A propriedade que recebe o <xref:Microsoft.AspNetCore.Components.RenderFragment> conteúdo deve ser nomeada `ChildContent` por convenção.</span><span class="sxs-lookup"><span data-stu-id="b5f04-229">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="b5f04-230">O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das `<ChildComponent>` marcas.</span><span class="sxs-lookup"><span data-stu-id="b5f04-230">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="b5f04-231">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-231">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="b5f04-232">Devido à maneira que Blazor renderiza conteúdo filho, os componentes de renderização dentro de um `for` loop exigirão uma variável de índice local se a variável de loop de incremento for usada no conteúdo do componente filho:</span><span class="sxs-lookup"><span data-stu-id="b5f04-232">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="b5f04-233">Como alternativa, use um `foreach` loop com <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="b5f04-233">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="b5f04-234">Para obter informações sobre como um <xref:Microsoft.AspNetCore.Components.RenderFragment> pode ser usado como um modelo para Razor a interface do usuário do componente, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="b5f04-234">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="b5f04-235">Atributo nivelamento e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="b5f04-235">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="b5f04-236">Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-236">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="b5f04-237">Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a [`@attributes`][3] Razor diretiva.</span><span class="sxs-lookup"><span data-stu-id="b5f04-237">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="b5f04-238">Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="b5f04-238">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="b5f04-239">Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que dê suporte a muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="b5f04-239">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="b5f04-240">No exemplo a seguir, o primeiro `<input>` elemento ( `id="useIndividualParams"` ) usa parâmetros de componente individuais, enquanto o segundo `<input>` elemento ( `id="useAttributesDict"` ) usa o atributo nivelamento:</span><span class="sxs-lookup"><span data-stu-id="b5f04-240">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="b5f04-241">O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="b5f04-241">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="b5f04-242">Os elementos renderizados `<input>` usando ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="b5f04-242">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="b5f04-243">Para aceitar atributos arbitrários, defina um parâmetro de componente usando o [ `[Parameter]` atributo](xref:Microsoft.AspNetCore.Components.ParameterAttribute) com a <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriedade definida como `true` :</span><span class="sxs-lookup"><span data-stu-id="b5f04-243">To accept arbitrary attributes, define a component parameter using the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="b5f04-244">A <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> Propriedade on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b5f04-244">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="b5f04-245">Um componente só pode definir um único parâmetro com <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="b5f04-245">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="b5f04-246">O tipo de propriedade usado com <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="b5f04-246">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="b5f04-247">`IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="b5f04-247">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="b5f04-248">A posição de [`@attributes`][3] relativo à posição dos atributos do elemento é importante.</span><span class="sxs-lookup"><span data-stu-id="b5f04-248">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="b5f04-249">Quando [`@attributes`][3] são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro).</span><span class="sxs-lookup"><span data-stu-id="b5f04-249">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="b5f04-250">Considere o exemplo a seguir de um componente que consome um `Child` componente:</span><span class="sxs-lookup"><span data-stu-id="b5f04-250">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="b5f04-251">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-251">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="b5f04-252">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-252">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="b5f04-253">O `Child` atributo do componente `extra` é definido à direita de [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="b5f04-253">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="b5f04-254">O `Parent` renderizado do componente `<div>` contém `extra="5"` quando passado pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):</span><span class="sxs-lookup"><span data-stu-id="b5f04-254">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="b5f04-255">No exemplo a seguir, a ordem de `extra` e [`@attributes`][3] é invertida no `Child` `<div>` :</span><span class="sxs-lookup"><span data-stu-id="b5f04-255">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="b5f04-256">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-256">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="b5f04-257">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5f04-257">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="b5f04-258">O renderizado `<div>` no `Parent` componente contém `extra="10"` quando passado pelo atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="b5f04-258">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="b5f04-259">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="b5f04-259">Capture references to components</span></span>

<span data-ttu-id="b5f04-260">As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-260">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="b5f04-261">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="b5f04-261">To capture a component reference:</span></span>

* <span data-ttu-id="b5f04-262">Adicione um [`@ref`][4] atributo ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="b5f04-262">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="b5f04-263">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="b5f04-263">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="b5f04-264">Quando o componente é renderizado, o `loginDialog` campo é populado com a `CustomLoginDialog` instância de componente filho.</span><span class="sxs-lookup"><span data-stu-id="b5f04-264">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="b5f04-265">Em seguida, você pode invocar os métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-265">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5f04-266">A `loginDialog` variável é populada apenas depois que o componente é renderizado e sua saída inclui o `MyLoginDialog` elemento.</span><span class="sxs-lookup"><span data-stu-id="b5f04-266">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="b5f04-267">Até que o componente seja renderizado, não há nada a fazer referência.</span><span class="sxs-lookup"><span data-stu-id="b5f04-267">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="b5f04-268">Para manipular referências de componentes após a conclusão da renderização do componente, use os [ `OnAfterRenderAsync` `OnAfterRender` métodos ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="b5f04-268">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="b5f04-269">Para usar uma variável de referência com um manipulador de eventos, use uma expressão lambda ou atribua o delegado manipulador de eventos nos [ `OnAfterRenderAsync` `OnAfterRender` métodos ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="b5f04-269">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="b5f04-270">Isso garante que a variável de referência seja atribuída antes que o manipulador de eventos seja atribuído.</span><span class="sxs-lookup"><span data-stu-id="b5f04-270">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="b5f04-271">Para fazer referência a componentes em um loop, consulte [capturar referências para vários componentes filho semelhantes (dotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="b5f04-271">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="b5f04-272">Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), ela não é um recurso de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5f04-272">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="b5f04-273">As referências de componente não são passadas para o código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5f04-273">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="b5f04-274">As referências de componente são usadas apenas no código .NET.</span><span class="sxs-lookup"><span data-stu-id="b5f04-274">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="b5f04-275">Não **use referências** de componente para converter o estado dos componentes filho.</span><span class="sxs-lookup"><span data-stu-id="b5f04-275">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="b5f04-276">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="b5f04-276">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="b5f04-277">O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-277">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="b5f04-278">Contexto de sincronização</span><span class="sxs-lookup"><span data-stu-id="b5f04-278">Synchronization context</span></span>

<span data-ttu-id="b5f04-279">Blazor usa um contexto de sincronização ( <xref:System.Threading.SynchronizationContext> ) para impor um único thread lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="b5f04-279">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="b5f04-280">Os métodos de [ciclo de vida](xref:blazor/components/lifecycle) de um componente e quaisquer retornos de chamada de evento que são gerados pelo Blazor são executados no contexto de sincronização.</span><span class="sxs-lookup"><span data-stu-id="b5f04-280">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="b5f04-281">Blazor Servero contexto de sincronização do tenta emular um ambiente de thread único para que ele corresponda ao modelo Webassembly no navegador, que é um thread único.</span><span class="sxs-lookup"><span data-stu-id="b5f04-281">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="b5f04-282">Em qualquer momento determinado, o trabalho é executado em exatamente um thread, dando a impressão de um único thread lógico.</span><span class="sxs-lookup"><span data-stu-id="b5f04-282">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="b5f04-283">Duas operações não são executadas simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-283">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="b5f04-284">Evitar chamadas de bloqueio de thread</span><span class="sxs-lookup"><span data-stu-id="b5f04-284">Avoid thread-blocking calls</span></span>

<span data-ttu-id="b5f04-285">Em geral, não chame os métodos a seguir.</span><span class="sxs-lookup"><span data-stu-id="b5f04-285">Generally, don't call the following methods.</span></span> <span data-ttu-id="b5f04-286">Os métodos a seguir bloqueiam o thread e, portanto, impedem que o aplicativo retome o trabalho até que o subjacente <xref:System.Threading.Tasks.Task> seja concluído:</span><span class="sxs-lookup"><span data-stu-id="b5f04-286">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="b5f04-287">Invocar métodos de componente externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="b5f04-287">Invoke component methods externally to update state</span></span>

<span data-ttu-id="b5f04-288">No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o `InvokeAsync` método, que é expedido para o Blazor contexto de sincronização.</span><span class="sxs-lookup"><span data-stu-id="b5f04-288">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="b5f04-289">Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="b5f04-289">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="b5f04-290">Registre o `NotifierService` :</span><span class="sxs-lookup"><span data-stu-id="b5f04-290">Register the `NotifierService`:</span></span>

* <span data-ttu-id="b5f04-291">No Blazor WebAssembly , registre o serviço como singleton no `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="b5f04-291">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="b5f04-292">No Blazor Server , registre o serviço como escopo em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b5f04-292">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="b5f04-293">Use o `NotifierService` para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="b5f04-293">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="b5f04-294">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="b5f04-294">In the preceding example:</span></span>

* <span data-ttu-id="b5f04-295">`NotifierService` invoca o método do componente `OnNotify` fora do Blazor contexto de sincronização.</span><span class="sxs-lookup"><span data-stu-id="b5f04-295">`NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="b5f04-296">`InvokeAsync` é usado para alternar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="b5f04-296">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="b5f04-297">Para obter mais informações, consulte <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="b5f04-297">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="b5f04-298">O componente implementa <xref:System.IDisposable> e o `OnNotify` delegado tem uma assinatura cancelada no `Dispose` método, que é chamado pelo Framework quando o componente é Descartado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-298">The component implements <xref:System.IDisposable>, and the `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="b5f04-299">Para obter mais informações, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="b5f04-299">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="b5f04-300">Use \@ a chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="b5f04-300">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="b5f04-301">Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente mudam, o Blazor algoritmo de diferenciação do deve decidir quais elementos ou componentes anteriores podem ser retidos e como os objetos de modelo devem ser mapeados para eles.</span><span class="sxs-lookup"><span data-stu-id="b5f04-301">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="b5f04-302">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-302">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="b5f04-303">Considere o seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="b5f04-303">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="b5f04-304">O conteúdo da `People` coleção pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="b5f04-304">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="b5f04-305">Quando o componente é rerenderizado, o `<DetailsEditor>` componente pode ser alterado para receber `Details` valores de parâmetro diferentes.</span><span class="sxs-lookup"><span data-stu-id="b5f04-305">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="b5f04-306">Isso pode causar um reprocessamento mais complexo do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-306">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="b5f04-307">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.</span><span class="sxs-lookup"><span data-stu-id="b5f04-307">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="b5f04-308">O processo de mapeamento pode ser controlado com o [`@key`][5] atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="b5f04-308">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="b5f04-309">[`@key`][5] faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="b5f04-309">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="b5f04-310">Quando a `People` coleção é alterada, o algoritmo diff mantém a associação entre `<DetailsEditor>` instâncias e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="b5f04-310">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="b5f04-311">Se um `Person` for excluído da `People` lista, somente a instância correspondente `<DetailsEditor>` será REmovida da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b5f04-311">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="b5f04-312">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="b5f04-312">Other instances are left unchanged.</span></span>
* <span data-ttu-id="b5f04-313">Se um `Person` for inserido em alguma posição na lista, uma nova `<DetailsEditor>` instância será inserida na posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-313">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="b5f04-314">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="b5f04-314">Other instances are left unchanged.</span></span>
* <span data-ttu-id="b5f04-315">Se `Person` as entradas forem reordenadas, as `<DetailsEditor>` instâncias correspondentes serão preservadas e reordenadas na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b5f04-315">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="b5f04-316">Em alguns cenários, o uso de [`@key`][5] minimiza a complexidade da rerenderização e evita possíveis problemas com partes com estado da alteração do dom, como a posição do foco.</span><span class="sxs-lookup"><span data-stu-id="b5f04-316">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5f04-317">As chaves são locais para cada elemento ou componente de contêiner.</span><span class="sxs-lookup"><span data-stu-id="b5f04-317">Keys are local to each container element or component.</span></span> <span data-ttu-id="b5f04-318">As chaves não são comparadas globalmente ao longo do documento.</span><span class="sxs-lookup"><span data-stu-id="b5f04-318">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="b5f04-319">Quando usar a \@ chave</span><span class="sxs-lookup"><span data-stu-id="b5f04-319">When to use \@key</span></span>

<span data-ttu-id="b5f04-320">Normalmente, faz sentido usar [`@key`][5] sempre que uma lista é renderizada (por exemplo, em um bloco [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) e um valor adequado existe para definir o [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="b5f04-320">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="b5f04-321">Você também pode usar [`@key`][5] para evitar a Blazor preservação de uma subárvore de elemento ou componente quando um objeto for alterado:</span><span class="sxs-lookup"><span data-stu-id="b5f04-321">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="b5f04-322">Se `@currentPerson` forem alteradas, a [`@key`][5] diretiva de atributo forçará Blazor a descartar todo `<div>` e seus descendentes e recriará a subárvore dentro da interface do usuário com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="b5f04-322">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="b5f04-323">Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando houver `@currentPerson` alterações.</span><span class="sxs-lookup"><span data-stu-id="b5f04-323">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="b5f04-324">Quando não usar a \@ chave</span><span class="sxs-lookup"><span data-stu-id="b5f04-324">When not to use \@key</span></span>

<span data-ttu-id="b5f04-325">Há um custo de desempenho ao comparar com [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="b5f04-325">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="b5f04-326">O custo de desempenho não é grande, mas só especifica [`@key`][5] se controlar as regras de preservação de elementos ou componentes beneficiam o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-326">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="b5f04-327">Mesmo que [`@key`][5] não seja usado, Blazor o preserva as instâncias de elemento filho e de componente o máximo possível.</span><span class="sxs-lookup"><span data-stu-id="b5f04-327">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="b5f04-328">A única vantagem de usar o [`@key`][5] é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="b5f04-328">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="b5f04-329">Quais valores usar para a \@ chave</span><span class="sxs-lookup"><span data-stu-id="b5f04-329">What values to use for \@key</span></span>

<span data-ttu-id="b5f04-330">Geralmente, faz sentido fornecer um dos seguintes tipos de valor para [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="b5f04-330">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="b5f04-331">Instâncias de objeto de modelo (por exemplo, uma `Person` instância como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="b5f04-331">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="b5f04-332">Isso garante a preservação com base na igualdade de referência de objeto.</span><span class="sxs-lookup"><span data-stu-id="b5f04-332">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="b5f04-333">Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int` , `string` ou `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="b5f04-333">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="b5f04-334">Verifique se os valores usados para [`@key`][5] não conflitam.</span><span class="sxs-lookup"><span data-stu-id="b5f04-334">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="b5f04-335">Se valores conflitantes forem detectados no mesmo elemento pai, Blazor o lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="b5f04-335">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="b5f04-336">Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="b5f04-336">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="b5f04-337">Parâmetros substituídos</span><span class="sxs-lookup"><span data-stu-id="b5f04-337">Overwritten parameters</span></span>

<span data-ttu-id="b5f04-338">A Blazor estrutura geralmente impõe uma atribuição de parâmetro pai-para-filho segura:</span><span class="sxs-lookup"><span data-stu-id="b5f04-338">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="b5f04-339">Os parâmetros não são substituídos inesperadamente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-339">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="b5f04-340">Os efeitos colaterais são minimizados.</span><span class="sxs-lookup"><span data-stu-id="b5f04-340">Side-effects are minimized.</span></span> <span data-ttu-id="b5f04-341">Por exemplo, renderizações adicionais são evitadas porque podem criar loops de renderização infinitos.</span><span class="sxs-lookup"><span data-stu-id="b5f04-341">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="b5f04-342">Um componente filho recebe novos valores de parâmetro que possivelmente substituem os valores existentes quando o componente pai é rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-342">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="b5f04-343">Accidentially substituir valores de parâmetro em um componente filho geralmente ocorre ao desenvolver o componente com um ou mais parâmetros associados a dados e o desenvolvedor grava diretamente em um parâmetro no filho:</span><span class="sxs-lookup"><span data-stu-id="b5f04-343">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="b5f04-344">O componente filho é renderizado com um ou mais valores de parâmetro do componente pai.</span><span class="sxs-lookup"><span data-stu-id="b5f04-344">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="b5f04-345">O filho grava diretamente no valor de um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b5f04-345">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="b5f04-346">O componente pai rerenderiza e substitui o valor do parâmetro do filho.</span><span class="sxs-lookup"><span data-stu-id="b5f04-346">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="b5f04-347">O potencial para substituir valores de parâmetros também se estende aos setters de Propriedade do componente filho.</span><span class="sxs-lookup"><span data-stu-id="b5f04-347">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="b5f04-348">**Nossas diretrizes gerais não criam componentes que gravam diretamente em seus próprios parâmetros.**</span><span class="sxs-lookup"><span data-stu-id="b5f04-348">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="b5f04-349">Considere o seguinte componente com falha `Expander` que:</span><span class="sxs-lookup"><span data-stu-id="b5f04-349">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="b5f04-350">Renderiza conteúdo filho.</span><span class="sxs-lookup"><span data-stu-id="b5f04-350">Renders child content.</span></span>
* <span data-ttu-id="b5f04-351">Alterna a exibição de conteúdo filho com um parâmetro de componente ( `Expanded` ).</span><span class="sxs-lookup"><span data-stu-id="b5f04-351">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="b5f04-352">O componente grava diretamente no `Expanded` parâmetro, que demonstra o problema com parâmetros substituídos e deve ser evitado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-352">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="b5f04-353">O `Expander` componente é adicionado a um componente pai que pode chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="b5f04-353">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="b5f04-354">Inicialmente, os `Expander` componentes se comportam de forma independente quando suas `Expanded` Propriedades são alternadas.</span><span class="sxs-lookup"><span data-stu-id="b5f04-354">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="b5f04-355">Os componentes filho mantêm seus Estados como esperado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-355">The child components maintain their states as expected.</span></span> <span data-ttu-id="b5f04-356">Quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> é chamado no pai, o `Expanded` parâmetro do primeiro componente filho é redefinido de volta para seu valor inicial ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="b5f04-356">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="b5f04-357">O `Expander` valor do segundo componente `Expanded` não é redefinido porque nenhum conteúdo filho é renderizado no segundo componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-357">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="b5f04-358">Para manter o estado no cenário anterior, use um *campo particular* no `Expander` componente para manter seu estado de alternância.</span><span class="sxs-lookup"><span data-stu-id="b5f04-358">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="b5f04-359">O seguinte componente revisado `Expander` :</span><span class="sxs-lookup"><span data-stu-id="b5f04-359">The following revised `Expander` component:</span></span>

* <span data-ttu-id="b5f04-360">Aceita o `Expanded` valor do parâmetro de componente do pai.</span><span class="sxs-lookup"><span data-stu-id="b5f04-360">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="b5f04-361">Atribui o valor do parâmetro de componente a um *campo privado* ( `expanded` ) no [evento OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="b5f04-361">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="b5f04-362">Usa o campo particular para manter seu estado de alternância interno, que demonstra como evitar gravar diretamente em um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b5f04-362">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

<span data-ttu-id="b5f04-363">Para obter informações adicionais, consulte [ Blazor erro de associação bidirecional (dotNet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="b5f04-363">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="b5f04-364">Aplicar um atributo</span><span class="sxs-lookup"><span data-stu-id="b5f04-364">Apply an attribute</span></span>

<span data-ttu-id="b5f04-365">Os atributos podem ser aplicados a Razor componentes com a [`@attribute`][7] diretiva.</span><span class="sxs-lookup"><span data-stu-id="b5f04-365">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="b5f04-366">O exemplo a seguir aplica o [ `[Authorize]` atributo](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) à classe de componente:</span><span class="sxs-lookup"><span data-stu-id="b5f04-366">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="b5f04-367">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="b5f04-367">Conditional HTML element attributes</span></span>

<span data-ttu-id="b5f04-368">Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="b5f04-368">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="b5f04-369">Se o valor for `false` ou `null` , o atributo não será renderizado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-369">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="b5f04-370">Se o valor é `true` , o atributo é processado minimizado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-370">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="b5f04-371">No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="b5f04-371">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="b5f04-372">Se `IsCompleted` for `true` , a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="b5f04-372">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="b5f04-373">Se `IsCompleted` for `false` , a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="b5f04-373">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="b5f04-374">Para obter mais informações, consulte [ Razor referência de sintaxe para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b5f04-374">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="b5f04-375">Alguns atributos HTML, como [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , não funcionam corretamente quando o tipo .net é um `bool` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-375">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="b5f04-376">Nesses casos, use um `string` tipo em vez de um `bool` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-376">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="b5f04-377">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="b5f04-377">Raw HTML</span></span>

<span data-ttu-id="b5f04-378">Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="b5f04-378">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="b5f04-379">Para renderizar HTML bruto, empacote o conteúdo HTML em um `MarkupString` valor.</span><span class="sxs-lookup"><span data-stu-id="b5f04-379">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="b5f04-380">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="b5f04-380">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="b5f04-381">O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="b5f04-381">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="b5f04-382">O exemplo a seguir mostra como usar o `MarkupString` tipo para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="b5f04-382">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="b5f04-383">Razor modelo</span><span class="sxs-lookup"><span data-stu-id="b5f04-383">Razor templates</span></span>

<span data-ttu-id="b5f04-384">Os fragmentos de renderização podem ser definidos usando a Razor sintaxe do modelo.</span><span class="sxs-lookup"><span data-stu-id="b5f04-384">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="b5f04-385">Razor os modelos são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="b5f04-385">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="b5f04-386">O exemplo a seguir ilustra como especificar <xref:Microsoft.AspNetCore.Components.RenderFragment> e <xref:Microsoft.AspNetCore.Components.RenderFragment%601> valores e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-386">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="b5f04-387">Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="b5f04-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="b5f04-388">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="b5f04-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="b5f04-389">Ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="b5f04-389">Static assets</span></span>

<span data-ttu-id="b5f04-390">Blazorsegue a Convenção de ASP.NET Core aplicativos que posicionam ativos estáticos na [ `web root (wwwroot)` pasta](xref:fundamentals/index#web-root)do projeto.</span><span class="sxs-lookup"><span data-stu-id="b5f04-390">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="b5f04-391">Use um caminho relativo de base ( `/` ) para se referir à raiz da Web para um ativo estático.</span><span class="sxs-lookup"><span data-stu-id="b5f04-391">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="b5f04-392">No exemplo a seguir, `logo.png` está localizado fisicamente na `{PROJECT ROOT}/wwwroot/images` pasta:</span><span class="sxs-lookup"><span data-stu-id="b5f04-392">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="b5f04-393">Razor os componentes **não** dão suporte a notação de barra de til ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="b5f04-393">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="b5f04-394">Para obter informações sobre como definir o caminho base de um aplicativo, consulte <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="b5f04-394">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="b5f04-395">Não há suporte para auxiliares de marcas nos componentes</span><span class="sxs-lookup"><span data-stu-id="b5f04-395">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="b5f04-396">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) Não tem suporte em Razor componentes ( `.razor` arquivos).</span><span class="sxs-lookup"><span data-stu-id="b5f04-396">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="b5f04-397">Para fornecer a funcionalidade do tipo auxiliar de marca no Blazor , crie um componente com a mesma funcionalidade que o auxiliar de marca e use o componente em vez disso.</span><span class="sxs-lookup"><span data-stu-id="b5f04-397">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="b5f04-398">Imagens SVG (gráficos vetoriais escaláveis)</span><span class="sxs-lookup"><span data-stu-id="b5f04-398">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="b5f04-399">Como Blazor o renderiza as imagens html, com suporte para navegadores, incluindo imagens de vetores escalonáveis (SVG) `.svg` , há suporte por meio da `<img>` marca:</span><span class="sxs-lookup"><span data-stu-id="b5f04-399">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="b5f04-400">Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos ( `.css` ):</span><span class="sxs-lookup"><span data-stu-id="b5f04-400">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="b5f04-401">No entanto, a marcação SVG embutida não tem suporte em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="b5f04-401">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="b5f04-402">Se você posicionar uma `<svg>` marca diretamente em um arquivo de componente ( `.razor` ), a renderização básica da imagem terá suporte, mas muitos cenários avançados ainda não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="b5f04-402">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="b5f04-403">Por exemplo, as `<use>` marcas não são respeitadas atualmente e [`@bind`][10] não podem ser usadas com algumas marcas SVG.</span><span class="sxs-lookup"><span data-stu-id="b5f04-403">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="b5f04-404">Para obter mais informações, consulte [suporte a SVG em Blazor (dotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="b5f04-404">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="b5f04-405">Comportamento de renderização de espaço em branco</span><span class="sxs-lookup"><span data-stu-id="b5f04-405">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b5f04-406">A menos que a [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) diretiva seja usada com um valor de `true` , espaço em branco extra será removido por padrão se:</span><span class="sxs-lookup"><span data-stu-id="b5f04-406">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="b5f04-407">À esquerda ou à direita dentro de um elemento.</span><span class="sxs-lookup"><span data-stu-id="b5f04-407">Leading or trailing within an element.</span></span>
* <span data-ttu-id="b5f04-408">À esquerda ou à direita dentro de um `RenderFragment` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b5f04-408">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="b5f04-409">Por exemplo, conteúdo filho passado para outro componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-409">For example, child content passed to another component.</span></span>
* <span data-ttu-id="b5f04-410">Ele precede ou segue um bloco de código C#, como `@if` ou `@foreach` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-410">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="b5f04-411">A remoção de espaço em branco pode afetar a saída renderizada ao usar uma regra de CSS, como `white-space: pre` .</span><span class="sxs-lookup"><span data-stu-id="b5f04-411">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="b5f04-412">Para desabilitar essa otimização de desempenho e preservar o espaço em branco, execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="b5f04-412">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="b5f04-413">Adicione a `@preservewhitespace true` diretiva na parte superior do `.razor` arquivo para aplicar a preferência a um componente específico.</span><span class="sxs-lookup"><span data-stu-id="b5f04-413">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="b5f04-414">Adicione a `@preservewhitespace true` diretiva dentro de um `_Imports.razor` arquivo para aplicar a preferência a um subdiretório inteiro ou a todo o projeto.</span><span class="sxs-lookup"><span data-stu-id="b5f04-414">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="b5f04-415">Na maioria dos casos, nenhuma ação é necessária, pois os aplicativos normalmente continuam a se comportar normalmente (mas com mais rapidez).</span><span class="sxs-lookup"><span data-stu-id="b5f04-415">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="b5f04-416">Se a remoção de espaço em branco causar qualquer problema para um componente específico, use- `@preservewhitespace true` o nesse componente para desabilitar essa otimização.</span><span class="sxs-lookup"><span data-stu-id="b5f04-416">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b5f04-417">O espaço em branco é mantido no código-fonte de um componente.</span><span class="sxs-lookup"><span data-stu-id="b5f04-417">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="b5f04-418">Texto somente em espaços em branco é renderizado no Modelo de Objeto do Documento do navegador (DOM) mesmo quando não há nenhum efeito visual.</span><span class="sxs-lookup"><span data-stu-id="b5f04-418">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="b5f04-419">Considere o seguinte Razor código de componente:</span><span class="sxs-lookup"><span data-stu-id="b5f04-419">Consider the following Razor component code:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="b5f04-420">O exemplo anterior processa o seguinte espaço em branco desnecessário:</span><span class="sxs-lookup"><span data-stu-id="b5f04-420">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="b5f04-421">Fora do `@foreach` bloco de código.</span><span class="sxs-lookup"><span data-stu-id="b5f04-421">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="b5f04-422">Em volta do `<li>` elemento.</span><span class="sxs-lookup"><span data-stu-id="b5f04-422">Around the `<li>` element.</span></span>
* <span data-ttu-id="b5f04-423">Em volta da `@item.Text` saída.</span><span class="sxs-lookup"><span data-stu-id="b5f04-423">Around the `@item.Text` output.</span></span>

<span data-ttu-id="b5f04-424">Uma lista contendo 100 itens resulta em 402 áreas de espaço em branco, e nenhum espaço em branco extra afeta visualmente a saída renderizada.</span><span class="sxs-lookup"><span data-stu-id="b5f04-424">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="b5f04-425">Ao renderizar HTML estático para componentes, o espaço em branco dentro de uma marca não é preservado.</span><span class="sxs-lookup"><span data-stu-id="b5f04-425">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="b5f04-426">Por exemplo, exiba a origem do seguinte componente na saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="b5f04-426">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="b5f04-427">O espaço em branco não é preservado da Razor marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="b5f04-427">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b5f04-428">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b5f04-428">Additional resources</span></span>

* <span data-ttu-id="b5f04-429"><xref:blazor/security/server/threat-mitigation>: Inclui diretrizes sobre a criação de Blazor Server aplicativos que devem combater o esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="b5f04-429"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[2]: <xref:mvc/views/razor#using> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[3]: <xref:mvc/views/razor#attributes> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[4]: <xref:mvc/views/razor#ref> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[5]: <xref:mvc/views/razor#key> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[6]: <xref:mvc/views/razor#inherits> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[7]: <xref:mvc/views/razor#attribute> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[8]: <xref:mvc/views/razor#namespace> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[9]: <xref:mvc/views/razor#page> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
[10]: <xref:mvc/views/razor#bind> "::: no-Loc (Razor)::: referência de sintaxe para ASP.NET Core"
