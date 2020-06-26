---
title: Parte 5, atualizar as páginas geradas em um aplicativo ASP.NET Core
author: rick-anderson
description: Parte 5 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 12/20/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 9ab97ca9a79e055b9013801ccc65bd6707e2b312
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404503"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>Parte 5, atualizar as páginas geradas em um aplicativo ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

O aplicativo de filme gerado por scaffolding tem um bom começo, mas a apresentação não é ideal. **ReleaseDate** deveria ser **Data de Lançamento** (duas palavras).

![Aplicativo de filme aberto no Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Atualizar o código gerado

Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas no seguinte código:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` permite que o Entity Framework Core mapeie o `Price` corretamente para a moeda no banco de dados. Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) é abordado no próximo tutorial. O atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica o que deve ser exibido no nome de um campo (neste caso, “Release Date” em vez de “ReleaseDate”). O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo de dados (Data) e, portanto, as informações de hora armazenadas no campo não são exibidas.

Procure Pages/Movies e focalize um link **Editar** para ver a URL de destino.

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

Os links **Editar**, **Detalhes** e **Excluir** são gerados pelo [Auxiliar de Marcação de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) no arquivo *Pages/Movies/Index.cshtml*.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos. No código anterior, o `AnchorTagHelper` gera dinamicamente o valor do `href` atributo HTML da Razor página (a rota é relativa), a `asp-page` e a ID da rota ( `asp-route-id` ). Consulte [Geração de URL para Páginas](xref:razor-pages/index#url-generation-for-pages) para obter mais informações.

Use **Exibir Código-fonte** em seu navegador favorito para examinar a marcação gerada. Uma parte do HTML gerado é mostrada abaixo:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Os links gerados dinamicamente passam a ID de filme com uma cadeia de consulta (por exemplo, o `?id=1` em `https://localhost:5001/Movies/Details?id=1`).

### <a name="add-route-template"></a>Adicionar modelo de rota

Atualize as páginas editar, detalhes e excluir Razor para usar o modelo de rota "{ID: int}". Altere a diretiva de página de cada uma dessas páginas de `@page` para `@page "{id:int}"`. Execute o aplicativo e, em seguida, exiba o código-fonte. O HTML gerado adiciona a ID à parte do caminho da URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Uma solicitação para a página com o modelo de rota “{id:int}” que **não** inclui o inteiro retornará um erro HTTP 404 (não encontrado). Por exemplo, `http://localhost:5000/Movies/Details` retornará um erro 404. Para tornar a ID opcional, acrescente `?` à restrição de rota:

 ```cshtml
@page "{id:int?}"
```

Para testar o comportamento de `@page "{id:int?}"`:

* defina a diretiva de página em *Pages/Movies/Details.cshtml* como `@page "{id:int?}"`.
* Defina um ponto de interrupção em `public async Task<IActionResult> OnGetAsync(int? id)` (em *Pages/Movies/Details.cshtml.cs*).
* Navegue até `https://localhost:5001/Movies/Details/`.

Com a diretiva `@page "{id:int}"`, o ponto de interrupção nunca é atingido. O mecanismo de roteamento retorna HTTP 404. Usando `@page "{id:int?}"`, o método `OnGetAsync` retorna `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Examinar o tratamento de exceção de simultaneidade

Examine o método `OnPostAsync` no arquivo *Pages/Movies/Edit.cshtml.cs*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

O código anterior detecta exceções de simultaneidade quando um cliente exclui o filme e o outro cliente posta alterações no filme.

Para testar o bloco `catch`:

* Definir um ponto de interrupção em `catch (DbUpdateConcurrencyException)`
* Selecione **Editar** para um filme, faça alterações, mas não insira **Salvar**.
* Em outra janela do navegador, selecione o link **Excluir** do mesmo filme e, em seguida, exclua o filme.
* Na janela do navegador anterior, poste as alterações no filme.

O código de produção talvez deseje detectar conflitos de simultaneidade. Confira [Lidar com conflitos de simultaneidade](xref:data/ef-rp/concurrency) para obter mais informações.

### <a name="posting-and-binding-review"></a>Análise de postagem e associação

Examine o arquivo *Pages/Movies/Edit.cshtml.cs*: 

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Quando uma solicitação HTTP GET é feita para a página Movies/Edit (por exemplo, `http://localhost:5000/Movies/Edit/2`):

* O método `OnGetAsync` busca o filme do banco de dados e retorna o método `Page`.
* O `Page` método renderiza a página *pages/Movies/Edit. cshtml* Razor . O arquivo *Pages/Movies/Edit.cshtml* contém a diretiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que disponibiliza o modelo de filme na página.
* O formulário Editar é exibido com os valores do filme.

Quando a página Movies/Edit é postada:

* Os valores de formulário na página são associados à propriedade `Movie`. O atributo `[BindProperty]` habilita o [Model binding](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Se há erros no estado do modelo (por exemplo, `ReleaseDate` não pode ser convertido em uma data), o formulário é exibido novamente com os valores enviados.
* Se não houver erros do modelo, o filme será salvo.

Os métodos GET HTTP nas páginas index, Create e Delete Razor seguem um padrão semelhante. O método HTTP POST `OnPostAsync` na página Criar Razor segue um padrão semelhante ao `OnPostAsync` método na Razor página Editar.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: trabalhando com um banco de dados](xref:tutorials/razor-pages/sql) 
>  [Próximo: Adicionar pesquisa](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O aplicativo de filme gerado por scaffolding tem um bom começo, mas a apresentação não é ideal. **ReleaseDate** deveria ser **Data de Lançamento** (duas palavras).

![Aplicativo de filme aberto no Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Atualizar o código gerado

Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas no seguinte código:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` permite que o Entity Framework Core mapeie o `Price` corretamente para a moeda no banco de dados. Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) é abordado no próximo tutorial. O atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica o que deve ser exibido no nome de um campo (neste caso, “Release Date” em vez de “ReleaseDate”). O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo de dados (Data) e, portanto, as informações de hora armazenadas no campo não são exibidas.

Procure Pages/Movies e focalize um link **Editar** para ver a URL de destino.

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

Os links **Editar**, **Detalhes** e **Excluir** são gerados pelo [Auxiliar de Marcação de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) no arquivo *Pages/Movies/Index.cshtml*.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos. No código anterior, o `AnchorTagHelper` gera dinamicamente o valor do `href` atributo HTML da Razor página (a rota é relativa), a `asp-page` e a ID da rota ( `asp-route-id` ). Consulte [Geração de URL para Páginas](xref:razor-pages/index#url-generation-for-pages) para obter mais informações.

Use **Exibir Código-fonte** em seu navegador favorito para examinar a marcação gerada. Uma parte do HTML gerado é mostrada abaixo:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Os links gerados dinamicamente passam a ID de filme com uma cadeia de consulta (por exemplo, o `?id=1` em `https://localhost:5001/Movies/Details?id=1`).

Atualize as páginas editar, detalhes e excluir Razor para usar o modelo de rota "{ID: int}". Altere a diretiva de página de cada uma dessas páginas de `@page` para `@page "{id:int}"`. Execute o aplicativo e, em seguida, exiba o código-fonte. O HTML gerado adiciona a ID à parte do caminho da URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Uma solicitação para a página com o modelo de rota “{id:int}” que **não** inclui o inteiro retornará um erro HTTP 404 (não encontrado). Por exemplo, `http://localhost:5000/Movies/Details` retornará um erro 404. Para tornar a ID opcional, acrescente `?` à restrição de rota:

 ```cshtml
@page "{id:int?}"
```

Para testar o comportamento de `@page "{id:int?}"`:

* defina a diretiva de página em *Pages/Movies/Details.cshtml* como `@page "{id:int?}"`.
* Defina um ponto de interrupção em `public async Task<IActionResult> OnGetAsync(int? id)` (em *Pages/Movies/Details.cshtml.cs*).
* Navegue até `https://localhost:5001/Movies/Details/`.

Com a diretiva `@page "{id:int}"`, o ponto de interrupção nunca é atingido. O mecanismo de roteamento retorna HTTP 404. Usando `@page "{id:int?}"`, o método `OnGetAsync` retorna `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Examinar o tratamento de exceção de simultaneidade

Examine o método `OnPostAsync` no arquivo *Pages/Movies/Edit.cshtml.cs*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

O código anterior detecta exceções de simultaneidade quando um cliente exclui o filme e o outro cliente posta alterações no filme.

Para testar o bloco `catch`:

* Definir um ponto de interrupção em `catch (DbUpdateConcurrencyException)`
* Selecione **Editar** para um filme, faça alterações, mas não insira **Salvar**.
* Em outra janela do navegador, selecione o link **Excluir** do mesmo filme e, em seguida, exclua o filme.
* Na janela do navegador anterior, poste as alterações no filme.

O código de produção talvez deseje detectar conflitos de simultaneidade. Confira [Lidar com conflitos de simultaneidade](xref:data/ef-rp/concurrency) para obter mais informações.

### <a name="posting-and-binding-review"></a>Análise de postagem e associação

Examine o arquivo *Pages/Movies/Edit.cshtml.cs*: 

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Quando uma solicitação HTTP GET é feita para a página Movies/Edit (por exemplo, `http://localhost:5000/Movies/Edit/2`):

* O método `OnGetAsync` busca o filme do banco de dados e retorna o método `Page`. 
* O `Page` método renderiza a página *pages/Movies/Edit. cshtml* Razor . O arquivo *Pages/Movies/Edit.cshtml* contém a diretiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que disponibiliza o modelo de filme na página.
* O formulário Editar é exibido com os valores do filme.

Quando a página Movies/Edit é postada:

* Os valores de formulário na página são associados à propriedade `Movie`. O atributo `[BindProperty]` habilita o [Model binding](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Se houver erros no estado do modelo (por exemplo, `ReleaseDate` não pode ser convertido em uma data), o formulário será mostrado com os valores enviados.
* Se não houver erros do modelo, o filme será salvo.

Os métodos GET HTTP nas páginas index, Create e Delete Razor seguem um padrão semelhante. O método HTTP POST `OnPostAsync` na página Criar Razor segue um padrão semelhante ao `OnPostAsync` método na Razor página Editar.

A pesquisa é adicionada no próximo tutorial.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Anterior: trabalhando com um banco de dados](xref:tutorials/razor-pages/sql) 
>  [Próximo: Adicionar pesquisa](xref:tutorials/razor-pages/search)

::: moniker-end
