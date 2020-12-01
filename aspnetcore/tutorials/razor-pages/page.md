---
title: Parte 3, páginas do com Scaffold Razor
author: rick-anderson
description: Parte 3 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 09/25/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: 4a5369b9e40de89ac9a1895466e7bdd7afb9d32e
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420026"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="a8d71-103">Parte 3, com Scaffold Razor páginas em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8d71-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="a8d71-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a8d71-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a8d71-105">Este tutorial examina as Razor páginas criadas por scaffolding no [tutorial anterior](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="a8d71-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a8d71-106">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a8d71-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="a8d71-107">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a8d71-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="a8d71-108">As páginas Create, Delete, Details e Edit</span><span class="sxs-lookup"><span data-stu-id="a8d71-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="a8d71-109">Examine o modelo de página *páginas/filmes/ Index . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="a8d71-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="a8d71-110">Razor As páginas são derivadas de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="a8d71-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="a8d71-111">Por convenção, a `PageModel` classe derivada é nomeada `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="a8d71-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="a8d71-112">O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `RazorPagesMovieContext` à página:</span><span class="sxs-lookup"><span data-stu-id="a8d71-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="a8d71-113">Confira [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="a8d71-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="a8d71-114">Quando uma solicitação é feita para a página, o `OnGetAsync` método retorna uma lista de filmes para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="a8d71-115">Em uma Razor página, `OnGetAsync` ou `OnGet` é chamado para inicializar o estado da página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="a8d71-116">Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.</span><span class="sxs-lookup"><span data-stu-id="a8d71-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="a8d71-117">Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task` , nenhuma instrução de retorno é usada.</span><span class="sxs-lookup"><span data-stu-id="a8d71-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="a8d71-118">Por exemplo, a página de privacidade:</span><span class="sxs-lookup"><span data-stu-id="a8d71-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="a8d71-119">Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno.</span><span class="sxs-lookup"><span data-stu-id="a8d71-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="a8d71-120">Por exemplo, o método *pages/Movies/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="a8d71-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="a8d71-121">Examine a página *páginas/filmes/ Index . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="a8d71-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="a8d71-122">Razor pode fazer a transição de HTML para C# ou para uma Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="a8d71-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="a8d71-123">Quando um `@` símbolo é seguido por uma [ Razor palavra-chave reservada](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para uma Razor marcação específica, caso contrário, ele faz a transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="a8d71-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="a8d71-124">A diretiva @page</span><span class="sxs-lookup"><span data-stu-id="a8d71-124">The @page directive</span></span>

<span data-ttu-id="a8d71-125">A `@page` Razor diretiva torna o arquivo uma ação MVC, o que significa que ele pode lidar com solicitações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="a8d71-126">`@page` deve ser a primeira Razor diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="a8d71-127">`@page` e `@model` são exemplos de transição para Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="a8d71-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="a8d71-128">Consulte a [ Razor sintaxe](xref:mvc/views/razor#razor-syntax) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="a8d71-129">A diretiva @model</span><span class="sxs-lookup"><span data-stu-id="a8d71-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="a8d71-130">A `@model` diretiva especifica o tipo do modelo passado para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="a8d71-131">No exemplo anterior, a `@model` linha torna a `PageModel` classe derivada disponível para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="a8d71-132">O modelo é usado nos  [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` na página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="a8d71-133">Examine a expressão lambda usada no auxiliar HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8d71-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="a8d71-134">O auxiliar HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="a8d71-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="a8d71-135">A expressão lambda é inspecionada em vez de avaliada.</span><span class="sxs-lookup"><span data-stu-id="a8d71-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="a8d71-136">Isso significa que não há nenhuma violação de acesso quando `model` , `model.Movie` ou `model.Movie[0]` está `null` ou vazio.</span><span class="sxs-lookup"><span data-stu-id="a8d71-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="a8d71-137">Quando a expressão lambda é avaliada, por exemplo, com `@Html.DisplayFor(modelItem => item.Title)` , os valores de Propriedade do modelo são avaliados.</span><span class="sxs-lookup"><span data-stu-id="a8d71-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="a8d71-138">A página de layout</span><span class="sxs-lookup"><span data-stu-id="a8d71-138">The layout page</span></span>

<span data-ttu-id="a8d71-139">Selecione os links de menu **Razor PagesMovie**, **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="a8d71-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="a8d71-140">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="a8d71-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="a8d71-141">O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a8d71-142">Abra e examine o arquivo *pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a8d71-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a8d71-143">Os modelos de [layout](xref:mvc/views/layout) permitem que o layout do contêiner HTML seja:</span><span class="sxs-lookup"><span data-stu-id="a8d71-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="a8d71-144">Especificado em um único lugar.</span><span class="sxs-lookup"><span data-stu-id="a8d71-144">Specified in one place.</span></span>
* <span data-ttu-id="a8d71-145">Aplicado a várias páginas no site.</span><span class="sxs-lookup"><span data-stu-id="a8d71-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="a8d71-146">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a8d71-147">`RenderBody` é um espaço reservado em que todas as exibições específicas da página são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="a8d71-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a8d71-148">Por exemplo, selecione o link **Privacidade** e a exibição *Pages/Privacy.cshtml* será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="a8d71-149">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="a8d71-149">ViewData and layout</span></span>

<span data-ttu-id="a8d71-150">Considere a seguinte marcação do arquivo *pages/Movies/ Index . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a8d71-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="a8d71-151">A marcação realçada anterior é um exemplo de Razor transição para C#.</span><span class="sxs-lookup"><span data-stu-id="a8d71-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="a8d71-152">Os caracteres `{` e `}` circunscrevem um bloco de código C#.</span><span class="sxs-lookup"><span data-stu-id="a8d71-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="a8d71-153">A `PageModel` classe base contém uma `ViewData` Propriedade Dictionary que pode ser usada para passar dados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="a8d71-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="a8d71-154">Os objetos são adicionados ao `ViewData` dicionário usando um padrão \***valor de chave** _.</span><span class="sxs-lookup"><span data-stu-id="a8d71-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="a8d71-155">No exemplo anterior, a propriedade `Title` é adicionada ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="a8d71-156">A `Title` propriedade é usada no arquivo _Pages/shared/_Layout. cshtml \*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="a8d71-157">A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="a8d71-158">A linha `@*Markup removed for brevity.*@` é um Razor comentário.</span><span class="sxs-lookup"><span data-stu-id="a8d71-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="a8d71-159">Ao contrário dos comentários HTML `<!-- -->` , Razor os comentários não são enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="a8d71-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="a8d71-160">Consulte [MDN Web docs: introdução ao HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="a8d71-161">Atualizar o layout</span><span class="sxs-lookup"><span data-stu-id="a8d71-161">Update the layout</span></span>

1. <span data-ttu-id="a8d71-162">Altere o `<title>` elemento no arquivo *pages/Shared/_Layout. cshtml* para exibir o **filme** em vez de **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="a8d71-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="a8d71-163">Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="a8d71-164">Substitua o elemento anterior pela marcação a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8d71-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="a8d71-165">O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a8d71-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="a8d71-166">Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a8d71-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="a8d71-167">O `asp-page="/Movies/Index"` atributo e o valor da marca auxiliar cria um link para a `/Movies/Index` Razor página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="a8d71-168">O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link.</span><span class="sxs-lookup"><span data-stu-id="a8d71-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="a8d71-169">Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="a8d71-170">Salve as alterações e teste o aplicativo selecionando o link **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="a8d71-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="a8d71-171">Confira o arquivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.</span><span class="sxs-lookup"><span data-stu-id="a8d71-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="a8d71-172">Teste os links **início**, **RpMovie**, **criar**, **Editar** e **excluir** .</span><span class="sxs-lookup"><span data-stu-id="a8d71-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="a8d71-173">Cada página define o título, que você pode ver na guia navegador. Quando você marcar uma página, o título será usado para o indicador.</span><span class="sxs-lookup"><span data-stu-id="a8d71-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="a8d71-174">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a8d71-175">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades não inglesas que usam uma vírgula (",") para um ponto decimal e não US-English formatos de data, você deve executar etapas para globalizar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8d71-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="a8d71-176">Confira [Problema 4076 do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.</span><span class="sxs-lookup"><span data-stu-id="a8d71-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="a8d71-177">A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a8d71-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="a8d71-178">A marcação anterior define o arquivo de layout para *pages/Shared/_Layout. cshtml* para todos os Razor arquivos na pasta *pages* .</span><span class="sxs-lookup"><span data-stu-id="a8d71-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="a8d71-179">Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="a8d71-180">O modelo Criar página</span><span class="sxs-lookup"><span data-stu-id="a8d71-180">The Create page model</span></span>

<span data-ttu-id="a8d71-181">Examine o modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8d71-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="a8d71-182">O método `OnGet` inicializa qualquer estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="a8d71-183">A página Criar não tem nenhum estado para inicializar, assim, `Page` é retornado.</span><span class="sxs-lookup"><span data-stu-id="a8d71-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="a8d71-184">Apresentamos um exemplo de inicialização de estado `OnGet` posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a8d71-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="a8d71-185">O método `Page` cria um objeto `PageResult` que renderiza a página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="a8d71-186">A `Movie` propriedade usa o atributo [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para aceitar a associação de [modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="a8d71-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="a8d71-187">Quando o formulário Criar posta os valores de formulário, o runtime do ASP.NET Core associa os valores postados ao modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="a8d71-188">O método `OnPostAsync` é executado quando a página posta dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="a8d71-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="a8d71-189">Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="a8d71-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="a8d71-190">A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado.</span><span class="sxs-lookup"><span data-stu-id="a8d71-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="a8d71-191">Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data.</span><span class="sxs-lookup"><span data-stu-id="a8d71-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="a8d71-192">A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a8d71-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="a8d71-193">Se não houver nenhum erro de modelo:</span><span class="sxs-lookup"><span data-stu-id="a8d71-193">If there are no model errors:</span></span>

* <span data-ttu-id="a8d71-194">Os dados são salvos.</span><span class="sxs-lookup"><span data-stu-id="a8d71-194">The data is saved.</span></span>
* <span data-ttu-id="a8d71-195">O navegador é redirecionado para a Index página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-195">The browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="a8d71-196">A Razor página criar</span><span class="sxs-lookup"><span data-stu-id="a8d71-196">The Create Razor Page</span></span>

<span data-ttu-id="a8d71-197">Examine o arquivo de página *páginas/filmes/Create. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="a8d71-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="a8d71-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d71-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a8d71-199">O Visual Studio exibe as marcas a seguir em uma fonte em negrito diferente usada em auxiliares de marcações:</span><span class="sxs-lookup"><span data-stu-id="a8d71-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Exibição de VS17 da página Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a8d71-201">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8d71-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a8d71-202">Os auxiliares de marcações a seguir são exibidos na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="a8d71-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="a8d71-203">O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a8d71-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="a8d71-204">O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="a8d71-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="a8d71-205">O mecanismo scaffolding cria a Razor marcação para cada campo no modelo, exceto o ID, semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="a8d71-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="a8d71-206">Os [auxiliares de marca de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) exibem erros de validação.</span><span class="sxs-lookup"><span data-stu-id="a8d71-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="a8d71-207">A validação será abordada em mais detalhes posteriormente nesta série.</span><span class="sxs-lookup"><span data-stu-id="a8d71-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="a8d71-208">A [marca de rótulo Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) gera a legenda do rótulo e o `[for]` atributo para a `Title` propriedade.</span><span class="sxs-lookup"><span data-stu-id="a8d71-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="a8d71-209">O [auxiliar de marca de entrada](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) usa os atributos [Annotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz atributos HTML necessários para a validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="a8d71-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="a8d71-210">Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a8d71-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8d71-211">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a8d71-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a8d71-212">[Anterior: adicionar um modelo](xref:tutorials/razor-pages/model) 
>  [Em seguida: trabalhar com um banco de dados](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="a8d71-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="a8d71-213">As páginas Create, Delete, Details e Edit</span><span class="sxs-lookup"><span data-stu-id="a8d71-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="a8d71-214">Examine o modelo de página *páginas/filmes/ Index . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="a8d71-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="a8d71-215">Razor As páginas são derivadas de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="a8d71-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="a8d71-216">Por convenção, a `PageModel` classe derivada é nomeada `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="a8d71-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="a8d71-217">O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `RazorPagesMovieContext` à página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="a8d71-218">As páginas com Scaffold seguem esse padrão.</span><span class="sxs-lookup"><span data-stu-id="a8d71-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="a8d71-219">Confira [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="a8d71-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="a8d71-220">Quando uma solicitação é feita para a página, o `OnGetAsync` método retorna uma lista de filmes para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="a8d71-221">`OnGetAsync` ou `OnGet` é chamado em uma Razor página para inicializar o estado da página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="a8d71-222">Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.</span><span class="sxs-lookup"><span data-stu-id="a8d71-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="a8d71-223">Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task` , nenhum método de retorno é usado.</span><span class="sxs-lookup"><span data-stu-id="a8d71-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="a8d71-224">Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno.</span><span class="sxs-lookup"><span data-stu-id="a8d71-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="a8d71-225">Por exemplo, o método *pages/Movies/Create. cshtml. cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="a8d71-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="a8d71-226">Examine a página *páginas/filmes/ Index . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="a8d71-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="a8d71-227">Razor pode fazer a transição de HTML para C# ou para uma Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="a8d71-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="a8d71-228">Quando um `@` símbolo é seguido por uma [ Razor palavra-chave reservada](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para uma Razor marcação específica, caso contrário, ele faz a transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="a8d71-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="a8d71-229">A `@page` Razor diretiva transforma o arquivo em uma ação MVC, o que significa que ele pode lidar com solicitações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="a8d71-230">`@page` deve ser a primeira Razor diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="a8d71-231">`@page` é um exemplo de transição para Razor marcação específica.</span><span class="sxs-lookup"><span data-stu-id="a8d71-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="a8d71-232">Consulte a [ Razor sintaxe](xref:mvc/views/razor#razor-syntax) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="a8d71-233">A diretiva @model</span><span class="sxs-lookup"><span data-stu-id="a8d71-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="a8d71-234">A `@model` diretiva especifica o tipo do modelo passado para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="a8d71-235">No exemplo anterior, a `@model` linha torna a `PageModel` classe derivada disponível para a Razor página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="a8d71-236">O modelo é usado nos  [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` na página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="a8d71-237">Auxiliares HTML</span><span class="sxs-lookup"><span data-stu-id="a8d71-237">HTML Helpers</span></span>

<span data-ttu-id="a8d71-238">Examine a expressão lambda usada no auxiliar HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8d71-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="a8d71-239">O auxiliar HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="a8d71-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="a8d71-240">A expressão lambda é inspecionada em vez de avaliada.</span><span class="sxs-lookup"><span data-stu-id="a8d71-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="a8d71-241">Isso significa que não há nenhuma violação de acesso quando `model`, `model.Movie` ou `model.Movie[0]` são `null` ou vazios.</span><span class="sxs-lookup"><span data-stu-id="a8d71-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="a8d71-242">Quando a expressão lambda é avaliada, por exemplo, com `@Html.DisplayFor(modelItem => item.Title)` , os valores de Propriedade do modelo são avaliados.</span><span class="sxs-lookup"><span data-stu-id="a8d71-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="a8d71-243">A página de layout</span><span class="sxs-lookup"><span data-stu-id="a8d71-243">The layout page</span></span>

<span data-ttu-id="a8d71-244">Selecione os links de menu **Razor PagesMovie**, **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="a8d71-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="a8d71-245">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="a8d71-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="a8d71-246">O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a8d71-247">Abra e examine o arquivo *pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a8d71-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a8d71-248">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML de um site em um único local e, em seguida, aplicá-lo em várias páginas no site.</span><span class="sxs-lookup"><span data-stu-id="a8d71-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="a8d71-249">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a8d71-250">`RenderBody` é um espaço reservado em que todas as visualizações específicas da página criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="a8d71-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a8d71-251">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Pages/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="a8d71-252">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="a8d71-252">ViewData and layout</span></span>

<span data-ttu-id="a8d71-253">Considere o seguinte código do arquivo *pages/Movies/ Index . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a8d71-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="a8d71-254">O código realçado anterior é um exemplo de Razor transição para o C#.</span><span class="sxs-lookup"><span data-stu-id="a8d71-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="a8d71-255">Os caracteres `{` e `}` circunscrevem um bloco de código C#.</span><span class="sxs-lookup"><span data-stu-id="a8d71-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="a8d71-256">A classe base `PageModel` tem uma propriedade de dicionário `ViewData` que pode ser usada para adicionar os dados que você deseja passar para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="a8d71-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="a8d71-257">Você adiciona objetos ao dicionário `ViewData` usando um padrão de chave/valor.</span><span class="sxs-lookup"><span data-stu-id="a8d71-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="a8d71-258">No exemplo anterior, a propriedade `Title` é adicionada ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="a8d71-259">A propriedade `Title` é usada no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a8d71-260">A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="a8d71-261">A linha `@*Markup removed for brevity.*@` é um Razor comentário.</span><span class="sxs-lookup"><span data-stu-id="a8d71-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="a8d71-262">Ao contrário dos comentários HTML `<!-- -->` , Razor os comentários não são enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="a8d71-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="a8d71-263">Consulte [MDN Web docs: introdução ao HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="a8d71-264">Atualizar o layout</span><span class="sxs-lookup"><span data-stu-id="a8d71-264">Update the layout</span></span>

<span data-ttu-id="a8d71-265">Altere o `<title>` elemento no arquivo *pages/Shared/_Layout. cshtml* para exibir o **filme** em vez de **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="a8d71-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="a8d71-266">Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="a8d71-267">Substitua o elemento anterior pela marcação a seguir.</span><span class="sxs-lookup"><span data-stu-id="a8d71-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="a8d71-268">O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a8d71-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="a8d71-269">Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a8d71-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="a8d71-270">O `asp-page="/Movies/Index"` atributo e o valor da marca auxiliar cria um link para a `/Movies/Index` Razor página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="a8d71-271">O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link.</span><span class="sxs-lookup"><span data-stu-id="a8d71-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="a8d71-272">Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="a8d71-273">Salve suas alterações e teste o aplicativo clicando no link **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="a8d71-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="a8d71-274">Confira o arquivo [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.</span><span class="sxs-lookup"><span data-stu-id="a8d71-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="a8d71-275">Teste os outros links (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**).</span><span class="sxs-lookup"><span data-stu-id="a8d71-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="a8d71-276">Cada página define o título, que você pode ver na guia navegador. Quando você marcar uma página, o título será usado para o indicador.</span><span class="sxs-lookup"><span data-stu-id="a8d71-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="a8d71-277">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a8d71-278">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades não inglesas que usam uma vírgula (",") para um ponto decimal e não US-English formatos de data, você deve executar etapas para globalizar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8d71-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="a8d71-279">Veja [Problema 4076 do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.</span><span class="sxs-lookup"><span data-stu-id="a8d71-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="a8d71-280">A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a8d71-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="a8d71-281">A marcação anterior define o arquivo de layout para *pages/Shared/_Layout. cshtml* para todos os Razor arquivos na pasta *pages* .</span><span class="sxs-lookup"><span data-stu-id="a8d71-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="a8d71-282">Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8d71-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="a8d71-283">O modelo Criar página</span><span class="sxs-lookup"><span data-stu-id="a8d71-283">The Create page model</span></span>

<span data-ttu-id="a8d71-284">Examine o modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8d71-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="a8d71-285">O método `OnGet` inicializa qualquer estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="a8d71-286">A página Criar não tem nenhum estado para inicializar, assim, `Page` é retornado.</span><span class="sxs-lookup"><span data-stu-id="a8d71-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="a8d71-287">Mais adiante no tutorial, você verá o estado de inicialização do método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="a8d71-288">O método `Page` cria um objeto `PageResult` que renderiza a página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a8d71-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="a8d71-289">A `Movie` propriedade usa o atributo [[BindProperty]] <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> para aceitar a associação de [modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="a8d71-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="a8d71-290">Quando o formulário Criar posta os valores de formulário, o runtime do ASP.NET Core associa os valores postados ao modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a8d71-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="a8d71-291">O método `OnPostAsync` é executado quando a página posta dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="a8d71-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="a8d71-292">Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="a8d71-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="a8d71-293">A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado.</span><span class="sxs-lookup"><span data-stu-id="a8d71-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="a8d71-294">Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data.</span><span class="sxs-lookup"><span data-stu-id="a8d71-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="a8d71-295">A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a8d71-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="a8d71-296">Se não houver nenhum erro de modelo, os dados serão salvos e o navegador será redirecionado para a Index página.</span><span class="sxs-lookup"><span data-stu-id="a8d71-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="a8d71-297">A Razor página criar</span><span class="sxs-lookup"><span data-stu-id="a8d71-297">The Create Razor Page</span></span>

<span data-ttu-id="a8d71-298">Examine o arquivo de página *páginas/filmes/Create. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="a8d71-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="a8d71-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8d71-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a8d71-300">O Visual Studio exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas:</span><span class="sxs-lookup"><span data-stu-id="a8d71-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Exibição de VS17 da página Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a8d71-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8d71-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a8d71-303">Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a8d71-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a8d71-304">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8d71-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a8d71-305">O Visual Studio para Mac exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas.</span><span class="sxs-lookup"><span data-stu-id="a8d71-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="a8d71-306">O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a8d71-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="a8d71-307">O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="a8d71-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="a8d71-308">O mecanismo scaffolding cria a Razor marcação para cada campo no modelo, exceto o ID, semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="a8d71-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="a8d71-309">Os [auxiliares de marca de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) exibem erros de validação.</span><span class="sxs-lookup"><span data-stu-id="a8d71-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="a8d71-310">A validação será abordada em mais detalhes posteriormente nesta série.</span><span class="sxs-lookup"><span data-stu-id="a8d71-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="a8d71-311">A [marca de rótulo Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) gera a legenda do rótulo e o `[for]` atributo para a `Title` propriedade.</span><span class="sxs-lookup"><span data-stu-id="a8d71-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="a8d71-312">O [auxiliar de marca de entrada](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) usa os atributos [Annotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz atributos HTML necessários para a validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="a8d71-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8d71-313">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a8d71-313">Additional resources</span></span>

* [<span data-ttu-id="a8d71-314">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="a8d71-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="a8d71-315">[Anterior: adicionar um modelo](xref:tutorials/razor-pages/model) 
>  [Em seguida: trabalhar com um banco de dados](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="a8d71-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
