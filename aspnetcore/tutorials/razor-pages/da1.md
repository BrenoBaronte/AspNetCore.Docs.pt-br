---
title: Parte 5, atualizar as páginas geradas
author: rick-anderson
description: Parte 5 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 09/20/2020
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 460950413d1dd2d3539c1d62b0eb11f6bb5144a9
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419961"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="66fef-103">Parte 5, atualizar as páginas geradas em um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66fef-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="66fef-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="66fef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="66fef-105">O aplicativo de filme gerado por scaffolding tem um bom começo, mas a apresentação não é ideal.</span><span class="sxs-lookup"><span data-stu-id="66fef-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="66fef-106">**Liberado** deve ser de duas palavras, **data de lançamento**.</span><span class="sxs-lookup"><span data-stu-id="66fef-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Aplicativo de filme aberto no Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="66fef-108">Atualizar o código gerado</span><span class="sxs-lookup"><span data-stu-id="66fef-108">Update the generated code</span></span>

<span data-ttu-id="66fef-109">Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="66fef-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="66fef-110">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="66fef-110">In the previous code:</span></span>

* <span data-ttu-id="66fef-111">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` permite que o Entity Framework Core mapeie o `Price` corretamente para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="66fef-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="66fef-112">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="66fef-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="66fef-113">O atributo [[DISPLAY]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) especifica o nome de exibição de um campo.</span><span class="sxs-lookup"><span data-stu-id="66fef-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="66fef-114">No código anterior, "data de lançamento" em vez de "liberado".</span><span class="sxs-lookup"><span data-stu-id="66fef-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="66fef-115">O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="66fef-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="66fef-116">As informações de hora armazenadas no campo não são exibidas.</span><span class="sxs-lookup"><span data-stu-id="66fef-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="66fef-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) é abordado no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="66fef-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="66fef-118">Navegue até *páginas/filmes* e passe o mouse sobre um link de **edição** para ver a URL de destino.</span><span class="sxs-lookup"><span data-stu-id="66fef-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="66fef-120">Os links **Editar**, **detalhes** e **excluir** são gerados pelo auxiliar de [marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) no arquivo *pages/Movies/ Index . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="66fef-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="66fef-121">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="66fef-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="66fef-122">No código anterior, o [auxiliar de marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) gera dinamicamente o `href` valor do atributo HTML da Razor página (a rota é relativa), o `asp-page` e o identificador de rota ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="66fef-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="66fef-123">Para obter mais informações, consulte [geração de URL para páginas](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="66fef-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="66fef-124">Use o **modo de exibição de origem** de um navegador para examinar a marcação gerada.</span><span class="sxs-lookup"><span data-stu-id="66fef-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="66fef-125">Uma parte do HTML gerado é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="66fef-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="66fef-126">Os links gerados dinamicamente passam a ID do filme com uma cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="66fef-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="66fef-127">Por exemplo, o `?id=1` no `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="66fef-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="66fef-128">Adicionar modelo de rota</span><span class="sxs-lookup"><span data-stu-id="66fef-128">Add route template</span></span>

<span data-ttu-id="66fef-129">Atualize as páginas editar, detalhes e excluir Razor para usar o `{id:int}` modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="66fef-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="66fef-130">Altere a diretiva de página de cada uma dessas páginas de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="66fef-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="66fef-131">Execute o aplicativo e, em seguida, exiba o código-fonte.</span><span class="sxs-lookup"><span data-stu-id="66fef-131">Run the app and then view source.</span></span>

<span data-ttu-id="66fef-132">O HTML gerado adiciona a ID à parte do caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="66fef-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="66fef-133">Uma solicitação para a página com o `{id:int}` modelo de rota que **não** inclui o inteiro retornará um erro http 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="66fef-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="66fef-134">Por exemplo, `https://localhost:5001/Movies/Details` retornará um erro 404.</span><span class="sxs-lookup"><span data-stu-id="66fef-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="66fef-135">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="66fef-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="66fef-136">Testar o comportamento de `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="66fef-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="66fef-137">defina a diretiva de página em *Pages/Movies/Details.cshtml* como `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="66fef-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="66fef-138">Defina um ponto de interrupção no `public async Task<IActionResult> OnGetAsync(int? id)` , em *pages/Movies/details. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="66fef-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="66fef-139">Navegue até `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="66fef-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="66fef-140">Com a diretiva `@page "{id:int}"`, o ponto de interrupção nunca é atingido.</span><span class="sxs-lookup"><span data-stu-id="66fef-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="66fef-141">O mecanismo de roteamento retorna HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="66fef-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="66fef-142">Usando `@page "{id:int?}"` , o `OnGetAsync` método retorna `NotFound` (http 404):</span><span class="sxs-lookup"><span data-stu-id="66fef-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="66fef-143">Examinar o tratamento de exceção de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="66fef-143">Review concurrency exception handling</span></span>

<span data-ttu-id="66fef-144">Examine o método `OnPostAsync` no arquivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="66fef-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="66fef-145">O código anterior detecta exceções de simultaneidade quando um cliente exclui o filme e o outro cliente posta alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="66fef-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="66fef-146">Para testar o bloco `catch`:</span><span class="sxs-lookup"><span data-stu-id="66fef-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="66fef-147">Defina um ponto de interrupção em `catch (DbUpdateConcurrencyException)` .</span><span class="sxs-lookup"><span data-stu-id="66fef-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="66fef-148">Selecione **Editar** para um filme, faça alterações, mas não insira **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="66fef-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="66fef-149">Em outra janela do navegador, selecione o link **Excluir** do mesmo filme e, em seguida, exclua o filme.</span><span class="sxs-lookup"><span data-stu-id="66fef-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="66fef-150">Na janela do navegador anterior, poste as alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="66fef-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="66fef-151">O código de produção talvez deseje detectar conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="66fef-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="66fef-152">Confira [Lidar com conflitos de simultaneidade](xref:data/ef-rp/concurrency) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="66fef-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="66fef-153">Análise de postagem e associação</span><span class="sxs-lookup"><span data-stu-id="66fef-153">Posting and binding review</span></span>

<span data-ttu-id="66fef-154">Examine o arquivo *Pages/Movies/Edit.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="66fef-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="66fef-155">Quando uma solicitação HTTP GET é feita na página filmes/editar, por exemplo `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="66fef-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="66fef-156">O método `OnGetAsync` busca o filme do banco de dados e retorna o método `Page`.</span><span class="sxs-lookup"><span data-stu-id="66fef-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="66fef-157">O `Page` método renderiza a página *pages/Movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="66fef-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="66fef-158">O arquivo *pages/Movies/Edit. cshtml* contém a diretiva Model `@model RazorPagesMovie.Pages.Movies.EditModel` , que torna o modelo de filme disponível na página.</span><span class="sxs-lookup"><span data-stu-id="66fef-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="66fef-159">O formulário Editar é exibido com os valores do filme.</span><span class="sxs-lookup"><span data-stu-id="66fef-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="66fef-160">Quando a página Movies/Edit é postada:</span><span class="sxs-lookup"><span data-stu-id="66fef-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="66fef-161">Os valores de formulário na página são associados à propriedade `Movie`.</span><span class="sxs-lookup"><span data-stu-id="66fef-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="66fef-162">O atributo `[BindProperty]` habilita o [Model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="66fef-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="66fef-163">Se houver erros no estado do modelo, por exemplo, `ReleaseDate` não puder ser convertido em uma data, o formulário será exibido novamente com os valores enviados.</span><span class="sxs-lookup"><span data-stu-id="66fef-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="66fef-164">Se não houver erros do modelo, o filme será salvo.</span><span class="sxs-lookup"><span data-stu-id="66fef-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="66fef-165">Os métodos GET HTTP nas Index páginas, criar e excluir Razor seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="66fef-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="66fef-166">O método HTTP POST `OnPostAsync` na página Criar Razor segue um padrão semelhante ao `OnPostAsync` método na Razor página Editar.</span><span class="sxs-lookup"><span data-stu-id="66fef-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66fef-167">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="66fef-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="66fef-168">[Anterior: trabalhar com um banco de dados](xref:tutorials/razor-pages/sql) 
>  [Próximo: Adicionar pesquisa](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="66fef-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="66fef-169">O aplicativo de filme gerado por scaffolding tem um bom começo, mas a apresentação não é ideal.</span><span class="sxs-lookup"><span data-stu-id="66fef-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="66fef-170">**Liberado** deve ser de duas palavras, **data de lançamento**.</span><span class="sxs-lookup"><span data-stu-id="66fef-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Aplicativo de filme aberto no Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="66fef-172">Atualizar o código gerado</span><span class="sxs-lookup"><span data-stu-id="66fef-172">Update the generated code</span></span>

<span data-ttu-id="66fef-173">Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="66fef-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="66fef-174">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` permite que o Entity Framework Core mapeie o `Price` corretamente para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="66fef-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="66fef-175">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="66fef-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="66fef-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) é abordado no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="66fef-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="66fef-177">O atributo [[DISPLAY]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) especifica o que exibir para o nome de um campo, neste caso, "data de lançamento" em vez de "liberado".</span><span class="sxs-lookup"><span data-stu-id="66fef-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="66fef-178">O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ), portanto, as informações de hora armazenadas no campo não são exibidas.</span><span class="sxs-lookup"><span data-stu-id="66fef-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="66fef-179">Procure Pages/Movies e focalize um link **Editar** para ver a URL de destino.</span><span class="sxs-lookup"><span data-stu-id="66fef-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="66fef-181">Os links **Editar**, **detalhes** e **excluir** são gerados pelo auxiliar de [marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) no arquivo *pages/Movies/ Index . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="66fef-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="66fef-182">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="66fef-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="66fef-183">No código anterior, o `AnchorTagHelper` gera dinamicamente o valor do `href` atributo HTML da Razor página (a rota é relativa), a `asp-page` e a ID da rota ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="66fef-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="66fef-184">Consulte [Geração de URL para Páginas](xref:razor-pages/index#url-generation-for-pages) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="66fef-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="66fef-185">Use o **modo de exibição de origem** de um navegador para examinar a marcação gerada.</span><span class="sxs-lookup"><span data-stu-id="66fef-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="66fef-186">Uma parte do HTML gerado é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="66fef-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="66fef-187">Os links gerados dinamicamente passam a ID do filme com uma cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="66fef-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="66fef-188">Por exemplo, o `?id=1` no  `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="66fef-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="66fef-189">Atualize as páginas editar, detalhes e excluir Razor para usar o modelo de rota "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="66fef-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="66fef-190">Altere a diretiva de página de cada uma dessas páginas de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="66fef-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="66fef-191">Execute o aplicativo e, em seguida, exiba o código-fonte.</span><span class="sxs-lookup"><span data-stu-id="66fef-191">Run the app and then view source.</span></span> <span data-ttu-id="66fef-192">O HTML gerado adiciona a ID à parte do caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="66fef-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="66fef-193">Uma solicitação para a página com o modelo de rota “{id:int}” que **não** inclui o inteiro retornará um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="66fef-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="66fef-194">Por exemplo, `https://localhost:5001/Movies/Details` retornará um erro 404.</span><span class="sxs-lookup"><span data-stu-id="66fef-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="66fef-195">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="66fef-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="66fef-196">Para testar o comportamento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="66fef-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="66fef-197">defina a diretiva de página em *Pages/Movies/Details.cshtml* como `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="66fef-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="66fef-198">Defina um ponto de interrupção no `public async Task<IActionResult> OnGetAsync(int? id)` , em *pages/Movies/details. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="66fef-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="66fef-199">Navegue até `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="66fef-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="66fef-200">Com a diretiva `@page "{id:int}"`, o ponto de interrupção nunca é atingido.</span><span class="sxs-lookup"><span data-stu-id="66fef-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="66fef-201">O mecanismo de roteamento retorna HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="66fef-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="66fef-202">Usando `@page "{id:int?}"` , o `OnGetAsync` método retorna `NotFound` (http 404):</span><span class="sxs-lookup"><span data-stu-id="66fef-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="66fef-203">Examinar o tratamento de exceção de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="66fef-203">Review concurrency exception handling</span></span>

<span data-ttu-id="66fef-204">Examine o método `OnPostAsync` no arquivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="66fef-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="66fef-205">O código anterior detecta exceções de simultaneidade quando um cliente exclui o filme e o outro cliente posta alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="66fef-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="66fef-206">Para testar o bloco `catch`:</span><span class="sxs-lookup"><span data-stu-id="66fef-206">To test the `catch` block:</span></span>

* <span data-ttu-id="66fef-207">Definir um ponto de interrupção em `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="66fef-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="66fef-208">Selecione **Editar** para um filme, faça alterações, mas não insira **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="66fef-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="66fef-209">Em outra janela do navegador, selecione o link **Excluir** do mesmo filme e, em seguida, exclua o filme.</span><span class="sxs-lookup"><span data-stu-id="66fef-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="66fef-210">Na janela do navegador anterior, poste as alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="66fef-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="66fef-211">O código de produção talvez deseje detectar conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="66fef-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="66fef-212">Confira [Lidar com conflitos de simultaneidade](xref:data/ef-rp/concurrency) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="66fef-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="66fef-213">Análise de postagem e associação</span><span class="sxs-lookup"><span data-stu-id="66fef-213">Posting and binding review</span></span>

<span data-ttu-id="66fef-214">Examine o arquivo *Pages/Movies/Edit.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="66fef-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="66fef-215">Quando uma solicitação HTTP GET é feita na página filmes/editar, por exemplo `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="66fef-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="66fef-216">O método `OnGetAsync` busca o filme do banco de dados e retorna o método `Page`.</span><span class="sxs-lookup"><span data-stu-id="66fef-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="66fef-217">O `Page` método renderiza a página *pages/Movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="66fef-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="66fef-218">O arquivo *pages/Movies/Edit. cshtml* contém a diretiva Model `@model RazorPagesMovie.Pages.Movies.EditModel` , que torna o modelo de filme disponível na página.</span><span class="sxs-lookup"><span data-stu-id="66fef-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="66fef-219">O formulário Editar é exibido com os valores do filme.</span><span class="sxs-lookup"><span data-stu-id="66fef-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="66fef-220">Quando a página Movies/Edit é postada:</span><span class="sxs-lookup"><span data-stu-id="66fef-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="66fef-221">Os valores de formulário na página são associados à propriedade `Movie`.</span><span class="sxs-lookup"><span data-stu-id="66fef-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="66fef-222">O atributo `[BindProperty]` habilita o [Model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="66fef-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="66fef-223">Se houver erros no estado do modelo, por exemplo, `ReleaseDate` não puder ser convertido em uma data, o formulário será exibido com os valores enviados.</span><span class="sxs-lookup"><span data-stu-id="66fef-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="66fef-224">Se não houver erros do modelo, o filme será salvo.</span><span class="sxs-lookup"><span data-stu-id="66fef-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="66fef-225">Os métodos GET HTTP nas Index páginas, criar e excluir Razor seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="66fef-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="66fef-226">O método HTTP POST `OnPostAsync` na página Criar Razor segue um padrão semelhante ao `OnPostAsync` método na Razor página Editar.</span><span class="sxs-lookup"><span data-stu-id="66fef-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="66fef-227">A pesquisa é adicionada no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="66fef-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66fef-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="66fef-228">Additional resources</span></span>

* [<span data-ttu-id="66fef-229">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="66fef-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="66fef-230">[Anterior: trabalhar com um banco de dados](xref:tutorials/razor-pages/sql) 
>  [Próximo: Adicionar pesquisa](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="66fef-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
