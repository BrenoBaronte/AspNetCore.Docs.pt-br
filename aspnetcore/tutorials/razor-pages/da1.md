---
title: Parte 5, atualizar as páginas geradas em um aplicativo ASP.NET Core
author: rick-anderson
description: Parte 5 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 12/20/2018
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 04479e5c3a0b1e9badbb4e58043cf059beefb4ab
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632792"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="77288-103">Parte 5, atualizar as páginas geradas em um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77288-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="77288-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="77288-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77288-105">O aplicativo de filme gerado por scaffolding tem um bom começo, mas a apresentação não é ideal.</span><span class="sxs-lookup"><span data-stu-id="77288-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="77288-106">**ReleaseDate** deveria ser **Data de Lançamento** (duas palavras).</span><span class="sxs-lookup"><span data-stu-id="77288-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplicativo de filme aberto no Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="77288-108">Atualizar o código gerado</span><span class="sxs-lookup"><span data-stu-id="77288-108">Update the generated code</span></span>

<span data-ttu-id="77288-109">Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="77288-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="77288-110">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` permite que o Entity Framework Core mapeie o `Price` corretamente para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="77288-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="77288-111">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="77288-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="77288-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) é abordado no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="77288-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="77288-113">O atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica o que deve ser exibido no nome de um campo (neste caso, “Release Date” em vez de “ReleaseDate”).</span><span class="sxs-lookup"><span data-stu-id="77288-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="77288-114">O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo de dados (Data) e, portanto, as informações de hora armazenadas no campo não são exibidas.</span><span class="sxs-lookup"><span data-stu-id="77288-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="77288-115">Procure Pages/Movies e focalize um link **Editar** para ver a URL de destino.</span><span class="sxs-lookup"><span data-stu-id="77288-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="77288-117">Os links **Editar**, **Detalhes** e **Excluir** são gerados pelo [Auxiliar de Marcação de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) no arquivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="77288-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="77288-118">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="77288-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="77288-119">No código anterior, o `AnchorTagHelper` gera dinamicamente o valor do `href` atributo HTML da Razor página (a rota é relativa), a `asp-page` e a ID da rota ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="77288-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="77288-120">Consulte [Geração de URL para Páginas](xref:razor-pages/index#url-generation-for-pages) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="77288-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="77288-121">Use **Exibir Código-fonte** em seu navegador favorito para examinar a marcação gerada.</span><span class="sxs-lookup"><span data-stu-id="77288-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="77288-122">Uma parte do HTML gerado é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="77288-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="77288-123">Os links gerados dinamicamente passam a ID de filme com uma cadeia de consulta (por exemplo, o `?id=1` em `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="77288-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="77288-124">Adicionar modelo de rota</span><span class="sxs-lookup"><span data-stu-id="77288-124">Add route template</span></span>

<span data-ttu-id="77288-125">Atualize as páginas editar, detalhes e excluir Razor para usar o modelo de rota "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="77288-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="77288-126">Altere a diretiva de página de cada uma dessas páginas de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="77288-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="77288-127">Execute o aplicativo e, em seguida, exiba o código-fonte.</span><span class="sxs-lookup"><span data-stu-id="77288-127">Run the app and then view source.</span></span> <span data-ttu-id="77288-128">O HTML gerado adiciona a ID à parte do caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="77288-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="77288-129">Uma solicitação para a página com o modelo de rota “{id:int}” que **não** inclui o inteiro retornará um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="77288-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="77288-130">Por exemplo, `http://localhost:5000/Movies/Details` retornará um erro 404.</span><span class="sxs-lookup"><span data-stu-id="77288-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="77288-131">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="77288-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="77288-132">Para testar o comportamento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="77288-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="77288-133">defina a diretiva de página em *Pages/Movies/Details.cshtml* como `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="77288-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="77288-134">Defina um ponto de interrupção em `public async Task<IActionResult> OnGetAsync(int? id)` (em *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="77288-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="77288-135">Navegue até `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="77288-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="77288-136">Com a diretiva `@page "{id:int}"`, o ponto de interrupção nunca é atingido.</span><span class="sxs-lookup"><span data-stu-id="77288-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="77288-137">O mecanismo de roteamento retorna HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="77288-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="77288-138">Usando `@page "{id:int?}"`, o método `OnGetAsync` retorna `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="77288-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="77288-139">Examinar o tratamento de exceção de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="77288-139">Review concurrency exception handling</span></span>

<span data-ttu-id="77288-140">Examine o método `OnPostAsync` no arquivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="77288-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="77288-141">O código anterior detecta exceções de simultaneidade quando um cliente exclui o filme e o outro cliente posta alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="77288-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="77288-142">Para testar o bloco `catch`:</span><span class="sxs-lookup"><span data-stu-id="77288-142">To test the `catch` block:</span></span>

* <span data-ttu-id="77288-143">Definir um ponto de interrupção em `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="77288-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="77288-144">Selecione **Editar** para um filme, faça alterações, mas não insira **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="77288-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="77288-145">Em outra janela do navegador, selecione o link **Excluir** do mesmo filme e, em seguida, exclua o filme.</span><span class="sxs-lookup"><span data-stu-id="77288-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="77288-146">Na janela do navegador anterior, poste as alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="77288-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="77288-147">O código de produção talvez deseje detectar conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="77288-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="77288-148">Confira [Lidar com conflitos de simultaneidade](xref:data/ef-rp/concurrency) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="77288-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="77288-149">Análise de postagem e associação</span><span class="sxs-lookup"><span data-stu-id="77288-149">Posting and binding review</span></span>

<span data-ttu-id="77288-150">Examine o arquivo *Pages/Movies/Edit.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="77288-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="77288-151">Quando uma solicitação HTTP GET é feita para a página Movies/Edit (por exemplo, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="77288-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="77288-152">O método `OnGetAsync` busca o filme do banco de dados e retorna o método `Page`.</span><span class="sxs-lookup"><span data-stu-id="77288-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="77288-153">O `Page` método renderiza a página *pages/Movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="77288-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="77288-154">O arquivo *Pages/Movies/Edit.cshtml* contém a diretiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que disponibiliza o modelo de filme na página.</span><span class="sxs-lookup"><span data-stu-id="77288-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="77288-155">O formulário Editar é exibido com os valores do filme.</span><span class="sxs-lookup"><span data-stu-id="77288-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="77288-156">Quando a página Movies/Edit é postada:</span><span class="sxs-lookup"><span data-stu-id="77288-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="77288-157">Os valores de formulário na página são associados à propriedade `Movie`.</span><span class="sxs-lookup"><span data-stu-id="77288-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="77288-158">O atributo `[BindProperty]` habilita o [Model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="77288-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="77288-159">Se há erros no estado do modelo (por exemplo, `ReleaseDate` não pode ser convertido em uma data), o formulário é exibido novamente com os valores enviados.</span><span class="sxs-lookup"><span data-stu-id="77288-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="77288-160">Se não houver erros do modelo, o filme será salvo.</span><span class="sxs-lookup"><span data-stu-id="77288-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="77288-161">Os métodos GET HTTP nas páginas index, Create e Delete Razor seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="77288-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="77288-162">O método HTTP POST `OnPostAsync` na página Criar Razor segue um padrão semelhante ao `OnPostAsync` método na Razor página Editar.</span><span class="sxs-lookup"><span data-stu-id="77288-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77288-163">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="77288-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="77288-164">[Anterior: trabalhando com um banco de dados](xref:tutorials/razor-pages/sql) 
>  [Próximo: Adicionar pesquisa](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="77288-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77288-165">O aplicativo de filme gerado por scaffolding tem um bom começo, mas a apresentação não é ideal.</span><span class="sxs-lookup"><span data-stu-id="77288-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="77288-166">**ReleaseDate** deveria ser **Data de Lançamento** (duas palavras).</span><span class="sxs-lookup"><span data-stu-id="77288-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplicativo de filme aberto no Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="77288-168">Atualizar o código gerado</span><span class="sxs-lookup"><span data-stu-id="77288-168">Update the generated code</span></span>

<span data-ttu-id="77288-169">Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="77288-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="77288-170">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` permite que o Entity Framework Core mapeie o `Price` corretamente para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="77288-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="77288-171">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="77288-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="77288-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) é abordado no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="77288-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="77288-173">O atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica o que deve ser exibido no nome de um campo (neste caso, “Release Date” em vez de “ReleaseDate”).</span><span class="sxs-lookup"><span data-stu-id="77288-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="77288-174">O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo de dados (Data) e, portanto, as informações de hora armazenadas no campo não são exibidas.</span><span class="sxs-lookup"><span data-stu-id="77288-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="77288-175">Procure Pages/Movies e focalize um link **Editar** para ver a URL de destino.</span><span class="sxs-lookup"><span data-stu-id="77288-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="77288-177">Os links **Editar**, **Detalhes** e **Excluir** são gerados pelo [Auxiliar de Marcação de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) no arquivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="77288-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="77288-178">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="77288-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="77288-179">No código anterior, o `AnchorTagHelper` gera dinamicamente o valor do `href` atributo HTML da Razor página (a rota é relativa), a `asp-page` e a ID da rota ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="77288-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="77288-180">Consulte [Geração de URL para Páginas](xref:razor-pages/index#url-generation-for-pages) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="77288-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="77288-181">Use **Exibir Código-fonte** em seu navegador favorito para examinar a marcação gerada.</span><span class="sxs-lookup"><span data-stu-id="77288-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="77288-182">Uma parte do HTML gerado é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="77288-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="77288-183">Os links gerados dinamicamente passam a ID de filme com uma cadeia de consulta (por exemplo, o `?id=1` em `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="77288-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="77288-184">Atualize as páginas editar, detalhes e excluir Razor para usar o modelo de rota "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="77288-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="77288-185">Altere a diretiva de página de cada uma dessas páginas de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="77288-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="77288-186">Execute o aplicativo e, em seguida, exiba o código-fonte.</span><span class="sxs-lookup"><span data-stu-id="77288-186">Run the app and then view source.</span></span> <span data-ttu-id="77288-187">O HTML gerado adiciona a ID à parte do caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="77288-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="77288-188">Uma solicitação para a página com o modelo de rota “{id:int}” que **não** inclui o inteiro retornará um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="77288-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="77288-189">Por exemplo, `http://localhost:5000/Movies/Details` retornará um erro 404.</span><span class="sxs-lookup"><span data-stu-id="77288-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="77288-190">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="77288-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="77288-191">Para testar o comportamento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="77288-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="77288-192">defina a diretiva de página em *Pages/Movies/Details.cshtml* como `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="77288-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="77288-193">Defina um ponto de interrupção em `public async Task<IActionResult> OnGetAsync(int? id)` (em *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="77288-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="77288-194">Navegue até `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="77288-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="77288-195">Com a diretiva `@page "{id:int}"`, o ponto de interrupção nunca é atingido.</span><span class="sxs-lookup"><span data-stu-id="77288-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="77288-196">O mecanismo de roteamento retorna HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="77288-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="77288-197">Usando `@page "{id:int?}"`, o método `OnGetAsync` retorna `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="77288-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="77288-198">Examinar o tratamento de exceção de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="77288-198">Review concurrency exception handling</span></span>

<span data-ttu-id="77288-199">Examine o método `OnPostAsync` no arquivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="77288-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="77288-200">O código anterior detecta exceções de simultaneidade quando um cliente exclui o filme e o outro cliente posta alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="77288-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="77288-201">Para testar o bloco `catch`:</span><span class="sxs-lookup"><span data-stu-id="77288-201">To test the `catch` block:</span></span>

* <span data-ttu-id="77288-202">Definir um ponto de interrupção em `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="77288-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="77288-203">Selecione **Editar** para um filme, faça alterações, mas não insira **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="77288-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="77288-204">Em outra janela do navegador, selecione o link **Excluir** do mesmo filme e, em seguida, exclua o filme.</span><span class="sxs-lookup"><span data-stu-id="77288-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="77288-205">Na janela do navegador anterior, poste as alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="77288-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="77288-206">O código de produção talvez deseje detectar conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="77288-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="77288-207">Confira [Lidar com conflitos de simultaneidade](xref:data/ef-rp/concurrency) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="77288-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="77288-208">Análise de postagem e associação</span><span class="sxs-lookup"><span data-stu-id="77288-208">Posting and binding review</span></span>

<span data-ttu-id="77288-209">Examine o arquivo *Pages/Movies/Edit.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="77288-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="77288-210">Quando uma solicitação HTTP GET é feita para a página Movies/Edit (por exemplo, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="77288-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="77288-211">O método `OnGetAsync` busca o filme do banco de dados e retorna o método `Page`.</span><span class="sxs-lookup"><span data-stu-id="77288-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="77288-212">O `Page` método renderiza a página *pages/Movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="77288-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="77288-213">O arquivo *Pages/Movies/Edit.cshtml* contém a diretiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que disponibiliza o modelo de filme na página.</span><span class="sxs-lookup"><span data-stu-id="77288-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="77288-214">O formulário Editar é exibido com os valores do filme.</span><span class="sxs-lookup"><span data-stu-id="77288-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="77288-215">Quando a página Movies/Edit é postada:</span><span class="sxs-lookup"><span data-stu-id="77288-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="77288-216">Os valores de formulário na página são associados à propriedade `Movie`.</span><span class="sxs-lookup"><span data-stu-id="77288-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="77288-217">O atributo `[BindProperty]` habilita o [Model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="77288-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="77288-218">Se houver erros no estado do modelo (por exemplo, `ReleaseDate` não pode ser convertido em uma data), o formulário será mostrado com os valores enviados.</span><span class="sxs-lookup"><span data-stu-id="77288-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="77288-219">Se não houver erros do modelo, o filme será salvo.</span><span class="sxs-lookup"><span data-stu-id="77288-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="77288-220">Os métodos GET HTTP nas páginas index, Create e Delete Razor seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="77288-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="77288-221">O método HTTP POST `OnPostAsync` na página Criar Razor segue um padrão semelhante ao `OnPostAsync` método na Razor página Editar.</span><span class="sxs-lookup"><span data-stu-id="77288-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="77288-222">A pesquisa é adicionada no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="77288-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77288-223">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="77288-223">Additional resources</span></span>

* [<span data-ttu-id="77288-224">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="77288-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="77288-225">[Anterior: trabalhando com um banco de dados](xref:tutorials/razor-pages/sql) 
>  [Próximo: Adicionar pesquisa](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="77288-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
