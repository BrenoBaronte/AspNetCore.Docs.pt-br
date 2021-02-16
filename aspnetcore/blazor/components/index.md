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
ms.openlocfilehash: 7b4438b4003916488c17d389b9817b5e09d1086c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536214"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="04153-103">Criar e usar componentes de ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="04153-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="04153-104">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04153-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="04153-105">Blazor os aplicativos são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="04153-105">Blazor apps are built using *components*.</span></span> <span data-ttu-id="04153-106">Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="04153-106">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="04153-107">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="04153-107">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="04153-108">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="04153-108">Components are flexible and lightweight.</span></span> <span data-ttu-id="04153-109">Eles podem ser aninhados, reutilizados e compartilhados entre projetos.</span><span class="sxs-lookup"><span data-stu-id="04153-109">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="04153-110">Classes de componente</span><span class="sxs-lookup"><span data-stu-id="04153-110">Component classes</span></span>

<span data-ttu-id="04153-111">Os componentes são implementados em [Razor](xref:mvc/views/razor) arquivos de componente ( `.razor` ) usando uma combinação de C# e marcação HTML.</span><span class="sxs-lookup"><span data-stu-id="04153-111">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="04153-112">Um componente no Blazor é conhecido formalmente como um *Razor componente*.</span><span class="sxs-lookup"><span data-stu-id="04153-112">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="04153-113">Sintaxe de Razor</span><span class="sxs-lookup"><span data-stu-id="04153-113">Razor syntax</span></span>

<span data-ttu-id="04153-114">Razor os componentes em Blazor aplicativos usam extensivamente a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="04153-114">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="04153-115">Se você não estiver familiarizado com a Razor linguagem de marcação, é recomendável ler a [ Razor referência de sintaxe para ASP.NET Core](xref:mvc/views/razor) antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="04153-115">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="04153-116">Ao acessar o conteúdo em Razor sintaxe, preste atenção especial às seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="04153-116">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="04153-117">[Diretivas](xref:mvc/views/razor#directives): `@` -palavras-chave reservadas prefixadas que normalmente alteram a maneira como a marcação de componente é analisada ou funciona.</span><span class="sxs-lookup"><span data-stu-id="04153-117">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="04153-118">[Atributos de diretiva](xref:mvc/views/razor#directive-attributes): `@` -palavras-chave reservadas prefixadas que normalmente alteram a maneira como os elementos de componente são analisados ou funcionam.</span><span class="sxs-lookup"><span data-stu-id="04153-118">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="04153-119">Nomes</span><span class="sxs-lookup"><span data-stu-id="04153-119">Names</span></span>

<span data-ttu-id="04153-120">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="04153-120">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="04153-121">Por exemplo, `MyCoolComponent.razor` é válido e `myCoolComponent.razor` é inválido.</span><span class="sxs-lookup"><span data-stu-id="04153-121">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="04153-122">Roteamento</span><span class="sxs-lookup"><span data-stu-id="04153-122">Routing</span></span>

<span data-ttu-id="04153-123">O roteamento no Blazor é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04153-123">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="04153-124">Quando um Razor arquivo com uma [`@page`][9] diretiva é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="04153-124">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="04153-125">Em tempo de execução, o roteador procura classes de componentes com um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="04153-125">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="04153-126">Para obter mais informações, consulte <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="04153-126">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="04153-127">Marcação</span><span class="sxs-lookup"><span data-stu-id="04153-127">Markup</span></span>

<span data-ttu-id="04153-128">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="04153-128">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="04153-129">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe C# inserida chamada *Razor* .</span><span class="sxs-lookup"><span data-stu-id="04153-129">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="04153-130">Quando um aplicativo é compilado, a marcação HTML e a lógica de renderização C# são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="04153-130">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="04153-131">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="04153-131">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="04153-132">Os membros da classe de componente são definidos em um [`@code`][1] bloco.</span><span class="sxs-lookup"><span data-stu-id="04153-132">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="04153-133">No [`@code`][1] bloco, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="04153-133">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="04153-134">Mais de um [`@code`][1] bloco é permitido.</span><span class="sxs-lookup"><span data-stu-id="04153-134">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="04153-135">Os membros do componente podem ser usados como parte da lógica de renderização do componente usando expressões C# que começam com `@` .</span><span class="sxs-lookup"><span data-stu-id="04153-135">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="04153-136">Por exemplo, um campo C# é renderizado pela prefixação `@` para o nome do campo.</span><span class="sxs-lookup"><span data-stu-id="04153-136">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="04153-137">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="04153-137">The following example evaluates and renders:</span></span>

* <span data-ttu-id="04153-138">`headingFontStyle` para o valor da propriedade de CSS para `font-style` .</span><span class="sxs-lookup"><span data-stu-id="04153-138">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="04153-139">`headingText` para o conteúdo do `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="04153-139">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="04153-140">Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="04153-140">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="04153-141">Blazor em seguida, compara a nova árvore de renderização com a anterior e aplica quaisquer modificações ao Modelo de Objeto do Documento do navegador (DOM).</span><span class="sxs-lookup"><span data-stu-id="04153-141">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="04153-142">Detalhes adicionais são fornecidos no <xref:blazor/components/rendering> .</span><span class="sxs-lookup"><span data-stu-id="04153-142">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="04153-143">Os componentes são classes C# comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="04153-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="04153-144">Os componentes que produzem páginas da Web geralmente residem na `Pages` pasta.</span><span class="sxs-lookup"><span data-stu-id="04153-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="04153-145">Os componentes que não são de página são frequentemente colocados na `Shared` pasta ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="04153-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="04153-146">Namespaces</span><span class="sxs-lookup"><span data-stu-id="04153-146">Namespaces</span></span>

<span data-ttu-id="04153-147">Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e do local do componente (pasta) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04153-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="04153-148">Se o namespace raiz do aplicativo for `BlazorSample` e o `Counter` componente residir na `Pages` pasta:</span><span class="sxs-lookup"><span data-stu-id="04153-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="04153-149">O `Counter` namespace do componente é `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="04153-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="04153-150">O nome do tipo totalmente qualificado do componente é `BlazorSample.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="04153-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="04153-151">Para pastas personalizadas que contêm componentes, adicione uma [`@using`][2] diretiva ao componente pai ou ao arquivo do aplicativo `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="04153-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="04153-152">O exemplo a seguir torna os componentes na `Components` pasta disponíveis:</span><span class="sxs-lookup"><span data-stu-id="04153-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="04153-153">Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não exige a [`@using`][2] diretiva:</span><span class="sxs-lookup"><span data-stu-id="04153-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="04153-154">O namespace de um componente criado com o Razor é baseado em (em ordem de prioridade):</span><span class="sxs-lookup"><span data-stu-id="04153-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="04153-155">[`@namespace`][8] designação na Razor marcação File ( `.razor` ) ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="04153-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="04153-156">O projeto `RootNamespace` no arquivo de projeto ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="04153-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="04153-157">O nome do projeto, extraído do nome de arquivo do arquivo de projeto ( `.csproj` ) e o caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="04153-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="04153-158">Por exemplo, a estrutura resolve `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) para o namespace `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="04153-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="04153-159">Os componentes seguem regras de associação de nome C#.</span><span class="sxs-lookup"><span data-stu-id="04153-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="04153-160">Para o `Index` componente neste exemplo, os componentes no escopo são todos os componentes:</span><span class="sxs-lookup"><span data-stu-id="04153-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="04153-161">Na mesma pasta, `Pages` .</span><span class="sxs-lookup"><span data-stu-id="04153-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="04153-162">Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.</span><span class="sxs-lookup"><span data-stu-id="04153-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="04153-163">`global::`Não há suporte para a qualificação.</span><span class="sxs-lookup"><span data-stu-id="04153-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="04153-164">[`using`](/dotnet/csharp/language-reference/keywords/using-statement)Não há suporte para a importação de componentes com instruções com alias (por exemplo, `@using Foo = Bar` ).</span><span class="sxs-lookup"><span data-stu-id="04153-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="04153-165">Não há suporte para nomes parcialmente qualificados.</span><span class="sxs-lookup"><span data-stu-id="04153-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="04153-166">Por exemplo, `@using BlazorSample` não há suporte para adicionar e referenciar o `NavMenu` componente ( `NavMenu.razor` ) com `<Shared.NavMenu></Shared.NavMenu>` .</span><span class="sxs-lookup"><span data-stu-id="04153-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="04153-167">Suporte de classe parcial</span><span class="sxs-lookup"><span data-stu-id="04153-167">Partial class support</span></span>

<span data-ttu-id="04153-168">Razor os componentes são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="04153-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="04153-169">Razor os componentes são criados usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="04153-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="04153-170">O código C# é definido em um [`@code`][1] bloco com marcação e Razor código HTML em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="04153-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="04153-171">Blazor os modelos definem seus Razor componentes usando essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="04153-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="04153-172">O código C# é colocado em um arquivo code-behind definido como uma classe parcial.</span><span class="sxs-lookup"><span data-stu-id="04153-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="04153-173">O exemplo a seguir mostra o `Counter` componente padrão com um [`@code`][1] bloco em um aplicativo gerado a partir de um Blazor modelo.</span><span class="sxs-lookup"><span data-stu-id="04153-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="04153-174">A marcação HTML, o Razor código e o código C# estão no mesmo arquivo:</span><span class="sxs-lookup"><span data-stu-id="04153-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="04153-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="04153-176">O `Counter` componente também pode ser criado usando um arquivo code-behind com uma classe parcial:</span><span class="sxs-lookup"><span data-stu-id="04153-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="04153-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="04153-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="04153-178">`Counter.razor.cs`:</span></span>

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

<span data-ttu-id="04153-179">Adicione quaisquer namespaces necessários ao arquivo de classe parcial, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="04153-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="04153-180">Os namespaces típicos usados pelos Razor componentes incluem:</span><span class="sxs-lookup"><span data-stu-id="04153-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="04153-181">[`@using`][2] as diretivas no `_Imports.razor` arquivo são aplicadas somente a Razor arquivos ( `.razor` ), não a arquivos C# ( `.cs` ).</span><span class="sxs-lookup"><span data-stu-id="04153-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="04153-182">Especificar uma classe base</span><span class="sxs-lookup"><span data-stu-id="04153-182">Specify a base class</span></span>

<span data-ttu-id="04153-183">A [`@inherits`][6] diretiva pode ser usada para especificar uma classe base para um componente.</span><span class="sxs-lookup"><span data-stu-id="04153-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="04153-184">O exemplo a seguir mostra como um componente pode herdar uma classe base, `BlazorRocksBase` ,, para fornecer as propriedades e os métodos do componente.</span><span class="sxs-lookup"><span data-stu-id="04153-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="04153-185">A classe base deve derivar de <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="04153-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="04153-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="04153-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="04153-187">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="04153-188">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="04153-188">Use components</span></span>

<span data-ttu-id="04153-189">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="04153-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="04153-190">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="04153-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="04153-191">A seguinte marcação no `Pages/Index.razor` renderiza uma `HeadingComponent` instância:</span><span class="sxs-lookup"><span data-stu-id="04153-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="04153-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="04153-193">Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="04153-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="04153-194">A adição de uma [`@using`][2] diretiva para o namespace do componente torna o componente disponível, o que resolve o aviso.</span><span class="sxs-lookup"><span data-stu-id="04153-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="04153-195">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="04153-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="04153-196">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="04153-196">Route parameters</span></span>

<span data-ttu-id="04153-197">Os componentes podem receber parâmetros de rota do modelo de rota fornecido na [`@page`][9] diretiva.</span><span class="sxs-lookup"><span data-stu-id="04153-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="04153-198">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.</span><span class="sxs-lookup"><span data-stu-id="04153-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="04153-199">Há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="04153-199">Optional parameters are supported.</span></span> <span data-ttu-id="04153-200">No exemplo a seguir, o `text` parâmetro opcional atribui o valor do segmento de rota à propriedade do componente `Text` .</span><span class="sxs-lookup"><span data-stu-id="04153-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="04153-201">Se o segmento não estiver presente, o valor de `Text` será definido como `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="04153-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="04153-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="04153-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="04153-204">Não há suporte para parâmetros opcionais, portanto, duas [`@page`][9] diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="04153-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="04153-205">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04153-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="04153-206">A segunda [`@page`][9] diretiva recebe o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="04153-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="04153-207">Para obter informações sobre como capturar todos os parâmetros de rota ( `{*pageRoute}` ), que capturam caminhos entre vários limites de pasta, consulte <xref:blazor/fundamentals/routing#catch-all-route-parameters> .</span><span class="sxs-lookup"><span data-stu-id="04153-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="04153-208">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="04153-208">Component parameters</span></span>

<span data-ttu-id="04153-209">Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas simples ou complexas na classe de componente com o [ `[Parameter]` atributo](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="04153-209">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="04153-210">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="04153-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="04153-211">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-211">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="04153-212">Os parâmetros de componente podem ser atribuídos a um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="04153-212">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="04153-213">No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da `Title` Propriedade do `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="04153-213">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="04153-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-214">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="04153-215">Atribua campos, propriedades e métodos em C# a parâmetros de componente como valores de atributo HTML usando o [ Razor `@` símbolo reservado do](xref:mvc/views/razor#razor-syntax):</span><span class="sxs-lookup"><span data-stu-id="04153-215">Assign C# fields, properties, and methods to component parameters as HTML attribute values using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="04153-216">Para atribuir o campo, a propriedade ou o método de um componente pai ao parâmetro de um componente filho, Prefixe o campo, a propriedade ou o nome do método com o `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="04153-216">To assign a parent component's field, property, or method to a child component's parameter, prefix the field, property, or method name with the `@` symbol.</span></span> <span data-ttu-id="04153-217">Para atribuir o resultado de uma [expressão C# implícita](xref:mvc/views/razor#implicit-razor-expressions) a um parâmetro, Prefixe a expressão implícita com um `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="04153-217">To assign the result of an [implicit C# expression](xref:mvc/views/razor#implicit-razor-expressions) to a parameter, prefix the implicit expression with an `@` symbol.</span></span>

  <span data-ttu-id="04153-218">O componente pai a seguir exibe quatro instâncias do `ChildComponent` componente anterior e define seus `Title` valores de parâmetro como:</span><span class="sxs-lookup"><span data-stu-id="04153-218">The following parent component displays four instances of the preceding `ChildComponent` component and sets their `Title` parameter values to:</span></span>

  * <span data-ttu-id="04153-219">O valor do `title` campo.</span><span class="sxs-lookup"><span data-stu-id="04153-219">The value of the `title` field.</span></span>
  * <span data-ttu-id="04153-220">O resultado do `GetTitle` método C#.</span><span class="sxs-lookup"><span data-stu-id="04153-220">The result of the `GetTitle` C# method.</span></span>
  * <span data-ttu-id="04153-221">A data local atual em formato longo com <xref:System.DateTime.ToLongDateString%2A> .</span><span class="sxs-lookup"><span data-stu-id="04153-221">The current local date in long format with <xref:System.DateTime.ToLongDateString%2A>.</span></span>
  * <span data-ttu-id="04153-222">A `person` Propriedade do objeto `Name` .</span><span class="sxs-lookup"><span data-stu-id="04153-222">The `person` object's `Name` property.</span></span>

  <span data-ttu-id="04153-223">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-223">`Pages/ParentComponent.razor`:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from field.
  </ChildComponent>
  
  <ChildComponent Title="@GetTitle()">
      Title from method.
  </ChildComponent>
  
  <ChildComponent Title="@DateTime.Now.ToLongDateString()">
      Title from implicit Razor expression.
  </ChildComponent>
  
  <ChildComponent Title="@person.Name">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title = "Panel Title from Parent";
      private Person person = new Person();
      
      private string GetTitle()
      {
          return "Panel Title from Parent";
      }
      
      private class Person
      {
          public string Name { get; set; } = "Dr. Who";
      }
  }
  ```
  
  <span data-ttu-id="04153-224">Ao contrário das Razor páginas ( `.cshtml` ), Blazor não é possível executar o trabalho assíncrono em uma Razor expressão durante a renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="04153-224">Unlike in Razor pages (`.cshtml`), Blazor can't perform asynchronous work in a Razor expression while rendering a component.</span></span> <span data-ttu-id="04153-225">Isso ocorre porque o Blazor é projetado para renderizar UIs interativas.</span><span class="sxs-lookup"><span data-stu-id="04153-225">This is because Blazor is designed for rendering interactive UIs.</span></span> <span data-ttu-id="04153-226">Em uma interface do usuário interativa, a tela sempre deve exibir algo, portanto, não faz sentido bloquear o fluxo de renderização.</span><span class="sxs-lookup"><span data-stu-id="04153-226">In an interactive UI, the screen must always display something, so it doesn't make sense to block the rendering flow.</span></span> <span data-ttu-id="04153-227">Em vez disso, o trabalho assíncrono é executado durante um dos [eventos de ciclo de vida assíncrono](xref:blazor/components/lifecycle).</span><span class="sxs-lookup"><span data-stu-id="04153-227">Instead, asynchronous work is performed during one of the [asynchronous lifecycle events](xref:blazor/components/lifecycle).</span></span> <span data-ttu-id="04153-228">Após cada evento de ciclo de vida assíncrono, o componente pode ser renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="04153-228">After each asynchronous lifecycle event, the component may render again.</span></span> <span data-ttu-id="04153-229">Razor **Não** há suporte para a sintaxe a seguir:</span><span class="sxs-lookup"><span data-stu-id="04153-229">The following Razor syntax is **not** supported:</span></span>
  
  ```razor
  <ChildComponent Title="@await ...">
      ...
  </ChildComponent>
  ```
  
  <span data-ttu-id="04153-230">O código no exemplo anterior gera um *erro do compilador* se o aplicativo for compilado:</span><span class="sxs-lookup"><span data-stu-id="04153-230">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="04153-231">O operador ' Await ' só pode ser usado em um método assíncrono.</span><span class="sxs-lookup"><span data-stu-id="04153-231">The 'await' operator can only be used within an async method.</span></span> <span data-ttu-id="04153-232">Considere marcar esse método com o modificador ' async ' e alterar seu tipo de retorno para ' Task '.</span><span class="sxs-lookup"><span data-stu-id="04153-232">Consider marking this method with the 'async' modifier and changing its return type to 'Task'.</span></span>

  <span data-ttu-id="04153-233">Para obter um valor para o `Title` parâmetro no exemplo anterior Asychronously, o componente pode usar o [ `OnInitializedAsync` evento de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods), como demonstra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="04153-233">To obtain a value for the `Title` parameter in the preceding example asychronously, the component can use the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods), as the following example demonstrates:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title;
      
      protected override async Task OnInitializedAsync()
      {
          title = await ...;
      }
  }
  ```
  
* <span data-ttu-id="04153-234">Para atribuir o resultado de uma [expressão C# explícita](xref:mvc/views/razor#explicit-razor-expressions) no componente pai ao parâmetro de um componente filho, coloque a expressão entre parênteses com um `@` prefixo de símbolo.</span><span class="sxs-lookup"><span data-stu-id="04153-234">To assign the result of an [explicit C# expression](xref:mvc/views/razor#explicit-razor-expressions) in the parent component to a child component's parameter, surround the expression in parentheses with an `@` symbol prefix.</span></span>

  <span data-ttu-id="04153-235">O componente filho a seguir tem um <xref:System.DateTime> parâmetro de componente, `ShowItemsSinceDate` .</span><span class="sxs-lookup"><span data-stu-id="04153-235">The following child component has a <xref:System.DateTime> component parameter, `ShowItemsSinceDate`.</span></span>
  
  <span data-ttu-id="04153-236">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-236">`Shared/ChildComponent.razor`:</span></span>
  
  ```razor
  <div class="panel panel-default">
      <div class="panel-heading">Explicit DateTime Expression Example</div>
      <div class="panel-body">
          <p>@ChildContent</p>
          <p>One week ago date: @ShowItemsSinceDate</p>
      </div>
  </div>

  @code {
      [Parameter]
      public DateTime ShowItemsSinceDate { get; set; }

      [Parameter]
      public RenderFragment ChildContent { get; set; }
  }
  ```
  
  <span data-ttu-id="04153-237">O componente pai a seguir calcula uma data com uma expressão C# explícita que é uma semana no passado para atribuição ao parâmetro do filho `ShowItemsSinceDate` .</span><span class="sxs-lookup"><span data-stu-id="04153-237">The following parent component calculates a date with an explicit C# expression that's one week in the past for assignment to the child's `ShowItemsSinceDate` parameter.</span></span>
  
  <span data-ttu-id="04153-238">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-238">`Pages/ParentComponent.razor`:</span></span>

  ```razor
  <ChildComponent ShowItemsSinceDate="@(DateTime.Now - TimeSpan.FromDays(7))">
      Title from explicit Razor expression.
  </ChildComponent>
  ```

  <span data-ttu-id="04153-239">**Não** há suporte para o uso de uma expressão explícita para concatenar o texto com um resultado de expressão para a atribuição a um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04153-239">Use of an explicit expression to concatenate text with an expression result for assignment to a parameter is **not** supported.</span></span> <span data-ttu-id="04153-240">O exemplo a seguir procura concatenar o texto "SKU-" com um número de estoque do produto ( `SKU` propriedade, "unidade de manutenção de estoque") fornecido pelo objeto de um componente pai `product` .</span><span class="sxs-lookup"><span data-stu-id="04153-240">The following example seeks to concatenate the text "SKU-" with a product stock number (`SKU` property, "Stock Keeping Unit") provided by a parent component's `product` object.</span></span> <span data-ttu-id="04153-241">Embora essa sintaxe tenha suporte em uma Razor página ( `.cshtml` ), ela não é válida para atribuição ao parâmetro do filho `Title` .</span><span class="sxs-lookup"><span data-stu-id="04153-241">Although this syntax is supported in a Razor page (`.cshtml`), it isn't valid for assignment to the child's `Title` parameter.</span></span>
  
  ```razor
  <ChildComponent Title="SKU-@(product.SKU)">
      Title from composed Razor expression. This doesn't compile.
  </ChildComponent>
  ```
  
  <span data-ttu-id="04153-242">O código no exemplo anterior gera um *erro do compilador* se o aplicativo for compilado:</span><span class="sxs-lookup"><span data-stu-id="04153-242">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="04153-243">Atributos de componente não dão suporte a conteúdo complexo (C# e marcação combinados).</span><span class="sxs-lookup"><span data-stu-id="04153-243">Component attributes do not support complex content (mixed C# and markup).</span></span>
  
  <span data-ttu-id="04153-244">Para dar suporte à atribuição de um valor composto, use um método, campo ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="04153-244">To support the assignment of a composed value, use a method, field, or property.</span></span> <span data-ttu-id="04153-245">O exemplo a seguir executa o concatination de "SKU-" e o número de estoque de um produto no método C# `GetTitle` :</span><span class="sxs-lookup"><span data-stu-id="04153-245">The following example performs the concatination of "SKU-" and a product's stock number in the C# method `GetTitle`:</span></span>
  
  ```razor
  <ChildComponent Title="@GetTitle()">
      Composed title from method.
  </ChildComponent>
  
  @code {
      private Product product = new Product();

      private string GetTitle() => $"SKU-{product.SKU}";
      
      private class Product
      {
          public string SKU { get; set; } = "12345";
      }
  }
  ```
  
<span data-ttu-id="04153-246">Para obter mais informações, consulte [ Razor referência de sintaxe para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="04153-246">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="04153-247">Não crie componentes que gravam em seus próprios *parâmetros de componente*, use um campo particular em vez disso.</span><span class="sxs-lookup"><span data-stu-id="04153-247">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="04153-248">Para obter mais informações, consulte a seção [parâmetros substituídos](#overwritten-parameters) .</span><span class="sxs-lookup"><span data-stu-id="04153-248">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

#### <a name="component-parameters-should-be-auto-properties"></a><span data-ttu-id="04153-249">Os parâmetros de componente devem ser Propriedades automáticas</span><span class="sxs-lookup"><span data-stu-id="04153-249">Component parameters should be auto-properties</span></span>

<span data-ttu-id="04153-250">Os parâmetros de componente devem ser declarados como *Propriedades automáticas*, o que significa que eles não devem conter lógica personalizada em seus getters ou setters.</span><span class="sxs-lookup"><span data-stu-id="04153-250">Component parameters should be declared as *auto-properties*, meaning that they shouldn't contain custom logic in their getters or setters.</span></span> <span data-ttu-id="04153-251">Por exemplo, a seguinte `StartData` propriedade é uma propriedade automática:</span><span class="sxs-lookup"><span data-stu-id="04153-251">For example, the following `StartData` property is an auto-property:</span></span>

```csharp
[Parameter]
public DateTime StartData { get; set; }
```

<span data-ttu-id="04153-252">Não coloque a lógica personalizada no `get` `set` acessador ou porque os parâmetros do componente são puramente destinados ao uso como um canal para um componente pai para o fluxo de informações para um componente filho.</span><span class="sxs-lookup"><span data-stu-id="04153-252">Don't place custom logic in the `get` or `set` accessor because component parameters are purely intended for use as a channel for a parent component to flow information to a child component.</span></span> <span data-ttu-id="04153-253">Se um setter de uma propriedade de componente filho contiver lógica que causa a rerenderização do componente pai, um loop de renderização infinito resultará em resultados.</span><span class="sxs-lookup"><span data-stu-id="04153-253">If a setter of a child component property contains logic that causes rerendering of the parent component, an infinite rendering loop results.</span></span>

<span data-ttu-id="04153-254">Se você precisar transformar um valor de parâmetro recebido:</span><span class="sxs-lookup"><span data-stu-id="04153-254">If you need to transform a received parameter value:</span></span>

* <span data-ttu-id="04153-255">Deixe a propriedade de parâmetro como uma propriedade automática pura para representar os dados brutos fornecidos.</span><span class="sxs-lookup"><span data-stu-id="04153-255">Leave the parameter property as a pure auto-property to represent the supplied raw data.</span></span>
* <span data-ttu-id="04153-256">Crie alguma outra propriedade ou método que forneça os dados transformados com base na propriedade de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04153-256">Create some other property or method that supplies the transformed data based on the parameter property.</span></span>

<span data-ttu-id="04153-257">Você pode substituir `OnParametersSetAsync` se quiser transformar um parâmetro recebido sempre que novos dados forem recebidos.</span><span class="sxs-lookup"><span data-stu-id="04153-257">You can override `OnParametersSetAsync` if you want to transform a received parameter each time new data is received.</span></span>

## <a name="child-content"></a><span data-ttu-id="04153-258">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="04153-258">Child content</span></span>

<span data-ttu-id="04153-259">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="04153-259">Components can set the content of another component.</span></span> <span data-ttu-id="04153-260">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.</span><span class="sxs-lookup"><span data-stu-id="04153-260">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="04153-261">No exemplo a seguir, o `ChildComponent` tem uma `ChildContent` propriedade que representa um <xref:Microsoft.AspNetCore.Components.RenderFragment> , que representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="04153-261">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="04153-262">O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="04153-262">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="04153-263">O valor de `ChildContent` é recebido do componente pai e renderizado dentro do painel de inicialização `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="04153-263">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="04153-264">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-264">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="04153-265">A propriedade que recebe o <xref:Microsoft.AspNetCore.Components.RenderFragment> conteúdo deve ser nomeada `ChildContent` por convenção.</span><span class="sxs-lookup"><span data-stu-id="04153-265">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="04153-266">O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das `<ChildComponent>` marcas.</span><span class="sxs-lookup"><span data-stu-id="04153-266">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="04153-267">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-267">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="04153-268">Devido à maneira que Blazor renderiza conteúdo filho, os componentes de renderização dentro de um `for` loop exigirão uma variável de índice local se a variável de loop de incremento for usada no conteúdo do componente filho:</span><span class="sxs-lookup"><span data-stu-id="04153-268">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
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
> <span data-ttu-id="04153-269">Como alternativa, use um `foreach` loop com <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="04153-269">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="04153-270">Para obter informações sobre como um <xref:Microsoft.AspNetCore.Components.RenderFragment> pode ser usado como um modelo para Razor a interface do usuário do componente, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="04153-270">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="04153-271">Atributo nivelamento e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="04153-271">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="04153-272">Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="04153-272">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="04153-273">Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a [`@attributes`][3] Razor diretiva.</span><span class="sxs-lookup"><span data-stu-id="04153-273">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="04153-274">Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="04153-274">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="04153-275">Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que dê suporte a muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="04153-275">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="04153-276">No exemplo a seguir, o primeiro `<input>` elemento ( `id="useIndividualParams"` ) usa parâmetros de componente individuais, enquanto o segundo `<input>` elemento ( `id="useAttributesDict"` ) usa o atributo nivelamento:</span><span class="sxs-lookup"><span data-stu-id="04153-276">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="04153-277">O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="04153-277">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="04153-278">Os elementos renderizados `<input>` usando ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="04153-278">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="04153-279">Para aceitar atributos arbitrários, defina um parâmetro de componente usando o [ `[Parameter]` atributo](xref:Microsoft.AspNetCore.Components.ParameterAttribute) com a <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriedade definida como `true` :</span><span class="sxs-lookup"><span data-stu-id="04153-279">To accept arbitrary attributes, define a component parameter using the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="04153-280">A <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> Propriedade on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04153-280">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="04153-281">Um componente só pode definir um único parâmetro com <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="04153-281">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="04153-282">O tipo de propriedade usado com <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="04153-282">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="04153-283">`IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="04153-283">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="04153-284">A posição de [`@attributes`][3] relativo à posição dos atributos do elemento é importante.</span><span class="sxs-lookup"><span data-stu-id="04153-284">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="04153-285">Quando [`@attributes`][3] são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro).</span><span class="sxs-lookup"><span data-stu-id="04153-285">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="04153-286">Considere o exemplo a seguir de um componente que consome um `Child` componente:</span><span class="sxs-lookup"><span data-stu-id="04153-286">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="04153-287">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-287">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="04153-288">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-288">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="04153-289">O `Child` atributo do componente `extra` é definido à direita de [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="04153-289">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="04153-290">O `Parent` renderizado do componente `<div>` contém `extra="5"` quando passado pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):</span><span class="sxs-lookup"><span data-stu-id="04153-290">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="04153-291">No exemplo a seguir, a ordem de `extra` e [`@attributes`][3] é invertida no `Child` `<div>` :</span><span class="sxs-lookup"><span data-stu-id="04153-291">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="04153-292">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-292">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="04153-293">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="04153-293">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="04153-294">O renderizado `<div>` no `Parent` componente contém `extra="10"` quando passado pelo atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="04153-294">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="04153-295">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="04153-295">Capture references to components</span></span>

<span data-ttu-id="04153-296">As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset` .</span><span class="sxs-lookup"><span data-stu-id="04153-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="04153-297">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="04153-297">To capture a component reference:</span></span>

* <span data-ttu-id="04153-298">Adicione um [`@ref`][4] atributo ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="04153-298">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="04153-299">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="04153-299">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="04153-300">Quando o componente é renderizado, o `loginDialog` campo é populado com a `CustomLoginDialog` instância de componente filho.</span><span class="sxs-lookup"><span data-stu-id="04153-300">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="04153-301">Em seguida, você pode invocar os métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="04153-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="04153-302">A `loginDialog` variável é populada apenas depois que o componente é renderizado e sua saída inclui o `MyLoginDialog` elemento.</span><span class="sxs-lookup"><span data-stu-id="04153-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="04153-303">Até que o componente seja renderizado, não há nada a fazer referência.</span><span class="sxs-lookup"><span data-stu-id="04153-303">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="04153-304">Para manipular referências de componentes após a conclusão da renderização do componente, use os [ `OnAfterRenderAsync` `OnAfterRender` métodos ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="04153-304">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="04153-305">Para usar uma variável de referência com um manipulador de eventos, use uma expressão lambda ou atribua o delegado manipulador de eventos nos [ `OnAfterRenderAsync` `OnAfterRender` métodos ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="04153-305">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="04153-306">Isso garante que a variável de referência seja atribuída antes que o manipulador de eventos seja atribuído.</span><span class="sxs-lookup"><span data-stu-id="04153-306">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="04153-307">Para fazer referência a componentes em um loop, consulte [capturar referências para vários componentes filho semelhantes (dotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="04153-307">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="04153-308">Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), ela não é um recurso de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="04153-308">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="04153-309">As referências de componente não são passadas para o código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="04153-309">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="04153-310">As referências de componente são usadas apenas no código .NET.</span><span class="sxs-lookup"><span data-stu-id="04153-310">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="04153-311">Não **use referências** de componente para converter o estado dos componentes filho.</span><span class="sxs-lookup"><span data-stu-id="04153-311">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="04153-312">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="04153-312">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="04153-313">O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.</span><span class="sxs-lookup"><span data-stu-id="04153-313">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="04153-314">Contexto de sincronização</span><span class="sxs-lookup"><span data-stu-id="04153-314">Synchronization context</span></span>

<span data-ttu-id="04153-315">Blazor usa um contexto de sincronização ( <xref:System.Threading.SynchronizationContext> ) para impor um único thread lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="04153-315">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="04153-316">Os métodos de [ciclo de vida](xref:blazor/components/lifecycle) de um componente e quaisquer retornos de chamada de evento que são gerados pelo Blazor são executados no contexto de sincronização.</span><span class="sxs-lookup"><span data-stu-id="04153-316">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="04153-317">Blazor Servero contexto de sincronização do tenta emular um ambiente de thread único para que ele corresponda ao modelo Webassembly no navegador, que é um thread único.</span><span class="sxs-lookup"><span data-stu-id="04153-317">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="04153-318">Em qualquer momento determinado, o trabalho é executado em exatamente um thread, dando a impressão de um único thread lógico.</span><span class="sxs-lookup"><span data-stu-id="04153-318">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="04153-319">Duas operações não são executadas simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="04153-319">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="04153-320">Evitar chamadas de bloqueio de thread</span><span class="sxs-lookup"><span data-stu-id="04153-320">Avoid thread-blocking calls</span></span>

<span data-ttu-id="04153-321">Em geral, não chame os métodos a seguir.</span><span class="sxs-lookup"><span data-stu-id="04153-321">Generally, don't call the following methods.</span></span> <span data-ttu-id="04153-322">Os métodos a seguir bloqueiam o thread e, portanto, impedem que o aplicativo retome o trabalho até que o subjacente <xref:System.Threading.Tasks.Task> seja concluído:</span><span class="sxs-lookup"><span data-stu-id="04153-322">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="04153-323">Invocar métodos de componente externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="04153-323">Invoke component methods externally to update state</span></span>

<span data-ttu-id="04153-324">No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o `InvokeAsync` método, que é expedido para o Blazor contexto de sincronização.</span><span class="sxs-lookup"><span data-stu-id="04153-324">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="04153-325">Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="04153-325">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="04153-326">Registre o `NotifierService` :</span><span class="sxs-lookup"><span data-stu-id="04153-326">Register the `NotifierService`:</span></span>

* <span data-ttu-id="04153-327">No Blazor WebAssembly , registre o serviço como singleton no `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="04153-327">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="04153-328">No Blazor Server , registre o serviço como escopo em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="04153-328">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="04153-329">Use o `NotifierService` para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="04153-329">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="04153-330">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="04153-330">In the preceding example:</span></span>

* <span data-ttu-id="04153-331">`NotifierService` invoca o método do componente `OnNotify` fora do Blazor contexto de sincronização.</span><span class="sxs-lookup"><span data-stu-id="04153-331">`NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="04153-332">`InvokeAsync` é usado para alternar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="04153-332">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="04153-333">Para obter mais informações, consulte <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="04153-333">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="04153-334">O componente implementa <xref:System.IDisposable> e o `OnNotify` delegado tem uma assinatura cancelada no `Dispose` método, que é chamado pelo Framework quando o componente é Descartado.</span><span class="sxs-lookup"><span data-stu-id="04153-334">The component implements <xref:System.IDisposable>, and the `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="04153-335">Para obter mais informações, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="04153-335">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="04153-336">Use \@ a chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="04153-336">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="04153-337">Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente mudam, o Blazor algoritmo de diferenciação do deve decidir quais elementos ou componentes anteriores podem ser retidos e como os objetos de modelo devem ser mapeados para eles.</span><span class="sxs-lookup"><span data-stu-id="04153-337">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="04153-338">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="04153-338">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="04153-339">Considere o seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="04153-339">Consider the following example:</span></span>

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

<span data-ttu-id="04153-340">O conteúdo da `People` coleção pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="04153-340">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="04153-341">Quando o componente é rerenderizado, o `<DetailsEditor>` componente pode ser alterado para receber `Details` valores de parâmetro diferentes.</span><span class="sxs-lookup"><span data-stu-id="04153-341">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="04153-342">Isso pode causar um reprocessamento mais complexo do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="04153-342">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="04153-343">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.</span><span class="sxs-lookup"><span data-stu-id="04153-343">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="04153-344">O processo de mapeamento pode ser controlado com o [`@key`][5] atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="04153-344">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="04153-345">[`@key`][5] faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="04153-345">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="04153-346">Quando a `People` coleção é alterada, o algoritmo diff mantém a associação entre `<DetailsEditor>` instâncias e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="04153-346">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="04153-347">Se um `Person` for excluído da `People` lista, somente a instância correspondente `<DetailsEditor>` será REmovida da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="04153-347">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="04153-348">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="04153-348">Other instances are left unchanged.</span></span>
* <span data-ttu-id="04153-349">Se um `Person` for inserido em alguma posição na lista, uma nova `<DetailsEditor>` instância será inserida na posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="04153-349">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="04153-350">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="04153-350">Other instances are left unchanged.</span></span>
* <span data-ttu-id="04153-351">Se `Person` as entradas forem reordenadas, as `<DetailsEditor>` instâncias correspondentes serão preservadas e reordenadas na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="04153-351">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="04153-352">Em alguns cenários, o uso de [`@key`][5] minimiza a complexidade da rerenderização e evita possíveis problemas com partes com estado da alteração do dom, como a posição do foco.</span><span class="sxs-lookup"><span data-stu-id="04153-352">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="04153-353">As chaves são locais para cada elemento ou componente de contêiner.</span><span class="sxs-lookup"><span data-stu-id="04153-353">Keys are local to each container element or component.</span></span> <span data-ttu-id="04153-354">As chaves não são comparadas globalmente ao longo do documento.</span><span class="sxs-lookup"><span data-stu-id="04153-354">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="04153-355">Quando usar a \@ chave</span><span class="sxs-lookup"><span data-stu-id="04153-355">When to use \@key</span></span>

<span data-ttu-id="04153-356">Normalmente, faz sentido usar [`@key`][5] sempre que uma lista é renderizada (por exemplo, em um bloco [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) e um valor adequado existe para definir o [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="04153-356">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="04153-357">Você também pode usar [`@key`][5] para evitar a Blazor preservação de uma subárvore de elemento ou componente quando um objeto for alterado:</span><span class="sxs-lookup"><span data-stu-id="04153-357">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="04153-358">Se `@currentPerson` forem alteradas, a [`@key`][5] diretiva de atributo forçará Blazor a descartar todo `<div>` e seus descendentes e recriará a subárvore dentro da interface do usuário com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="04153-358">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="04153-359">Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando houver `@currentPerson` alterações.</span><span class="sxs-lookup"><span data-stu-id="04153-359">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="04153-360">Quando não usar a \@ chave</span><span class="sxs-lookup"><span data-stu-id="04153-360">When not to use \@key</span></span>

<span data-ttu-id="04153-361">Há um custo de desempenho ao comparar com [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="04153-361">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="04153-362">O custo de desempenho não é grande, mas só especifica [`@key`][5] se controlar as regras de preservação de elementos ou componentes beneficiam o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04153-362">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="04153-363">Mesmo que [`@key`][5] não seja usado, Blazor o preserva as instâncias de elemento filho e de componente o máximo possível.</span><span class="sxs-lookup"><span data-stu-id="04153-363">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="04153-364">A única vantagem de usar o [`@key`][5] é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="04153-364">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="04153-365">Quais valores usar para a \@ chave</span><span class="sxs-lookup"><span data-stu-id="04153-365">What values to use for \@key</span></span>

<span data-ttu-id="04153-366">Geralmente, faz sentido fornecer um dos seguintes tipos de valor para [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="04153-366">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="04153-367">Instâncias de objeto de modelo (por exemplo, uma `Person` instância como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="04153-367">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="04153-368">Isso garante a preservação com base na igualdade de referência de objeto.</span><span class="sxs-lookup"><span data-stu-id="04153-368">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="04153-369">Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int` , `string` ou `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="04153-369">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="04153-370">Verifique se os valores usados para [`@key`][5] não conflitam.</span><span class="sxs-lookup"><span data-stu-id="04153-370">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="04153-371">Se valores conflitantes forem detectados no mesmo elemento pai, Blazor o lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="04153-371">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="04153-372">Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="04153-372">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="04153-373">Parâmetros substituídos</span><span class="sxs-lookup"><span data-stu-id="04153-373">Overwritten parameters</span></span>

<span data-ttu-id="04153-374">A Blazor estrutura geralmente impõe uma atribuição de parâmetro pai-para-filho segura:</span><span class="sxs-lookup"><span data-stu-id="04153-374">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="04153-375">Os parâmetros não são substituídos inesperadamente.</span><span class="sxs-lookup"><span data-stu-id="04153-375">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="04153-376">Os efeitos colaterais são minimizados.</span><span class="sxs-lookup"><span data-stu-id="04153-376">Side-effects are minimized.</span></span> <span data-ttu-id="04153-377">Por exemplo, renderizações adicionais são evitadas porque podem criar loops de renderização infinitos.</span><span class="sxs-lookup"><span data-stu-id="04153-377">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="04153-378">Um componente filho recebe novos valores de parâmetro que possivelmente substituem os valores existentes quando o componente pai é rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="04153-378">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="04153-379">Accidentially substituir valores de parâmetro em um componente filho geralmente ocorre ao desenvolver o componente com um ou mais parâmetros associados a dados e o desenvolvedor grava diretamente em um parâmetro no filho:</span><span class="sxs-lookup"><span data-stu-id="04153-379">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="04153-380">O componente filho é renderizado com um ou mais valores de parâmetro do componente pai.</span><span class="sxs-lookup"><span data-stu-id="04153-380">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="04153-381">O filho grava diretamente no valor de um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04153-381">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="04153-382">O componente pai rerenderiza e substitui o valor do parâmetro do filho.</span><span class="sxs-lookup"><span data-stu-id="04153-382">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="04153-383">O potencial para substituir valores de parâmetros também se estende aos setters de Propriedade do componente filho.</span><span class="sxs-lookup"><span data-stu-id="04153-383">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="04153-384">**Nossas diretrizes gerais não criam componentes que gravam diretamente em seus próprios parâmetros.**</span><span class="sxs-lookup"><span data-stu-id="04153-384">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="04153-385">Considere o seguinte componente com falha `Expander` que:</span><span class="sxs-lookup"><span data-stu-id="04153-385">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="04153-386">Renderiza conteúdo filho.</span><span class="sxs-lookup"><span data-stu-id="04153-386">Renders child content.</span></span>
* <span data-ttu-id="04153-387">Alterna a exibição de conteúdo filho com um parâmetro de componente ( `Expanded` ).</span><span class="sxs-lookup"><span data-stu-id="04153-387">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="04153-388">O componente grava diretamente no `Expanded` parâmetro, que demonstra o problema com parâmetros substituídos e deve ser evitado.</span><span class="sxs-lookup"><span data-stu-id="04153-388">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

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

<span data-ttu-id="04153-389">O `Expander` componente é adicionado a um componente pai que pode chamar <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="04153-389">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

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

<span data-ttu-id="04153-390">Inicialmente, os `Expander` componentes se comportam de forma independente quando suas `Expanded` Propriedades são alternadas.</span><span class="sxs-lookup"><span data-stu-id="04153-390">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="04153-391">Os componentes filho mantêm seus Estados como esperado.</span><span class="sxs-lookup"><span data-stu-id="04153-391">The child components maintain their states as expected.</span></span> <span data-ttu-id="04153-392">Quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> é chamado no pai, o `Expanded` parâmetro do primeiro componente filho é redefinido de volta para seu valor inicial ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="04153-392">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="04153-393">O `Expander` valor do segundo componente `Expanded` não é redefinido porque nenhum conteúdo filho é renderizado no segundo componente.</span><span class="sxs-lookup"><span data-stu-id="04153-393">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="04153-394">Para manter o estado no cenário anterior, use um *campo particular* no `Expander` componente para manter seu estado de alternância.</span><span class="sxs-lookup"><span data-stu-id="04153-394">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="04153-395">O seguinte componente revisado `Expander` :</span><span class="sxs-lookup"><span data-stu-id="04153-395">The following revised `Expander` component:</span></span>

* <span data-ttu-id="04153-396">Aceita o `Expanded` valor do parâmetro de componente do pai.</span><span class="sxs-lookup"><span data-stu-id="04153-396">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="04153-397">Atribui o valor do parâmetro de componente a um *campo privado* ( `expanded` ) no [evento OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="04153-397">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="04153-398">Usa o campo particular para manter seu estado de alternância interno, que demonstra como evitar gravar diretamente em um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04153-398">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

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

<span data-ttu-id="04153-399">Para obter informações adicionais, consulte [ Blazor erro de associação bidirecional (dotNet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="04153-399">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="04153-400">Aplicar um atributo</span><span class="sxs-lookup"><span data-stu-id="04153-400">Apply an attribute</span></span>

<span data-ttu-id="04153-401">Os atributos podem ser aplicados a Razor componentes com a [`@attribute`][7] diretiva.</span><span class="sxs-lookup"><span data-stu-id="04153-401">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="04153-402">O exemplo a seguir aplica o [ `[Authorize]` atributo](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) à classe de componente:</span><span class="sxs-lookup"><span data-stu-id="04153-402">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="04153-403">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="04153-403">Conditional HTML element attributes</span></span>

<span data-ttu-id="04153-404">Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="04153-404">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="04153-405">Se o valor for `false` ou `null` , o atributo não será renderizado.</span><span class="sxs-lookup"><span data-stu-id="04153-405">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="04153-406">Se o valor é `true` , o atributo é processado minimizado.</span><span class="sxs-lookup"><span data-stu-id="04153-406">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="04153-407">No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="04153-407">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="04153-408">Se `IsCompleted` for `true` , a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="04153-408">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="04153-409">Se `IsCompleted` for `false` , a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="04153-409">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="04153-410">Para obter mais informações, consulte [ Razor referência de sintaxe para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="04153-410">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="04153-411">Alguns atributos HTML, como [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , não funcionam corretamente quando o tipo .net é um `bool` .</span><span class="sxs-lookup"><span data-stu-id="04153-411">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="04153-412">Nesses casos, use um `string` tipo em vez de um `bool` .</span><span class="sxs-lookup"><span data-stu-id="04153-412">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="04153-413">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="04153-413">Raw HTML</span></span>

<span data-ttu-id="04153-414">Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="04153-414">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="04153-415">Para renderizar HTML bruto, empacote o conteúdo HTML em um `MarkupString` valor.</span><span class="sxs-lookup"><span data-stu-id="04153-415">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="04153-416">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="04153-416">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="04153-417">O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="04153-417">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="04153-418">O exemplo a seguir mostra como usar o `MarkupString` tipo para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="04153-418">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="04153-419">Razor modelo</span><span class="sxs-lookup"><span data-stu-id="04153-419">Razor templates</span></span>

<span data-ttu-id="04153-420">Os fragmentos de renderização podem ser definidos usando a Razor sintaxe do modelo.</span><span class="sxs-lookup"><span data-stu-id="04153-420">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="04153-421">Razor os modelos são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="04153-421">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="04153-422">O exemplo a seguir ilustra como especificar <xref:Microsoft.AspNetCore.Components.RenderFragment> e <xref:Microsoft.AspNetCore.Components.RenderFragment%601> valores e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="04153-422">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="04153-423">Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="04153-423">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="04153-424">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="04153-424">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="04153-425">Ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="04153-425">Static assets</span></span>

<span data-ttu-id="04153-426">Blazorsegue a Convenção de ASP.NET Core aplicativos que posicionam ativos estáticos na [ `web root (wwwroot)` pasta](xref:fundamentals/index#web-root)do projeto.</span><span class="sxs-lookup"><span data-stu-id="04153-426">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="04153-427">Use um caminho relativo de base ( `/` ) para se referir à raiz da Web para um ativo estático.</span><span class="sxs-lookup"><span data-stu-id="04153-427">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="04153-428">No exemplo a seguir, `logo.png` está localizado fisicamente na `{PROJECT ROOT}/wwwroot/images` pasta:</span><span class="sxs-lookup"><span data-stu-id="04153-428">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="04153-429">Razor os componentes **não** dão suporte a notação de barra de til ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="04153-429">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="04153-430">Para obter informações sobre como definir o caminho base de um aplicativo, consulte <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="04153-430">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="04153-431">Não há suporte para auxiliares de marcas nos componentes</span><span class="sxs-lookup"><span data-stu-id="04153-431">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="04153-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) Não tem suporte em Razor componentes ( `.razor` arquivos).</span><span class="sxs-lookup"><span data-stu-id="04153-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="04153-433">Para fornecer a funcionalidade do tipo auxiliar de marca no Blazor , crie um componente com a mesma funcionalidade que o auxiliar de marca e use o componente em vez disso.</span><span class="sxs-lookup"><span data-stu-id="04153-433">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="04153-434">Imagens SVG (gráficos vetoriais escaláveis)</span><span class="sxs-lookup"><span data-stu-id="04153-434">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="04153-435">Como Blazor o renderiza as imagens html, com suporte para navegadores, incluindo imagens de vetores escalonáveis (SVG) `.svg` , há suporte por meio da `<img>` marca:</span><span class="sxs-lookup"><span data-stu-id="04153-435">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="04153-436">Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos ( `.css` ):</span><span class="sxs-lookup"><span data-stu-id="04153-436">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="04153-437">No entanto, a marcação SVG embutida não tem suporte em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="04153-437">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="04153-438">Se você posicionar uma `<svg>` marca diretamente em um arquivo de componente ( `.razor` ), a renderização básica da imagem terá suporte, mas muitos cenários avançados ainda não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="04153-438">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="04153-439">Por exemplo, as `<use>` marcas não são respeitadas atualmente e [`@bind`][10] não podem ser usadas com algumas marcas SVG.</span><span class="sxs-lookup"><span data-stu-id="04153-439">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="04153-440">Para obter mais informações, consulte [suporte a SVG em Blazor (dotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="04153-440">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="04153-441">Comportamento de renderização de espaço em branco</span><span class="sxs-lookup"><span data-stu-id="04153-441">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="04153-442">A menos que a [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) diretiva seja usada com um valor de `true` , espaço em branco extra será removido por padrão se:</span><span class="sxs-lookup"><span data-stu-id="04153-442">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="04153-443">À esquerda ou à direita dentro de um elemento.</span><span class="sxs-lookup"><span data-stu-id="04153-443">Leading or trailing within an element.</span></span>
* <span data-ttu-id="04153-444">À esquerda ou à direita dentro de um `RenderFragment` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04153-444">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="04153-445">Por exemplo, conteúdo filho passado para outro componente.</span><span class="sxs-lookup"><span data-stu-id="04153-445">For example, child content passed to another component.</span></span>
* <span data-ttu-id="04153-446">Ele precede ou segue um bloco de código C#, como `@if` ou `@foreach` .</span><span class="sxs-lookup"><span data-stu-id="04153-446">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="04153-447">A remoção de espaço em branco pode afetar a saída renderizada ao usar uma regra de CSS, como `white-space: pre` .</span><span class="sxs-lookup"><span data-stu-id="04153-447">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="04153-448">Para desabilitar essa otimização de desempenho e preservar o espaço em branco, execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="04153-448">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="04153-449">Adicione a `@preservewhitespace true` diretiva na parte superior do `.razor` arquivo para aplicar a preferência a um componente específico.</span><span class="sxs-lookup"><span data-stu-id="04153-449">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="04153-450">Adicione a `@preservewhitespace true` diretiva dentro de um `_Imports.razor` arquivo para aplicar a preferência a um subdiretório inteiro ou a todo o projeto.</span><span class="sxs-lookup"><span data-stu-id="04153-450">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="04153-451">Na maioria dos casos, nenhuma ação é necessária, pois os aplicativos normalmente continuam a se comportar normalmente (mas com mais rapidez).</span><span class="sxs-lookup"><span data-stu-id="04153-451">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="04153-452">Se a remoção de espaço em branco causar qualquer problema para um componente específico, use- `@preservewhitespace true` o nesse componente para desabilitar essa otimização.</span><span class="sxs-lookup"><span data-stu-id="04153-452">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="04153-453">O espaço em branco é mantido no código-fonte de um componente.</span><span class="sxs-lookup"><span data-stu-id="04153-453">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="04153-454">Texto somente em espaços em branco é renderizado no Modelo de Objeto do Documento do navegador (DOM) mesmo quando não há nenhum efeito visual.</span><span class="sxs-lookup"><span data-stu-id="04153-454">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="04153-455">Considere o seguinte Razor código de componente:</span><span class="sxs-lookup"><span data-stu-id="04153-455">Consider the following Razor component code:</span></span>

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

<span data-ttu-id="04153-456">O exemplo anterior processa o seguinte espaço em branco desnecessário:</span><span class="sxs-lookup"><span data-stu-id="04153-456">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="04153-457">Fora do `@foreach` bloco de código.</span><span class="sxs-lookup"><span data-stu-id="04153-457">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="04153-458">Em volta do `<li>` elemento.</span><span class="sxs-lookup"><span data-stu-id="04153-458">Around the `<li>` element.</span></span>
* <span data-ttu-id="04153-459">Em volta da `@item.Text` saída.</span><span class="sxs-lookup"><span data-stu-id="04153-459">Around the `@item.Text` output.</span></span>

<span data-ttu-id="04153-460">Uma lista contendo 100 itens resulta em 402 áreas de espaço em branco, e nenhum espaço em branco extra afeta visualmente a saída renderizada.</span><span class="sxs-lookup"><span data-stu-id="04153-460">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="04153-461">Ao renderizar HTML estático para componentes, o espaço em branco dentro de uma marca não é preservado.</span><span class="sxs-lookup"><span data-stu-id="04153-461">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="04153-462">Por exemplo, exiba a origem do seguinte componente na saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="04153-462">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="04153-463">O espaço em branco não é preservado da Razor marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="04153-463">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="04153-464">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="04153-464">Additional resources</span></span>

* <span data-ttu-id="04153-465"><xref:blazor/security/server/threat-mitigation>: Inclui diretrizes sobre a criação de Blazor Server aplicativos que devem combater o esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="04153-465"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

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
