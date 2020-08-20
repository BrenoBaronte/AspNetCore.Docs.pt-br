---
title: Parte 6, adicionar pesquisa a ASP.NET Core Razor páginas
author: rick-anderson
description: Parte 6 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: 5e899929372697f7a381cd493655309fa3f70f79
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633845"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="6e131-103">Parte 6, adicionar pesquisa a ASP.NET Core Razor páginas</span><span class="sxs-lookup"><span data-stu-id="6e131-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="6e131-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6e131-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="6e131-105">Nas seções a seguir, a pesquisa de filmes por *gênero* ou *nome* é adicionada.</span><span class="sxs-lookup"><span data-stu-id="6e131-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="6e131-106">Adicione as seguintes propriedades realçadas em *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e131-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="6e131-107">`SearchString`: contém o texto que os usuários inserem na caixa de texto de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="6e131-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="6e131-108">`SearchString` tem o [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="6e131-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="6e131-109">`[BindProperty]` associa valores de formulário e cadeias de consulta ao mesmo nome da propriedade.</span><span class="sxs-lookup"><span data-stu-id="6e131-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="6e131-110">`(SupportsGet = true)` é necessário para a associação em solicitações GET.</span><span class="sxs-lookup"><span data-stu-id="6e131-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="6e131-111">`Genres`: contém a lista de gêneros.</span><span class="sxs-lookup"><span data-stu-id="6e131-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="6e131-112">`Genres` permite que o usuário selecione um gênero na lista.</span><span class="sxs-lookup"><span data-stu-id="6e131-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="6e131-113">`SelectList` exige `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="6e131-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="6e131-114">`MovieGenre`: contém o gênero específico que o usuário seleciona (por exemplo, "Oeste").</span><span class="sxs-lookup"><span data-stu-id="6e131-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="6e131-115">`Genres` e `MovieGenre` são abordados mais adiante neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="6e131-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="6e131-116">Atualize o método `OnGetAsync` da página de Índice pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6e131-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="6e131-117">A primeira linha do método `OnGetAsync` cria uma consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para selecionar os filmes:</span><span class="sxs-lookup"><span data-stu-id="6e131-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="6e131-118">A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e131-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="6e131-119">Se a propriedade `SearchString` não é nula nem vazia, a consulta de filmes é modificada para filtrar a cadeia de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="6e131-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="6e131-120">O código `s => s.Title.Contains()` é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="6e131-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="6e131-121">Lambdas são usados em consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) baseadas em método como argumentos para métodos de operadores de consulta padrão, como o método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` (usado no código anterior).</span><span class="sxs-lookup"><span data-stu-id="6e131-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="6e131-122">As consultas LINQ não são executadas quando são definidas ou quando são modificadas com uma chamada a um método (como `Where`, `Contains` ou `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="6e131-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="6e131-123">Em vez disso, a execução da consulta é adiada.</span><span class="sxs-lookup"><span data-stu-id="6e131-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="6e131-124">Isso significa que a avaliação de uma expressão é atrasada até que seu valor realizado seja iterado ou o método `ToListAsync` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="6e131-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="6e131-125">Consulte [Execução de consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6e131-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e131-126">o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C#.</span><span class="sxs-lookup"><span data-stu-id="6e131-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="6e131-127">A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação.</span><span class="sxs-lookup"><span data-stu-id="6e131-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="6e131-128">No SQL Server, `Contains` é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6e131-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="6e131-129">No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6e131-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="6e131-130">Navegue para a página Movies e acrescente uma cadeia de consulta, como `?searchString=Ghost`, à URL (por exemplo, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="6e131-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="6e131-131">Os filmes filtrados são exibidos.</span><span class="sxs-lookup"><span data-stu-id="6e131-131">The filtered movies are displayed.</span></span>

![Exibição de índice](search/_static/ghost.png)

<span data-ttu-id="6e131-133">Se o modelo de rota a seguir for adicionado à página de Índice, a cadeia de caracteres de pesquisa poderá ser passada como um segmento de URL (por exemplo, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="6e131-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="6e131-134">A restrição da rota anterior permite a pesquisa do título como dados de rota (um segmento de URL), em vez de como um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="6e131-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="6e131-135">O `?` em `"{searchString?}"` significa que esse é um parâmetro de rota opcional.</span><span class="sxs-lookup"><span data-stu-id="6e131-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Exibição de índice com a palavra “ghost” adicionada à URL e uma lista de filmes retornados com dois filmes, Ghostbusters e Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="6e131-137">O runtime do ASP.NET Core usa o [model binding](xref:mvc/models/model-binding) para definir o valor da propriedade `SearchString` na cadeia de consulta (`?searchString=Ghost`) ou nos dados de rota (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="6e131-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="6e131-138">O model binding não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6e131-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="6e131-139">No entanto, você não pode esperar que os usuários modifiquem a URL para pesquisar um filme.</span><span class="sxs-lookup"><span data-stu-id="6e131-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="6e131-140">Nesta etapa, a interface do usuário é adicionada para filtrar filmes.</span><span class="sxs-lookup"><span data-stu-id="6e131-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="6e131-141">Se você adicionou a restrição de rota `"{searchString?}"`, remova-a.</span><span class="sxs-lookup"><span data-stu-id="6e131-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="6e131-142">Abra o arquivo *Pages/Movies/Index.cshtml* e, em seguida, adicione a marcação `<form>` realçada no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6e131-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="6e131-143">A marca `<form>` HTML usa os seguintes [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="6e131-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="6e131-144">[Auxiliar de Marcas de Formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="6e131-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="6e131-145">Quando o formulário é enviado, a cadeia de caracteres de filtro é enviada para a página *Pages/Movies/Index* por meio da cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="6e131-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="6e131-146">Auxiliar de marcação de entrada</span><span class="sxs-lookup"><span data-stu-id="6e131-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="6e131-147">Salve as alterações e teste o filtro.</span><span class="sxs-lookup"><span data-stu-id="6e131-147">Save the changes and test the filter.</span></span>

![Exibição de índice com a palavra “ghost” digitada na caixa de texto Filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="6e131-149">Pesquisar por gênero</span><span class="sxs-lookup"><span data-stu-id="6e131-149">Search by genre</span></span>

<span data-ttu-id="6e131-150">Atualize o método `OnGetAsync` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6e131-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="6e131-151">O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e131-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="6e131-152">O `SelectList` de gêneros é criado com a projeção dos gêneros distintos.</span><span class="sxs-lookup"><span data-stu-id="6e131-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="6e131-153">Adicionar pesquisa por gênero à Razor página</span><span class="sxs-lookup"><span data-stu-id="6e131-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="6e131-154">Atualize *Index.cshtml* da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6e131-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="6e131-155">Teste o aplicativo pesquisando por gênero, título do filme e por ambos.</span><span class="sxs-lookup"><span data-stu-id="6e131-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e131-156">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6e131-156">Additional resources</span></span>

* [<span data-ttu-id="6e131-157">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="6e131-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="6e131-158">[Anterior: atualizando as páginas](xref:tutorials/razor-pages/da1) 
>  [Em seguida: adicionando um novo campo](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="6e131-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="6e131-159">Nas seções a seguir, a pesquisa de filmes por *gênero* ou *nome* é adicionada.</span><span class="sxs-lookup"><span data-stu-id="6e131-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="6e131-160">Adicione as seguintes propriedades realçadas em *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e131-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="6e131-161">`SearchString`: contém o texto que os usuários inserem na caixa de texto de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="6e131-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="6e131-162">`SearchString` tem o [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="6e131-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="6e131-163">`[BindProperty]` associa valores de formulário e cadeias de consulta ao mesmo nome da propriedade.</span><span class="sxs-lookup"><span data-stu-id="6e131-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="6e131-164">`(SupportsGet = true)` é necessário para a associação em solicitações GET.</span><span class="sxs-lookup"><span data-stu-id="6e131-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="6e131-165">`Genres`: contém a lista de gêneros.</span><span class="sxs-lookup"><span data-stu-id="6e131-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="6e131-166">`Genres` permite que o usuário selecione um gênero na lista.</span><span class="sxs-lookup"><span data-stu-id="6e131-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="6e131-167">`SelectList` exige `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="6e131-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="6e131-168">`MovieGenre`: contém o gênero específico que o usuário seleciona (por exemplo, "Oeste").</span><span class="sxs-lookup"><span data-stu-id="6e131-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="6e131-169">`Genres` e `MovieGenre` são abordados mais adiante neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="6e131-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="6e131-170">Atualize o método `OnGetAsync` da página de Índice pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6e131-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="6e131-171">A primeira linha do método `OnGetAsync` cria uma consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para selecionar os filmes:</span><span class="sxs-lookup"><span data-stu-id="6e131-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="6e131-172">A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e131-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="6e131-173">Se a propriedade `SearchString` não é nula nem vazia, a consulta de filmes é modificada para filtrar a cadeia de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="6e131-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="6e131-174">O código `s => s.Title.Contains()` é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="6e131-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="6e131-175">Lambdas são usados em consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) baseadas em método como argumentos para métodos de operadores de consulta padrão, como o método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` (usado no código anterior).</span><span class="sxs-lookup"><span data-stu-id="6e131-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="6e131-176">As consultas LINQ não são executadas quando são definidas ou quando são modificadas com uma chamada a um método (como `Where`, `Contains` ou `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="6e131-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="6e131-177">Em vez disso, a execução da consulta é adiada.</span><span class="sxs-lookup"><span data-stu-id="6e131-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="6e131-178">Isso significa que a avaliação de uma expressão é atrasada até que seu valor realizado seja iterado ou o método `ToListAsync` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="6e131-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="6e131-179">Consulte [Execução de consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6e131-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="6e131-180">**Observação:** o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C#.</span><span class="sxs-lookup"><span data-stu-id="6e131-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="6e131-181">A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação.</span><span class="sxs-lookup"><span data-stu-id="6e131-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="6e131-182">No SQL Server, `Contains` é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6e131-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="6e131-183">No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6e131-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="6e131-184">Navegue para a página Movies e acrescente uma cadeia de consulta, como `?searchString=Ghost`, à URL (por exemplo, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="6e131-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="6e131-185">Os filmes filtrados são exibidos.</span><span class="sxs-lookup"><span data-stu-id="6e131-185">The filtered movies are displayed.</span></span>

![Exibição de índice](search/_static/ghost.png)

<span data-ttu-id="6e131-187">Se o modelo de rota a seguir for adicionado à página de Índice, a cadeia de caracteres de pesquisa poderá ser passada como um segmento de URL (por exemplo, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="6e131-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="6e131-188">A restrição da rota anterior permite a pesquisa do título como dados de rota (um segmento de URL), em vez de como um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="6e131-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="6e131-189">O `?` em `"{searchString?}"` significa que esse é um parâmetro de rota opcional.</span><span class="sxs-lookup"><span data-stu-id="6e131-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Exibição de índice com a palavra “ghost” adicionada à URL e uma lista de filmes retornados com dois filmes, Ghostbusters e Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="6e131-191">O runtime do ASP.NET Core usa o [model binding](xref:mvc/models/model-binding) para definir o valor da propriedade `SearchString` na cadeia de consulta (`?searchString=Ghost`) ou nos dados de rota (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="6e131-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="6e131-192">O model binding não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6e131-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="6e131-193">No entanto, você não pode esperar que os usuários modifiquem a URL para pesquisar um filme.</span><span class="sxs-lookup"><span data-stu-id="6e131-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="6e131-194">Nesta etapa, a interface do usuário é adicionada para filtrar filmes.</span><span class="sxs-lookup"><span data-stu-id="6e131-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="6e131-195">Se você adicionou a restrição de rota `"{searchString?}"`, remova-a.</span><span class="sxs-lookup"><span data-stu-id="6e131-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="6e131-196">Abra o arquivo *Pages/Movies/Index.cshtml* e, em seguida, adicione a marcação `<form>` realçada no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6e131-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="6e131-197">A marca `<form>` HTML usa os seguintes [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="6e131-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="6e131-198">[Auxiliar de Marcas de Formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="6e131-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="6e131-199">Quando o formulário é enviado, a cadeia de caracteres de filtro é enviada para a página *Pages/Movies/Index* por meio da cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="6e131-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="6e131-200">Auxiliar de marcação de entrada</span><span class="sxs-lookup"><span data-stu-id="6e131-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="6e131-201">Salve as alterações e teste o filtro.</span><span class="sxs-lookup"><span data-stu-id="6e131-201">Save the changes and test the filter.</span></span>

![Exibição de índice com a palavra “ghost” digitada na caixa de texto Filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="6e131-203">Pesquisar por gênero</span><span class="sxs-lookup"><span data-stu-id="6e131-203">Search by genre</span></span>

<span data-ttu-id="6e131-204">Atualize o método `OnGetAsync` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6e131-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="6e131-205">O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e131-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="6e131-206">O `SelectList` de gêneros é criado com a projeção dos gêneros distintos.</span><span class="sxs-lookup"><span data-stu-id="6e131-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="6e131-207">Adicionar pesquisa por gênero à Razor página</span><span class="sxs-lookup"><span data-stu-id="6e131-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="6e131-208">Atualize *Index.cshtml* da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6e131-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="6e131-209">Teste o aplicativo pesquisando por gênero, título do filme e por ambos.</span><span class="sxs-lookup"><span data-stu-id="6e131-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="6e131-210">O código anterior usa o auxiliar [Select tag](xref:mvc/views/working-with-forms#the-select-tag-helper) e a marca Option Helper.</span><span class="sxs-lookup"><span data-stu-id="6e131-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e131-211">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6e131-211">Additional resources</span></span>

* [<span data-ttu-id="6e131-212">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="6e131-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="6e131-213">[Anterior: atualizando as páginas](xref:tutorials/razor-pages/da1) 
>  [Em seguida: adicionando um novo campo](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="6e131-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
