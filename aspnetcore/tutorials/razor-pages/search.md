---
title: Parte 6, adicionar pesquisa
author: rick-anderson
description: Parte 6 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 12/05/2019
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: d852766c9706941a1a5f4f3af2c9293ffc4e6a26
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486207"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="97641-103">Parte 6, adicionar pesquisa a ASP.NET Core Razor páginas</span><span class="sxs-lookup"><span data-stu-id="97641-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="97641-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="97641-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="97641-105">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="97641-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="97641-106">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="97641-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="97641-107">Nas seções a seguir, a pesquisa de filmes por *gênero* ou *nome* é adicionada.</span><span class="sxs-lookup"><span data-stu-id="97641-107">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="97641-108">Adicione o seguinte realçado usando a instrução e as propriedades a *pages/Movies/ Index . cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="97641-108">Add the following highlighted using statement and properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

<span data-ttu-id="97641-109">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="97641-109">In the previous code:</span></span>

* <span data-ttu-id="97641-110">`SearchString`: Contém o texto que os usuários inserem na caixa de texto de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="97641-110">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="97641-111">`SearchString` tem o [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="97641-111">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="97641-112">`[BindProperty]` associa valores de formulário e cadeias de consulta ao mesmo nome da propriedade.</span><span class="sxs-lookup"><span data-stu-id="97641-112">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="97641-113">`[BindProperty(SupportsGet = true)]` é necessário para a associação em solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="97641-113">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="97641-114">`Genres`: Contém a lista de gêneros.</span><span class="sxs-lookup"><span data-stu-id="97641-114">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="97641-115">`Genres` permite que o usuário selecione um gênero na lista.</span><span class="sxs-lookup"><span data-stu-id="97641-115">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="97641-116">`SelectList` exige `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="97641-116">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="97641-117">`MovieGenre`: Contém o gênero específico que o usuário seleciona.</span><span class="sxs-lookup"><span data-stu-id="97641-117">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="97641-118">Por exemplo, "ocidental".</span><span class="sxs-lookup"><span data-stu-id="97641-118">For example, "Western".</span></span>
* <span data-ttu-id="97641-119">`Genres` e `MovieGenre` são abordados mais adiante neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="97641-119">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="97641-120">Atualize o Index método da página `OnGetAsync` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="97641-120">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="97641-121">A primeira linha do método `OnGetAsync` cria uma consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para selecionar os filmes:</span><span class="sxs-lookup"><span data-stu-id="97641-121">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="97641-122">A consulta é apenas ***definida** _ neste ponto, ela _*_não_\*_ foi executada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="97641-122">The query is only ***defined** _ at this point, it has _*_not_\*_ been run against the database.</span></span>

<span data-ttu-id="97641-123">Se a propriedade `SearchString` não é nula nem vazia, a consulta de filmes é modificada para filtrar a cadeia de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="97641-123">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="97641-124">O código `s => s.Title.Contains()` é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="97641-124">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="97641-125">As Lambdas são usadas em consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) baseadas em método como argumentos para métodos de operador de consulta padrão, como o método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` .</span><span class="sxs-lookup"><span data-stu-id="97641-125">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="97641-126">Consultas LINQ não são executadas quando são definidas ou quando são modificadas chamando um método, como `Where` , `Contains` ou `OrderBy` .</span><span class="sxs-lookup"><span data-stu-id="97641-126">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="97641-127">Em vez disso, a execução da consulta é adiada.</span><span class="sxs-lookup"><span data-stu-id="97641-127">Rather, query execution is deferred.</span></span> <span data-ttu-id="97641-128">A avaliação de uma expressão é atrasada até que seu valor percebido seja iterado ou o `ToListAsync` método seja chamado.</span><span class="sxs-lookup"><span data-stu-id="97641-128">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="97641-129">Consulte [Execução de consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="97641-129">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="97641-130">o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C#.</span><span class="sxs-lookup"><span data-stu-id="97641-130">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="97641-131">A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação.</span><span class="sxs-lookup"><span data-stu-id="97641-131">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="97641-132">No SQL Server, `Contains` é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="97641-132">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="97641-133">No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="97641-133">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="97641-134">Navegue até a página filmes e acrescente uma cadeia de caracteres de consulta, como `?searchString=Ghost` à URL.</span><span class="sxs-lookup"><span data-stu-id="97641-134">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="97641-135">Por exemplo, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="97641-135">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="97641-136">Os filmes filtrados são exibidos.</span><span class="sxs-lookup"><span data-stu-id="97641-136">The filtered movies are displayed.</span></span>

![::: no-Loc (índice)::: exibição](search/_static/ghost.png)

<span data-ttu-id="97641-138">Se o modelo de rota a seguir for adicionado à Index página, a cadeia de caracteres de pesquisa poderá ser passada como um segmento de URL.</span><span class="sxs-lookup"><span data-stu-id="97641-138">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="97641-139">Por exemplo, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="97641-139">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="97641-140">A restrição da rota anterior permite a pesquisa do título como dados de rota (um segmento de URL), em vez de como um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="97641-140">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="97641-141">O `?` em `"{searchString?}"` significa que esse é um parâmetro de rota opcional.</span><span class="sxs-lookup"><span data-stu-id="97641-141">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![::: no-Loc (index)::: View com a palavra fantasma adicionada à URL e uma lista de filmes retornada de dois filmes, Ghostbusters e Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="97641-143">O runtime do ASP.NET Core usa o [model binding](xref:mvc/models/model-binding) para definir o valor da propriedade `SearchString` na cadeia de consulta (`?searchString=Ghost`) ou nos dados de rota (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="97641-143">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="97641-144">A associação de modelo _*_não_*_ diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="97641-144">Model binding is _*_not_*_ case sensitive.</span></span>

<span data-ttu-id="97641-145">No entanto, não se espera que os usuários modifiquem a URL para pesquisar um filme.</span><span class="sxs-lookup"><span data-stu-id="97641-145">However, users cannot be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="97641-146">Nesta etapa, a interface do usuário é adicionada para filtrar filmes.</span><span class="sxs-lookup"><span data-stu-id="97641-146">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="97641-147">Se você adicionou a restrição de rota `"{searchString?}"`, remova-a.</span><span class="sxs-lookup"><span data-stu-id="97641-147">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="97641-148">Abra o arquivo _Pages/Movies/ Index . cshtml \* e adicione a marcação realçada no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="97641-148">Open the _Pages/Movies/Index.cshtml\* file, and add the markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="97641-149">A marca `<form>` HTML usa os seguintes [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="97641-149">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="97641-150">[Auxiliar de Marcas de Formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="97641-150">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="97641-151">Quando o formulário é enviado, a cadeia de caracteres de filtro é enviada para as *páginas Index /filmes/* página por meio da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="97641-151">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="97641-152">Auxiliar de marcação de entrada</span><span class="sxs-lookup"><span data-stu-id="97641-152">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="97641-153">Salve as alterações e teste o filtro.</span><span class="sxs-lookup"><span data-stu-id="97641-153">Save the changes and test the filter.</span></span>

![::: no-Loc (índice)::: exibição com a palavra fantasma digitada na caixa de texto filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="97641-155">Pesquisar por gênero</span><span class="sxs-lookup"><span data-stu-id="97641-155">Search by genre</span></span>

<span data-ttu-id="97641-156">Atualize o Index método da página `OnGetAsync` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="97641-156">Update the Index page's `OnGetAsync` method with the following code:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="97641-157">O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="97641-157">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="97641-158">O `SelectList` de gêneros é criado com a projeção dos gêneros distintos.</span><span class="sxs-lookup"><span data-stu-id="97641-158">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="97641-159">Adicionar pesquisa por gênero à Razor página</span><span class="sxs-lookup"><span data-stu-id="97641-159">Add search by genre to the Razor Page</span></span>

1. <span data-ttu-id="97641-160">Atualize o *Index . cshtml* [ `<form>` Element] ( https://developer.mozilla.org/docs/Web/HTML/Element/form) conforme realçado na seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="97641-160">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. <span data-ttu-id="97641-161">Teste o aplicativo pesquisando por gênero, título do filme e por ambos.</span><span class="sxs-lookup"><span data-stu-id="97641-161">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97641-162">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="97641-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="97641-163">[Anterior: atualizar as páginas](xref:tutorials/razor-pages/da1) 
>  [Em seguida: adicionar um novo campo](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="97641-163">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="97641-164">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="97641-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="97641-165">Nas seções a seguir, a pesquisa de filmes por *gênero* ou *nome* é adicionada.</span><span class="sxs-lookup"><span data-stu-id="97641-165">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="97641-166">Adicione as seguintes propriedades realçadas a *pages/Movies/ Index . cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="97641-166">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="97641-167">`SearchString`: Contém o texto que os usuários inserem na caixa de texto de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="97641-167">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="97641-168">`SearchString` tem o [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="97641-168">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="97641-169">`[BindProperty]` associa valores de formulário e cadeias de consulta ao mesmo nome da propriedade.</span><span class="sxs-lookup"><span data-stu-id="97641-169">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="97641-170">`[BindProperty(SupportsGet = true)]` é necessário para a associação em solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="97641-170">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="97641-171">`Genres`: Contém a lista de gêneros.</span><span class="sxs-lookup"><span data-stu-id="97641-171">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="97641-172">`Genres` permite que o usuário selecione um gênero na lista.</span><span class="sxs-lookup"><span data-stu-id="97641-172">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="97641-173">`SelectList` exige `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="97641-173">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="97641-174">`MovieGenre`: Contém o gênero específico que o usuário seleciona.</span><span class="sxs-lookup"><span data-stu-id="97641-174">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="97641-175">Por exemplo, "ocidental".</span><span class="sxs-lookup"><span data-stu-id="97641-175">For example, "Western".</span></span>
* <span data-ttu-id="97641-176">`Genres` e `MovieGenre` são abordados mais adiante neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="97641-176">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="97641-177">Atualize o Index método da página `OnGetAsync` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="97641-177">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="97641-178">A primeira linha do método `OnGetAsync` cria uma consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para selecionar os filmes:</span><span class="sxs-lookup"><span data-stu-id="97641-178">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="97641-179">A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="97641-179">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="97641-180">Se a propriedade `SearchString` não é nula nem vazia, a consulta de filmes é modificada para filtrar a cadeia de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="97641-180">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="97641-181">O código `s => s.Title.Contains()` é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="97641-181">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="97641-182">As Lambdas são usadas em consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) baseadas em método como argumentos para métodos de operador de consulta padrão, como o método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` .</span><span class="sxs-lookup"><span data-stu-id="97641-182">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="97641-183">Consultas LINQ não são executadas quando são definidas ou quando são modificadas chamando um método, como `Where` `Contains`  ou `OrderBy` .</span><span class="sxs-lookup"><span data-stu-id="97641-183">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="97641-184">Em vez disso, a execução da consulta é adiada.</span><span class="sxs-lookup"><span data-stu-id="97641-184">Rather, query execution is deferred.</span></span> <span data-ttu-id="97641-185">A avaliação de uma expressão é atrasada até que seu valor percebido seja iterado ou o `ToListAsync` método seja chamado.</span><span class="sxs-lookup"><span data-stu-id="97641-185">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="97641-186">Consulte [Execução de consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="97641-186">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="97641-187">**Observação:** o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C#.</span><span class="sxs-lookup"><span data-stu-id="97641-187">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="97641-188">A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação.</span><span class="sxs-lookup"><span data-stu-id="97641-188">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="97641-189">No SQL Server, `Contains` é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="97641-189">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="97641-190">No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="97641-190">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="97641-191">Navegue até a página filmes e acrescente uma cadeia de caracteres de consulta, como `?searchString=Ghost` à URL.</span><span class="sxs-lookup"><span data-stu-id="97641-191">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="97641-192">Por exemplo, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="97641-192">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="97641-193">Os filmes filtrados são exibidos.</span><span class="sxs-lookup"><span data-stu-id="97641-193">The filtered movies are displayed.</span></span>

![::: no-Loc (índice)::: exibição](search/_static/ghost.png)

<span data-ttu-id="97641-195">Se o modelo de rota a seguir for adicionado à Index página, a cadeia de caracteres de pesquisa poderá ser passada como um segmento de URL.</span><span class="sxs-lookup"><span data-stu-id="97641-195">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="97641-196">Por exemplo, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="97641-196">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="97641-197">A restrição da rota anterior permite a pesquisa do título como dados de rota (um segmento de URL), em vez de como um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="97641-197">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="97641-198">O `?` em `"{searchString?}"` significa que esse é um parâmetro de rota opcional.</span><span class="sxs-lookup"><span data-stu-id="97641-198">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![::: no-Loc (index)::: View com a palavra fantasma adicionada à URL e uma lista de filmes retornada de dois filmes, Ghostbusters e Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="97641-200">O runtime do ASP.NET Core usa o [model binding](xref:mvc/models/model-binding) para definir o valor da propriedade `SearchString` na cadeia de consulta (`?searchString=Ghost`) ou nos dados de rota (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="97641-200">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="97641-201">A associação de modelo é \**_não_* diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="97641-201">Model binding is \**_not_* _ case sensitive.</span></span>

<span data-ttu-id="97641-202">No entanto, não se espera que os usuários modifiquem a URL para pesquisar um filme.</span><span class="sxs-lookup"><span data-stu-id="97641-202">However, users can't be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="97641-203">Nesta etapa, a interface do usuário é adicionada para filtrar filmes.</span><span class="sxs-lookup"><span data-stu-id="97641-203">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="97641-204">Se você adicionou a restrição de rota `"{searchString?}"`, remova-a.</span><span class="sxs-lookup"><span data-stu-id="97641-204">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="97641-205">Abra o arquivo _Pages/Movies/ Index . cshtml \* e adicione a `<form>` marcação realçada no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="97641-205">Open the _Pages/Movies/Index.cshtml\* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="97641-206">A marca `<form>` HTML usa os seguintes [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="97641-206">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="97641-207">[Auxiliar de Marcas de Formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="97641-207">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="97641-208">Quando o formulário é enviado, a cadeia de caracteres de filtro é enviada para as *páginas Index /filmes/* página por meio da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="97641-208">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="97641-209">Auxiliar de marcação de entrada</span><span class="sxs-lookup"><span data-stu-id="97641-209">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="97641-210">Salve as alterações e teste o filtro.</span><span class="sxs-lookup"><span data-stu-id="97641-210">Save the changes and test the filter.</span></span>

![::: no-Loc (índice)::: exibição com a palavra fantasma digitada na caixa de texto filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="97641-212">Pesquisar por gênero</span><span class="sxs-lookup"><span data-stu-id="97641-212">Search by genre</span></span>

<span data-ttu-id="97641-213">Atualize o método `OnGetAsync` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="97641-213">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="97641-214">O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="97641-214">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="97641-215">O `SelectList` de gêneros é criado com a projeção dos gêneros distintos.</span><span class="sxs-lookup"><span data-stu-id="97641-215">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="97641-216">Adicionar pesquisa por gênero à Razor página</span><span class="sxs-lookup"><span data-stu-id="97641-216">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="97641-217">Atualize o *Index . cshtml* [ `<form>` Element] ( https://developer.mozilla.org/docs/Web/HTML/Element/form) conforme realçado na seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="97641-217">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="97641-218">Teste o aplicativo pesquisando por gênero, título do filme e por ambos.</span><span class="sxs-lookup"><span data-stu-id="97641-218">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="97641-219">O código anterior usa o auxiliar [Select tag](xref:mvc/views/working-with-forms#the-select-tag-helper) e a marca Option Helper.</span><span class="sxs-lookup"><span data-stu-id="97641-219">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97641-220">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="97641-220">Additional resources</span></span>

* [<span data-ttu-id="97641-221">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="97641-221">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="97641-222">[Anterior: atualizar as páginas](xref:tutorials/razor-pages/da1) 
>  [Em seguida: adicionar um novo campo](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="97641-222">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
