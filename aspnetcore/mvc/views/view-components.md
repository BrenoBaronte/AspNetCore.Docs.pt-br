---
title: Componentes de exibição no ASP.NET Core
author: rick-anderson
description: Saiba como os componentes de exibição são usados no ASP.NET Core e como adicioná-los aos aplicativos.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-components
ms.openlocfilehash: 28696d246c5e1e6874e0d9058813750ed1955003
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774646"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="d0a6f-103">Componentes de exibição no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0a6f-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="d0a6f-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0a6f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d0a6f-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0a6f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="d0a6f-106">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-106">View components</span></span>

<span data-ttu-id="d0a6f-107">Os componentes de exibição são semelhantes às exibições parciais, mas são muito mais eficientes.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="d0a6f-108">Os componentes de exibição não usam o model binding e dependem apenas dos dados fornecidos durante uma chamada a eles.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="d0a6f-109">Este artigo foi escrito usando controladores e exibições, mas os componentes de exibição Razor também funcionam com páginas.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="d0a6f-110">Um componente de exibição:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-110">A view component:</span></span>

* <span data-ttu-id="d0a6f-111">Renderiza uma parte em vez de uma resposta inteira.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="d0a6f-112">Inclui os mesmos benefícios de capacidade de teste e separação de interesses e encontrados entre um controlador e uma exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="d0a6f-113">Pode ter parâmetros e uma lógica de negócios.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="d0a6f-114">É geralmente invocado em uma página de layout.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="d0a6f-115">Os componentes de exibição destinam-se a qualquer momento em que há uma lógica de renderização reutilizável muito complexa para uma exibição parcial, como:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="d0a6f-116">Menus de navegação dinâmica</span><span class="sxs-lookup"><span data-stu-id="d0a6f-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="d0a6f-117">Nuvem de marcas (na qual ela consulta o banco de dados)</span><span class="sxs-lookup"><span data-stu-id="d0a6f-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="d0a6f-118">Painel de logon</span><span class="sxs-lookup"><span data-stu-id="d0a6f-118">Login panel</span></span>
* <span data-ttu-id="d0a6f-119">Carrinho de compras</span><span class="sxs-lookup"><span data-stu-id="d0a6f-119">Shopping cart</span></span>
* <span data-ttu-id="d0a6f-120">Artigos publicados recentemente</span><span class="sxs-lookup"><span data-stu-id="d0a6f-120">Recently published articles</span></span>
* <span data-ttu-id="d0a6f-121">Conteúdo da barra lateral em um blog típico</span><span class="sxs-lookup"><span data-stu-id="d0a6f-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="d0a6f-122">Um painel de logon que é renderizado em cada página e mostra os links para logoff ou logon, dependendo do estado de logon do usuário</span><span class="sxs-lookup"><span data-stu-id="d0a6f-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="d0a6f-123">Um componente de exibição consiste em duas partes: a classe (normalmente derivada de [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) e o resultado que ele retorna (normalmente, uma exibição).</span><span class="sxs-lookup"><span data-stu-id="d0a6f-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="d0a6f-124">Assim como os controladores, um componente de exibição pode ser um POCO, mas a maioria dos desenvolvedores desejará aproveitar os métodos e as propriedades disponíveis com a derivação de `ViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="d0a6f-125">Ao considerar se os componentes de exibição atendem às especificações de um Razor aplicativo, considere usar componentes em vez disso.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> Razor<span data-ttu-id="d0a6f-126">Os componentes também combinam marcação com código C# para produzir unidades de interface do usuário reutilizáveis.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-126"> Components also combine markup with C# code to produce reusable UI units.</span></span> Razor<span data-ttu-id="d0a6f-127">Os componentes são projetados para a produtividade do desenvolvedor ao fornecer a lógica e a composição da interface do usuário do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-127"> Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="d0a6f-128">Para obter mais informações, consulte <xref:blazor/components>.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-128">For more information, see <xref:blazor/components>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="d0a6f-129">Criando um componente de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-129">Creating a view component</span></span>

<span data-ttu-id="d0a6f-130">Esta seção contém os requisitos de alto nível para a criação de um componente de exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="d0a6f-131">Mais adiante neste artigo, examinaremos cada etapa em detalhes e criaremos um componente de exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="d0a6f-132">A classe de componente de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-132">The view component class</span></span>

<span data-ttu-id="d0a6f-133">Uma classe de componente de exibição pode ser criada por um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="d0a6f-134">Derivação de *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="d0a6f-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="d0a6f-135">Decoração de uma classe com o atributo `[ViewComponent]` ou derivação de uma classe com o atributo `[ViewComponent]`</span><span class="sxs-lookup"><span data-stu-id="d0a6f-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="d0a6f-136">Criação de uma classe em que o nome termina com o sufixo *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="d0a6f-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="d0a6f-137">Assim como os controladores, os componentes de exibição precisam ser classes públicas, não aninhadas e não abstratas.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="d0a6f-138">O nome do componente de exibição é o nome da classe com o sufixo "ViewComponent" removido.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="d0a6f-139">Também pode ser especificado de forma explícita com a propriedade `ViewComponentAttribute.Name`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="d0a6f-140">Uma classe de componente de exibição:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-140">A view component class:</span></span>

* <span data-ttu-id="d0a6f-141">Dá suporte total à [injeção de dependência do construtor](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="d0a6f-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="d0a6f-142">Não participa do ciclo de vida do controlador, o que significa que não é possível usar [filtros](../controllers/filters.md) em um componente de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="d0a6f-143">Métodos de componente de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-143">View component methods</span></span>

<span data-ttu-id="d0a6f-144">Um componente de exibição define sua lógica em um método `InvokeAsync` que retorna um `Task<IViewComponentResult>` ou em um método `Invoke` síncrono que retorna um `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="d0a6f-145">Os parâmetros são recebidos diretamente da invocação do componente de exibição, não do model binding.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="d0a6f-146">Um componente de exibição nunca manipula uma solicitação diretamente.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-146">A view component never directly handles a request.</span></span> <span data-ttu-id="d0a6f-147">Normalmente, um componente de exibição inicializa um modelo e passa-o para uma exibição chamando o método `View`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="d0a6f-148">Em resumo, os métodos de componente de exibição:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-148">In summary, view component methods:</span></span>

* <span data-ttu-id="d0a6f-149">Definem um método `InvokeAsync` que retorna um `Task<IViewComponentResult>` ou um método `Invoke` síncrono que retorna um `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="d0a6f-150">Normalmente Inicializa um modelo e o passa para um modo de exibição chamando `ViewComponent` `View` o método.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="d0a6f-151">Os parâmetros são recebidos do método de chamada, não do HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="d0a6f-152">Não há nenhum model binding.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-152">There's no model binding.</span></span>
* <span data-ttu-id="d0a6f-153">Não são acessíveis diretamente como um ponto de extremidade HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="d0a6f-154">Eles são invocados no código (normalmente, em uma exibição).</span><span class="sxs-lookup"><span data-stu-id="d0a6f-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="d0a6f-155">Um componente de exibição nunca manipula uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-155">A view component never handles a request.</span></span>
* <span data-ttu-id="d0a6f-156">São sobrecarregados na assinatura, em vez de nos detalhes da solicitação HTTP atual.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="d0a6f-157">Caminho de pesquisa de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-157">View search path</span></span>

<span data-ttu-id="d0a6f-158">O runtime pesquisa a exibição nos seguintes caminhos:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="d0a6f-159">/Views/{Nome do Controlador}/Components/{Nome do Componente da Exibição}/{Nome da Exibição}</span><span class="sxs-lookup"><span data-stu-id="d0a6f-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="d0a6f-160">/Views/Shared/Components/{Nome do Componente da Exibição}/{Nome da Exibição}</span><span class="sxs-lookup"><span data-stu-id="d0a6f-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="d0a6f-161">/Pages/Shared/Components/{Nome do Componente da Exibição}/{Nome da Exibição}</span><span class="sxs-lookup"><span data-stu-id="d0a6f-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="d0a6f-162">O caminho de pesquisa aplica-se a projetos que usam Razor controladores + exibições e páginas.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="d0a6f-163">O nome de exibição padrão de um componente de exibição é *Default*, o que significa que o arquivo de exibição geralmente será nomeado *Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-163">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="d0a6f-164">Especifique outro nome de exibição ao criar o resultado do componente de exibição ou ao chamar o método `View`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="d0a6f-165">Recomendamos que você nomeie o arquivo de exibição *Default.cshtml* e use o caminho *Views/Shared/Components/{Nome do Componente da Exibição}/{Nome da Exibição}*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="d0a6f-166">O componente de exibição `PriorityList` usado nesta amostra usa *Views/Shared/Components/PriorityList/Default.cshtml* como a exibição do componente de exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="d0a6f-167">Personalizar o caminho de pesquisa de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-167">Customize the view search path</span></span>

<span data-ttu-id="d0a6f-168">Para personalizar o caminho de pesquisa de exibição Razor, <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> modifique a coleção.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-168">To customize the view search path, modify Razor's <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="d0a6f-169">Por exemplo, para procurar exibições no caminho "nome do componente/Components/{View}/{View nome}", adicione um novo item à coleção:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-169">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-cs[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="d0a6f-170">No código anterior, o espaço reservado "{0}" representa o caminho "Components/{View Component Name}/{View Name}".</span><span class="sxs-lookup"><span data-stu-id="d0a6f-170">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="d0a6f-171">Invocando um componente de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-171">Invoking a view component</span></span>

<span data-ttu-id="d0a6f-172">Para usar o componente de exibição, chame o seguinte em uma exibição:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-172">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="d0a6f-173">Os parâmetros serão passados para o método `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-173">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="d0a6f-174">O componente de exibição `PriorityList` desenvolvido no artigo é invocado por meio do arquivo de exibição *Views/ToDo/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-174">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="d0a6f-175">A seguir, o método `InvokeAsync` é chamado com dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-175">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="d0a6f-176">Invocando um componente de exibição como um Auxiliar de Marca</span><span class="sxs-lookup"><span data-stu-id="d0a6f-176">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="d0a6f-177">Para o ASP.NET Core 1.1 e superior, invoque um componente de exibição como um [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="d0a6f-177">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="d0a6f-178">Os parâmetros de classe e de método na formatação Pascal Case para Auxiliares de Marcas são convertidos em [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="d0a6f-178">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="d0a6f-179">Para invocar um componente de exibição, o Auxiliar de Marca usa o elemento `<vc></vc>`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-179">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="d0a6f-180">O componente de exibição é especificado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-180">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="d0a6f-181">Para usar um componente de exibição como um Auxiliar de Marca, registre o assembly que contém o componente de exibição usando a diretiva `@addTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-181">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="d0a6f-182">Se seu componente de exibição estiver em um assembly chamado `MyWebApp`, adicione a seguinte diretiva ao arquivo *_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-182">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="d0a6f-183">É possível registrar um componente de exibição como um Auxiliar de Marca a qualquer arquivo que referencie o componente de exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-183">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="d0a6f-184">Consulte [Gerenciando o escopo do Auxiliar de Marca](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) para obter mais informações sobre como registrar Auxiliares de Marca.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-184">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="d0a6f-185">O método `InvokeAsync` usado neste tutorial:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-185">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="d0a6f-186">Na marcação do Auxiliar de Marca:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-186">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="d0a6f-187">Na amostra acima, o componente de exibição `PriorityList` torna-se `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-187">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="d0a6f-188">Os parâmetros para o componente de exibição são passados como atributos em kebab case.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-188">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="d0a6f-189">Invocando um componente de exibição diretamente em um controlador</span><span class="sxs-lookup"><span data-stu-id="d0a6f-189">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="d0a6f-190">Os componentes de exibição normalmente são invocados em uma exibição, mas você pode invocá-los diretamente em um método do controlador.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-190">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="d0a6f-191">Embora os componentes de exibição não definam pontos de extremidade como controladores, você pode implementar com facilidade uma ação do controlador que retorna o conteúdo de um `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-191">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="d0a6f-192">Neste exemplo, o componente de exibição é chamado diretamente no controlador:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-192">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="d0a6f-193">Passo a passo: criando um componente de exibição simples</span><span class="sxs-lookup"><span data-stu-id="d0a6f-193">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="d0a6f-194">[Baixe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), compile e teste o código inicial.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-194">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="d0a6f-195">É um projeto simples com um controlador `ToDo` que exibe uma lista de itens *ToDo*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-195">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Lista de ToDos](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="d0a6f-197">Adicionar uma classe ViewComponent</span><span class="sxs-lookup"><span data-stu-id="d0a6f-197">Add a ViewComponent class</span></span>

<span data-ttu-id="d0a6f-198">Crie uma pasta *ViewComponents* e adicione a seguinte classe `PriorityListViewComponent`:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-198">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="d0a6f-199">Observações sobre o código:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-199">Notes on the code:</span></span>

* <span data-ttu-id="d0a6f-200">As classes de componente de exibição podem ser contidas em **qualquer** pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-200">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="d0a6f-201">Como o nome da classe PriorityList**ViewComponent** termina com o sufixo **ViewComponent**, o runtime usará a cadeia de caracteres "PriorityList" ao referenciar o componente de classe em uma exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-201">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="d0a6f-202">Explicarei isso mais detalhadamente mais adiante.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="d0a6f-203">O atributo `[ViewComponent]` pode alterar o nome usado para referenciar um componente de exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-203">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="d0a6f-204">Por exemplo, poderíamos nomear a classe `XYZ` e aplicar o atributo `ViewComponent`:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-204">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="d0a6f-205">O atributo `[ViewComponent]` acima instrui o seletor de componente de exibição a usar o nome `PriorityList` ao procurar as exibições associadas ao componente e a usar a cadeia de caracteres "PriorityList" ao referenciar o componente de classe em uma exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-205">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="d0a6f-206">Explicarei isso mais detalhadamente mais adiante.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-206">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="d0a6f-207">O componente usa a [injeção de dependência](../../fundamentals/dependency-injection.md) para disponibilizar o contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-207">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="d0a6f-208">`InvokeAsync` expõe um método que pode ser chamado em uma exibição e pode usar um número arbitrário de argumentos.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-208">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="d0a6f-209">O método `InvokeAsync` retorna o conjunto de itens `ToDo` que atendem aos parâmetros `isDone` e `maxPriority`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-209">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="d0a6f-210">Criar a exibição do Razor componente de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-210">Create the view component Razor view</span></span>

* <span data-ttu-id="d0a6f-211">Crie a pasta *Views/Shared/Components*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-211">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="d0a6f-212">Essa pasta **deve** nomeada *Components*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-212">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="d0a6f-213">Crie a pasta *Views/Shared/Components/PriorityList*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-213">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="d0a6f-214">Esse nome de pasta deve corresponder ao nome da classe do componente de exibição ou ao nome da classe menos o sufixo (se seguimos a convenção e usamos o sufixo *ViewComponent* no nome da classe).</span><span class="sxs-lookup"><span data-stu-id="d0a6f-214">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="d0a6f-215">Se você usou o atributo `ViewComponent`, o nome da classe precisa corresponder à designação de atributo.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-215">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="d0a6f-216">Crie uma exibição views */Shared/Components/prioritylist/default. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="d0a6f-216">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="d0a6f-217">A Razor exibição usa uma lista `TodoItem` e as exibe.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-217">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="d0a6f-218">Se o método `InvokeAsync` do componente de exibição não passar o nome da exibição (como em nossa amostra), *Default* será usado como o nome da exibição, por convenção.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-218">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="d0a6f-219">Mais adiante no tutorial, mostrarei como passar o nome da exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-219">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="d0a6f-220">Para substituir o estilo padrão de um controlador específico, adicione uma exibição à pasta de exibição específica do controlador (por exemplo, *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-220">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="d0a6f-221">Se o componente de exibição for específico do controlador, adicione-o à pasta específica do controlador (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="d0a6f-221">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="d0a6f-222">Adicione um `div` que contenha uma chamada para o componente da lista de prioridades à parte inferior do arquivo *Views/ToDo/index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-222">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="d0a6f-223">A marcação `@await Component.InvokeAsync` mostra a sintaxe para chamar componentes de exibição.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-223">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="d0a6f-224">O primeiro argumento é o nome do componente que queremos invocar ou chamar.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-224">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="d0a6f-225">Os parâmetros seguintes são passados para o componente.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-225">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="d0a6f-226">`InvokeAsync` pode usar um número arbitrário de argumentos.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-226">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="d0a6f-227">Testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-227">Test the app.</span></span> <span data-ttu-id="d0a6f-228">A seguinte imagem mostra a lista ToDo e os itens de prioridade:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-228">The following image shows the ToDo list and the priority items:</span></span>

![lista de tarefas pendentes e itens de prioridade](view-components/_static/pi.png)

<span data-ttu-id="d0a6f-230">Também chame o componente de exibição diretamente no controlador:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-230">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![itens de prioridade da ação IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="d0a6f-232">Especificando um nome de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-232">Specifying a view name</span></span>

<span data-ttu-id="d0a6f-233">Um componente de exibição complexo pode precisar especificar uma exibição não padrão em algumas condições.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-233">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="d0a6f-234">O código a seguir mostra como especificar a exibição "PVC" no método `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-234">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="d0a6f-235">Atualize o método `InvokeAsync` na classe `PriorityListViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-235">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="d0a6f-236">Copie o arquivo *Views/Shared/Components/PriorityList/Default.cshtml* para uma exibição nomeada *Views/Shared/Components/PriorityList/PVC.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-236">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="d0a6f-237">Adicione um cabeçalho para indicar que a exibição PVC está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-237">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="d0a6f-238">Atualize *Views/ToDo/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-238">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="d0a6f-239">Execute o aplicativo e verifique a exibição PVC.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-239">Run the app and verify PVC view.</span></span>

![Componente de exibição de prioridade](view-components/_static/pvc.png)

<span data-ttu-id="d0a6f-241">Se a exibição PVC não é renderizada, verifique se você está chamando o componente de exibição com uma prioridade igual a 4 ou superior.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-241">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="d0a6f-242">Examinar o caminho de exibição</span><span class="sxs-lookup"><span data-stu-id="d0a6f-242">Examine the view path</span></span>

* <span data-ttu-id="d0a6f-243">Altere o parâmetro de prioridade para três ou menos para que a exibição de prioridade não seja retornada.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-243">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="d0a6f-244">Renomeie temporariamente *Views/ToDo/Components/PriorityList/Default.cshtml* como *1Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-244">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="d0a6f-245">Teste o aplicativo. Você obterá o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-245">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="d0a6f-246">Copie *Views/ToDo/Components/PriorityList/1Default.cshtml* para *Views/Shared/Components/PriorityList/Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-246">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="d0a6f-247">Adicione uma marcação ao componente de exibição ToDo *Shared* para indicar que a exibição foi obtida da pasta *Shared*.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-247">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="d0a6f-248">Teste o componente de exibição **Shared**.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-248">Test the **Shared** component view.</span></span>

![Saída de ToDo com o componente de exibição Shared](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="d0a6f-250">Como evitar cadeias de caracteres codificadas</span><span class="sxs-lookup"><span data-stu-id="d0a6f-250">Avoiding hard-coded strings</span></span>

<span data-ttu-id="d0a6f-251">Se deseja obter segurança em tempo de compilação, substitua o nome do componente de exibição embutido em código pelo nome da classe.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-251">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="d0a6f-252">Crie o componente de exibição sem o sufixo "ViewComponent":</span><span class="sxs-lookup"><span data-stu-id="d0a6f-252">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="d0a6f-253">Adicione uma `using` instrução ao arquivo Razor de exibição e use o `nameof` operador:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-253">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="d0a6f-254">Executar o trabalho síncrono</span><span class="sxs-lookup"><span data-stu-id="d0a6f-254">Perform synchronous work</span></span>

<span data-ttu-id="d0a6f-255">A estrutura manipulará a invocação de um método `Invoke` síncrono se não for necessário realizar o trabalho assíncrono.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-255">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="d0a6f-256">O método a seguir cria um componente de exibição `Invoke` síncrono:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-256">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="d0a6f-257">O arquivo do Razor componente de exibição lista as cadeias de `Invoke` caracteres passadas para o método (*views/Home/Components/prioritylist/default. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="d0a6f-257">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="d0a6f-258">O componente de exibição é invocado Razor em um arquivo (por exemplo, *views/home/index. cshtml*) usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-258">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="d0a6f-259">Auxiliar de Marca</span><span class="sxs-lookup"><span data-stu-id="d0a6f-259">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="d0a6f-260">Para usar a abordagem <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>, chame `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-260">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="d0a6f-261">O componente de exibição é invocado Razor em um arquivo (por exemplo, *views/home/index. cshtml*) com <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-261">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="d0a6f-262">Chame `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-262">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="d0a6f-263">Para usar o Auxiliar de Marca, registre o assembly que contém o Componente de exibição que usa a diretiva `@addTagHelper` (o componente de exibição está em um assembly chamado `MyWebApp`):</span><span class="sxs-lookup"><span data-stu-id="d0a6f-263">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="d0a6f-264">Use o auxiliar de marca do componente de Razor exibição no arquivo de marcação:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-264">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="d0a6f-265">A assinatura do método `PriorityList.Invoke` de é síncrona, Razor mas localiza e chama o método `Component.InvokeAsync` com no arquivo de marcação.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-265">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="d0a6f-266">Todos os parâmetros do componente de exibição são obrigatórios</span><span class="sxs-lookup"><span data-stu-id="d0a6f-266">All view component parameters are required</span></span>

<span data-ttu-id="d0a6f-267">Todo parâmetro de um componente de exibição é um atributo obrigatório.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-267">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="d0a6f-268">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5011).</span><span class="sxs-lookup"><span data-stu-id="d0a6f-268">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="d0a6f-269">Se algum parâmetro for omitido:</span><span class="sxs-lookup"><span data-stu-id="d0a6f-269">If any  parameter is omitted:</span></span>

* <span data-ttu-id="d0a6f-270">A assinatura do método `InvokeAsync` não corresponderá, portanto, o método não será executado.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-270">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="d0a6f-271">O ViewComponent não renderizará nenhuma marcação.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-271">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="d0a6f-272">Nenhum erro será gerado.</span><span class="sxs-lookup"><span data-stu-id="d0a6f-272">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0a6f-273">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d0a6f-273">Additional resources</span></span>

* [<span data-ttu-id="d0a6f-274">Injeção de dependência em exibições</span><span class="sxs-lookup"><span data-stu-id="d0a6f-274">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
