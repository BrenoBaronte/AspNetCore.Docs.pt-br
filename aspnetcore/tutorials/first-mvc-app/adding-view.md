---
title: Parte 3, adicionar uma exibição a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Parte 3 da série de tutoriais no ASP.NET Core MVC.
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804535"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="41353-103">Parte 3, adicionar uma exibição a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="41353-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="41353-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41353-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41353-105">Nesta seção, você modificará a `HelloWorldController` classe para usar os [Razor](xref:mvc/views/razor) arquivos de exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="41353-106">Isso encapsula corretamente o processo de geração de respostas HTML para um cliente.</span><span class="sxs-lookup"><span data-stu-id="41353-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="41353-107">Os modelos de exibição são criados usando o Razor .</span><span class="sxs-lookup"><span data-stu-id="41353-107">View templates are created using Razor.</span></span> <span data-ttu-id="41353-108">Razormodelos de exibição com base em:</span><span class="sxs-lookup"><span data-stu-id="41353-108">Razor-based view templates:</span></span>

* <span data-ttu-id="41353-109">Ter uma extensão de arquivo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="41353-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="41353-110">Forneça uma maneira elegante de criar saída HTML com C#.</span><span class="sxs-lookup"><span data-stu-id="41353-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="41353-111">Atualmente, o `Index` método retorna uma cadeia de caracteres com uma mensagem na classe Controller.</span><span class="sxs-lookup"><span data-stu-id="41353-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="41353-112">Na classe `HelloWorldController`, substitua o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="41353-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="41353-113">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="41353-113">The preceding code:</span></span>

* <span data-ttu-id="41353-114">Chama o método do controlador <xref:Microsoft.AspNetCore.Mvc.Controller.View*> .</span><span class="sxs-lookup"><span data-stu-id="41353-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="41353-115">Usa um modelo de exibição para gerar uma resposta HTML.</span><span class="sxs-lookup"><span data-stu-id="41353-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="41353-116">Métodos do controlador:</span><span class="sxs-lookup"><span data-stu-id="41353-116">Controller methods:</span></span>

* <span data-ttu-id="41353-117">São chamados de *métodos de ação*.</span><span class="sxs-lookup"><span data-stu-id="41353-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="41353-118">Por exemplo, o `Index` método de ação no código anterior.</span><span class="sxs-lookup"><span data-stu-id="41353-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="41353-119">Geralmente, retorna um <xref:Microsoft.AspNetCore.Mvc.IActionResult> ou uma classe derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult> , não um tipo como `string` .</span><span class="sxs-lookup"><span data-stu-id="41353-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="41353-120">Adicionar uma exibição</span><span class="sxs-lookup"><span data-stu-id="41353-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41353-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41353-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41353-122">Clique com o botão direito do mouse na pasta *views* e **adicione > nova pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="41353-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="41353-123">Clique com o botão direito do mouse na pasta *views/HelloWorld* e **adicione > novo item**.</span><span class="sxs-lookup"><span data-stu-id="41353-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="41353-124">Na caixa de diálogo **Adicionar novo item-MvcMovie** :</span><span class="sxs-lookup"><span data-stu-id="41353-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="41353-125">Na caixa de pesquisa no canto superior direito, insira *exibição*</span><span class="sxs-lookup"><span data-stu-id="41353-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="41353-126">Selecionar **Razor exibição**</span><span class="sxs-lookup"><span data-stu-id="41353-126">Select **Razor View**</span></span>
* <span data-ttu-id="41353-127">Mantenha o valor da caixa **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="41353-128">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="41353-128">Select **Add**</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="41353-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41353-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="41353-131">Adicione uma `Index` exibição para `HelloWorldController` :</span><span class="sxs-lookup"><span data-stu-id="41353-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="41353-132">Adicione uma nova pasta chamada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="41353-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="41353-133">Adicione um novo arquivo à pasta *Views/HelloWorld* chamada *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41353-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="41353-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="41353-135">Controle-clique na pasta *exibições* e, em seguida, **adicione > nova pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="41353-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="41353-136">Clique no botão de controle na pasta *views/HelloWorld* e **adicione > novo arquivo**.</span><span class="sxs-lookup"><span data-stu-id="41353-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="41353-137">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="41353-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="41353-138">Selecione **ASP.NET Core** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="41353-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="41353-139">Selecione **Razor Exibir-vazio** no painel central.</span><span class="sxs-lookup"><span data-stu-id="41353-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="41353-140">Digite *index* na caixa **nome** .</span><span class="sxs-lookup"><span data-stu-id="41353-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="41353-141">Selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="41353-141">Select **New**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="41353-143">Substitua o conteúdo do arquivo de exibição *exibições/HelloWorld/index. cshtml* Razor pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="41353-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="41353-144">Navegue até `https://localhost:{PORT}/HelloWorld` :</span><span class="sxs-lookup"><span data-stu-id="41353-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="41353-145">O `Index` método na `HelloWorldController` instrução executou `return View();` , que especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="41353-146">Um nome de arquivo de modelo de exibição não foi especificado; portanto, o MVC assume o uso do arquivo de exibição padrão.</span><span class="sxs-lookup"><span data-stu-id="41353-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="41353-147">Quando o nome do arquivo de exibição não é especificado, o modo de exibição padrão é retornado.</span><span class="sxs-lookup"><span data-stu-id="41353-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="41353-148">O modo de exibição padrão tem o mesmo nome que o método de ação, `Index` neste exemplo.</span><span class="sxs-lookup"><span data-stu-id="41353-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="41353-149">O modelo de exibição */views/HelloWorld/index.cshtml* é usado.</span><span class="sxs-lookup"><span data-stu-id="41353-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="41353-150">A imagem a seguir mostra a cadeia de caracteres "Olá de nosso modelo de exibição!"</span><span class="sxs-lookup"><span data-stu-id="41353-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="41353-151">embutido em código na exibição:</span><span class="sxs-lookup"><span data-stu-id="41353-151">hard-coded in the view:</span></span>

  ![Janela do navegador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="41353-153">Alterar exibições e páginas de layout</span><span class="sxs-lookup"><span data-stu-id="41353-153">Change views and layout pages</span></span>

<span data-ttu-id="41353-154">Selecione os links de menu **MvcMovie**, **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="41353-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="41353-155">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="41353-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="41353-156">O layout de menu é implementado no arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="41353-157">Abra o arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="41353-158">Os modelos de [layout](xref:mvc/views/layout) permitem:</span><span class="sxs-lookup"><span data-stu-id="41353-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="41353-159">Especificando o layout de contêiner HTML de um site em um único lugar.</span><span class="sxs-lookup"><span data-stu-id="41353-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="41353-160">Aplicar o layout de contêiner HTML em várias páginas no site.</span><span class="sxs-lookup"><span data-stu-id="41353-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="41353-161">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="41353-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="41353-162">`RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="41353-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="41353-163">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Views/Home/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="41353-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="41353-164">Alterar o título, o rodapé e o link de menu no arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="41353-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="41353-165">Substitua o conteúdo do arquivo *views/Shared/_Layout. cshtml* pela marcação a seguir.</span><span class="sxs-lookup"><span data-stu-id="41353-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="41353-166">As alterações são realçadas:</span><span class="sxs-lookup"><span data-stu-id="41353-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41353-167">A marcação anterior fez as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="41353-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="41353-168">Três ocorrências de `MvcMovie` para `Movie App` .</span><span class="sxs-lookup"><span data-stu-id="41353-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="41353-169">O elemento de âncora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` para `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="41353-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="41353-170">Na marcação anterior, o `asp-area=""` [atributo auxiliar de marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) e o valor do atributo foram omitidos porque esse aplicativo não está usando [áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="41353-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="41353-171">**Observação**: o `Movies` controlador não foi implementado.</span><span class="sxs-lookup"><span data-stu-id="41353-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="41353-172">Neste ponto, o `Movie App` link não está funcionando.</span><span class="sxs-lookup"><span data-stu-id="41353-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="41353-173">Salve as alterações e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="41353-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="41353-174">Observe como o título na guia do navegador agora exibe **Política de Privacidade – Aplicativo de filme**, em vez de **Política de Privacidade – Filme MVC**:</span><span class="sxs-lookup"><span data-stu-id="41353-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Guia Privacidade](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="41353-176">Clique no link **Página Inicial**.</span><span class="sxs-lookup"><span data-stu-id="41353-176">Select the **Home** link.</span></span>

<span data-ttu-id="41353-177">Observe que o título e o texto de ancoragem exibem o **aplicativo de filme**.</span><span class="sxs-lookup"><span data-stu-id="41353-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="41353-178">As alterações foram feitas uma vez no modelo de layout e todas as páginas no site refletem o novo texto de link e o novo título.</span><span class="sxs-lookup"><span data-stu-id="41353-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="41353-179">Examine o arquivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="41353-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="41353-180">O arquivo *Views/_ViewStart.cshtml* mostra o arquivo *Views/Shared/_Layout.cshtml* em cada exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="41353-181">A propriedade `Layout` pode ser usada para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.</span><span class="sxs-lookup"><span data-stu-id="41353-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="41353-182">Abra o arquivo de exibição *exibições/HelloWorld/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="41353-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="41353-183">Altere o título e o `<h2>` elemento como realçado no seguinte:</span><span class="sxs-lookup"><span data-stu-id="41353-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="41353-184">O título e o `<h2>` elemento são ligeiramente diferentes, portanto, fica claro qual parte do código altera a exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="41353-185">`ViewData["Title"] = "Movie List";` no código acima define a propriedade `Title` do dicionário `ViewData` como “Lista de Filmes”.</span><span class="sxs-lookup"><span data-stu-id="41353-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="41353-186">A propriedade `Title` é usada no elemento HTML `<title>` na página de layout:</span><span class="sxs-lookup"><span data-stu-id="41353-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="41353-187">Salve as alterações e navegue para `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="41353-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="41353-188">Observe que os seguintes foram alterados:</span><span class="sxs-lookup"><span data-stu-id="41353-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="41353-189">Título do navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-189">Browser title.</span></span>
* <span data-ttu-id="41353-190">Título principal.</span><span class="sxs-lookup"><span data-stu-id="41353-190">Primary heading.</span></span>
* <span data-ttu-id="41353-191">Títulos secundários.</span><span class="sxs-lookup"><span data-stu-id="41353-191">Secondary headings.</span></span>

<span data-ttu-id="41353-192">Se não houver nenhuma alteração no navegador, ele poderá ser o conteúdo em cache que está sendo exibido.</span><span class="sxs-lookup"><span data-stu-id="41353-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="41353-193">Pressione CTRL + F5 no navegador para forçar a resposta do servidor a ser carregado.</span><span class="sxs-lookup"><span data-stu-id="41353-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="41353-194">O título do navegador é criado com `ViewData["Title"]` que definimos no modelo de exibição *Index.cshtml* e o “– Aplicativo de Filme” adicional adicionado no arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="41353-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="41353-195">O conteúdo do modelo de exibição *Index.cshtml* é mesclado com o modelo de exibição *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="41353-196">Uma única resposta HTML é enviada ao navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="41353-197">Os modelos de layout facilitam a realização de alterações que se aplicam a todas as páginas de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="41353-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="41353-198">Para saber mais, consulte [layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="41353-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Exibição de Lista de Filmes](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="41353-200">O pequeno bit de "dados", o "Olá de nosso modelo de exibição!"</span><span class="sxs-lookup"><span data-stu-id="41353-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="41353-201">mensagem, é embutida no código.</span><span class="sxs-lookup"><span data-stu-id="41353-201">message, is hard-coded however.</span></span> <span data-ttu-id="41353-202">O aplicativo MVC tem um "V" (exibição), um "C" (controlador), mas nenhum "M" (modelo) ainda.</span><span class="sxs-lookup"><span data-stu-id="41353-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="41353-203">Passando dados do controlador para a exibição</span><span class="sxs-lookup"><span data-stu-id="41353-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="41353-204">As ações do controlador são invocadas em resposta a uma solicitação de URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="41353-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="41353-205">Uma classe de controlador é o local em que o código é escrito e que manipula as solicitações recebidas do navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="41353-206">O controlador recupera dados de uma fonte de dados e decide qual tipo de resposta será enviada novamente para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="41353-207">Modelos de exibição podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="41353-208">Os controladores são responsáveis por fornecer os dados necessários para que um modelo de exibição renderize uma resposta.</span><span class="sxs-lookup"><span data-stu-id="41353-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="41353-209">Os modelos de exibição **não** devem:</span><span class="sxs-lookup"><span data-stu-id="41353-209">View templates should **not**:</span></span>

* <span data-ttu-id="41353-210">Fazer lógica de negócios</span><span class="sxs-lookup"><span data-stu-id="41353-210">Do business logic</span></span>
* <span data-ttu-id="41353-211">Interaja com um banco de dados diretamente.</span><span class="sxs-lookup"><span data-stu-id="41353-211">Interact with a database directly.</span></span>

<span data-ttu-id="41353-212">Um modelo de exibição deve funcionar apenas com os dados fornecidos a ele pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="41353-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="41353-213">Manter essa "separação de preocupações" ajuda a manter o código:</span><span class="sxs-lookup"><span data-stu-id="41353-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="41353-214">Limpar.</span><span class="sxs-lookup"><span data-stu-id="41353-214">Clean.</span></span>
* <span data-ttu-id="41353-215">Testável.</span><span class="sxs-lookup"><span data-stu-id="41353-215">Testable.</span></span>
* <span data-ttu-id="41353-216">Passível.</span><span class="sxs-lookup"><span data-stu-id="41353-216">Maintainable.</span></span>

<span data-ttu-id="41353-217">Atualmente, o método `Welcome` na classe `HelloWorldController` usa um parâmetro `name` e um `ID` e, em seguida, gera os valores diretamente no navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="41353-218">Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, altere o controlador para que ele use um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="41353-219">O modelo de exibição gera uma resposta dinâmica, o que significa que os dados apropriados devem ser passados do controlador para a exibição para gerar a resposta.</span><span class="sxs-lookup"><span data-stu-id="41353-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="41353-220">Faça isso fazendo com que o controlador Coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um `ViewData` dicionário.</span><span class="sxs-lookup"><span data-stu-id="41353-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="41353-221">O modelo de exibição pode então acessar os dados dinâmicos.</span><span class="sxs-lookup"><span data-stu-id="41353-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="41353-222">Em *HelloWorldController.cs*, altere o método `Welcome` para adicionar um valor `Message` e `NumTimes` ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="41353-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="41353-223">O `ViewData` dicionário é um objeto dinâmico, o que significa que qualquer tipo pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="41353-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="41353-224">O `ViewData` objeto não tem propriedades definidas até que algo seja adicionado.</span><span class="sxs-lookup"><span data-stu-id="41353-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="41353-225">O [sistema de associação de modelo MVC](xref:mvc/models/model-binding) mapeia automaticamente os parâmetros nomeados `name` e `numTimes` da cadeia de caracteres de consulta para parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="41353-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="41353-226">A conclusão `HelloWorldController` :</span><span class="sxs-lookup"><span data-stu-id="41353-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="41353-227">O objeto de dicionário `ViewData` contém dados que serão passados para a exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="41353-228">Crie um modelo de exibição Boas-vindas chamado *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="41353-229">Você criará um loop no modelo de exibição *Welcome.cshtml* que exibe “Olá” `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="41353-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="41353-230">Substitua o conteúdo de *Views/HelloWorld/Welcome.cshtml* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="41353-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="41353-231">Salve as alterações e navegue para a seguinte URL:</span><span class="sxs-lookup"><span data-stu-id="41353-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="41353-232">Os dados são extraídos da URL e passados para o controlador usando o [associador de modelo MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="41353-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="41353-233">O controlador empacota os dados em um dicionário `ViewData` e passa esse objeto para a exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="41353-234">Em seguida, a exibição renderiza os dados como HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-234">The view then renders the data as HTML to the browser.</span></span>

![Exibição de privacidade que mostra um rótulo Boas-vindas e a frase Olá, Ricardo mostrada quatro vezes](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="41353-236">No exemplo anterior, o `ViewData` dicionário foi usado para passar dados do controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="41353-237">Mais adiante no tutorial, um modelo de exibição será usado para passar dados de um controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="41353-238">A abordagem do modelo de exibição para passar dados é preferida sobre a `ViewData` abordagem do dicionário.</span><span class="sxs-lookup"><span data-stu-id="41353-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="41353-239">No próximo tutorial, será criado um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="41353-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="41353-240">[Anterior: adicionar um controlador](adding-controller.md) 
>  [Em seguida: adicionar um modelo](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="41353-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="41353-241">Nesta seção, a `HelloWorldController` classe é modificada para usar [Razor](xref:mvc/views/razor) os arquivos de exibição para encapsular corretamente o processo de geração de respostas HTML para um cliente.</span><span class="sxs-lookup"><span data-stu-id="41353-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="41353-242">Você cria um arquivo de modelo de exibição usando Razor .</span><span class="sxs-lookup"><span data-stu-id="41353-242">You create a view template file using Razor.</span></span> <span data-ttu-id="41353-243">Razoros modelos de exibição baseados em são uma extensão de arquivo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="41353-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="41353-244">Eles fornecem uma maneira elegante de criar a saída HTML com o C#.</span><span class="sxs-lookup"><span data-stu-id="41353-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="41353-245">Atualmente, o método `Index` retorna uma cadeia de caracteres com uma mensagem que é embutida em código na classe do controlador.</span><span class="sxs-lookup"><span data-stu-id="41353-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="41353-246">Na classe `HelloWorldController`, substitua o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="41353-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="41353-247">O código anterior chama o método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> do controlador.</span><span class="sxs-lookup"><span data-stu-id="41353-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="41353-248">Ele usa um modelo de exibição para gerar uma resposta HTML.</span><span class="sxs-lookup"><span data-stu-id="41353-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="41353-249">Métodos do controlador (também conhecidos como *métodos de ação*), como o método `Index` acima, geralmente retornam um <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou uma classe derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), não um tipo como `string`.</span><span class="sxs-lookup"><span data-stu-id="41353-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="41353-250">Adicionar uma exibição</span><span class="sxs-lookup"><span data-stu-id="41353-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41353-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41353-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="41353-252">Clique com o botão direito do mouse na pasta *views* e **adicione > nova pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="41353-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="41353-253">Clique com o botão direito do mouse na pasta *views/HelloWorld* e **adicione > novo item**.</span><span class="sxs-lookup"><span data-stu-id="41353-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="41353-254">Na caixa de diálogo **Adicionar Novo Item – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="41353-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="41353-255">Na caixa de pesquisa no canto superior direito, insira *exibição*</span><span class="sxs-lookup"><span data-stu-id="41353-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="41353-256">Selecionar **Razor exibição**</span><span class="sxs-lookup"><span data-stu-id="41353-256">Select **Razor View**</span></span>

  * <span data-ttu-id="41353-257">Mantenha o valor da caixa **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="41353-258">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="41353-258">Select **Add**</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="41353-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41353-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="41353-261">Adicione uma exibição `Index` ao `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="41353-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="41353-262">Adicione uma nova pasta chamada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="41353-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="41353-263">Adicione um novo arquivo à pasta *Views/HelloWorld* chamada *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41353-264">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="41353-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="41353-265">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="41353-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="41353-266">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="41353-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="41353-267">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="41353-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="41353-268">Selecione **Web** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="41353-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="41353-269">Selecione **Arquivo HTML vazio** no painel central.</span><span class="sxs-lookup"><span data-stu-id="41353-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="41353-270">Digite *Index.cshtml* na caixa **Nome**.</span><span class="sxs-lookup"><span data-stu-id="41353-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="41353-271">Selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="41353-271">Select **New**.</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="41353-273">Substitua o conteúdo do arquivo de exibição *exibições/HelloWorld/index. cshtml* Razor pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="41353-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="41353-274">Navegue até `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="41353-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="41353-275">O método `Index` no `HelloWorldController` não fez muita coisa: ele executou a instrução `return View();`, que especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="41353-276">Como um nome de arquivo de modo de exibição não foi especificado, o MVC é padronizado para usar o arquivo de exibição padrão.</span><span class="sxs-lookup"><span data-stu-id="41353-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="41353-277">O arquivo de exibição padrão tem o mesmo nome que o método (`Index`), portanto, é usado */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="41353-278">A imagem abaixo mostra a cadeia de caracteres “Olá de nosso modelo de exibição!”</span><span class="sxs-lookup"><span data-stu-id="41353-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="41353-279">embutida em código na exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-279">hard-coded in the view.</span></span>

![Janela do navegador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="41353-281">Alterar exibições e páginas de layout</span><span class="sxs-lookup"><span data-stu-id="41353-281">Change views and layout pages</span></span>

<span data-ttu-id="41353-282">Selecione os links de menu (**MvcMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="41353-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="41353-283">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="41353-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="41353-284">O layout de menu é implementado no arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="41353-285">Abra o arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="41353-286">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site.</span><span class="sxs-lookup"><span data-stu-id="41353-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="41353-287">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="41353-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="41353-288">`RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="41353-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="41353-289">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Views/Home/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="41353-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="41353-290">Alterar o título, o rodapé e o link de menu no arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="41353-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="41353-291">Nos elementos de título e de rodapé, altere `MvcMovie` para `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="41353-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="41353-292">Altere o elemento de âncora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` para `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="41353-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="41353-293">A seguinte marcação mostra as alterações realçadas:</span><span class="sxs-lookup"><span data-stu-id="41353-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="41353-294">Na marcação anterior, o `asp-area` [atributo do Auxiliar de Marca de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) foi omitido porque este aplicativo não está usando [Áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="41353-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="41353-295">**Observação**: o `Movies` controlador não foi implementado.</span><span class="sxs-lookup"><span data-stu-id="41353-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="41353-296">Neste ponto, o link `Movie App` não está funcionando.</span><span class="sxs-lookup"><span data-stu-id="41353-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="41353-297">Salve suas alterações e selecione o link **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="41353-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="41353-298">Observe como o título na guia do navegador agora exibe **Política de Privacidade – Aplicativo de filme**, em vez de **Política de Privacidade – Filme MVC**:</span><span class="sxs-lookup"><span data-stu-id="41353-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Guia Privacidade](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="41353-300">Selecione o link **Página Inicial** e observe que o texto do título e de âncora também exibem **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="41353-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="41353-301">Conseguimos fazer a alteração uma vez no modelo de layout e fazer com que todas as páginas no site refletissem o novo texto do link e o novo título.</span><span class="sxs-lookup"><span data-stu-id="41353-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="41353-302">Examine o arquivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="41353-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="41353-303">O arquivo *Views/_ViewStart.cshtml* mostra o arquivo *Views/Shared/_Layout.cshtml* em cada exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="41353-304">A propriedade `Layout` pode ser usada para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.</span><span class="sxs-lookup"><span data-stu-id="41353-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="41353-305">Altere o título e o elemento `<h2>` do arquivo de exibição *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="41353-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="41353-306">O título e o elemento `<h2>` são ligeiramente diferentes para que possa ver qual parte do código altera a exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="41353-307">`ViewData["Title"] = "Movie List";` no código acima define a propriedade `Title` do dicionário `ViewData` como “Lista de Filmes”.</span><span class="sxs-lookup"><span data-stu-id="41353-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="41353-308">A propriedade `Title` é usada no elemento HTML `<title>` na página de layout:</span><span class="sxs-lookup"><span data-stu-id="41353-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="41353-309">Salve as alterações e navegue para `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="41353-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="41353-310">Observe que o título do navegador, o cabeçalho primário e os títulos secundários foram alterados.</span><span class="sxs-lookup"><span data-stu-id="41353-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="41353-311">(Se as alterações não forem exibidas no navegador, talvez o conteúdo armazenado em cache esteja sendo exibido.</span><span class="sxs-lookup"><span data-stu-id="41353-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="41353-312">Pressione CTRL + F5 em seu navegador para forçar a resposta do servidor a ser carregado.) O título do navegador é criado com o `ViewData["Title"]` que definimos no modelo de exibição *index. cshtml* e o "-Movie app" adicional adicionado no arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="41353-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="41353-313">Observe também como o conteúdo no modelo de exibição *Index.cshtml* foi mesclado com o modelo de exibição *Views/Shared/_Layout.cshtml* e uma única resposta HTML foi enviada para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="41353-314">Os modelos de layout facilitam realmente a realização de alterações que se aplicam a todas as páginas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="41353-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="41353-315">Para saber mais, consulte [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="41353-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Exibição de Lista de Filmes](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="41353-317">Apesar disso, nossos poucos “dados” (nesse caso, a mensagem “Olá de nosso modelo de exibição!”)</span><span class="sxs-lookup"><span data-stu-id="41353-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="41353-318">são embutidos em código.</span><span class="sxs-lookup"><span data-stu-id="41353-318">message) is hard-coded, though.</span></span> <span data-ttu-id="41353-319">O aplicativo MVC tem um “V” (exibição) e você tem um “C” (controlador), mas ainda nenhum “M” (modelo).</span><span class="sxs-lookup"><span data-stu-id="41353-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="41353-320">Passando dados do controlador para a exibição</span><span class="sxs-lookup"><span data-stu-id="41353-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="41353-321">As ações do controlador são invocadas em resposta a uma solicitação de URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="41353-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="41353-322">Uma classe de controlador é o local em que o código é escrito e que manipula as solicitações recebidas do navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="41353-323">O controlador recupera dados de uma fonte de dados e decide qual tipo de resposta será enviada novamente para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="41353-324">Modelos de exibição podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="41353-325">Os controladores são responsáveis por fornecer os dados necessários para que um modelo de exibição renderize uma resposta.</span><span class="sxs-lookup"><span data-stu-id="41353-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="41353-326">Uma melhor prática: modelos de exibição **não** devem executar a lógica de negócios nem interagir diretamente com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="41353-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="41353-327">Em vez disso, um modelo de exibição deve funcionar somente com os dados fornecidos pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="41353-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="41353-328">Manter essa “separação de preocupações” ajuda a manter o código limpo, testável e com capacidade de manutenção.</span><span class="sxs-lookup"><span data-stu-id="41353-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="41353-329">Atualmente, o método `Welcome` na classe `HelloWorldController` usa um parâmetro `name` e um `ID` e, em seguida, gera os valores diretamente no navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="41353-330">Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, altere o controlador para que ele use um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="41353-331">O modelo de exibição gera uma resposta dinâmica, o que significa que é necessário passar bits de dados apropriados do controlador para a exibição para gerar a resposta.</span><span class="sxs-lookup"><span data-stu-id="41353-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="41353-332">Faça isso fazendo com que o controlador coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um dicionário `ViewData` que pode ser acessado em seguida pelo modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="41353-333">Em *HelloWorldController.cs*, altere o método `Welcome` para adicionar um valor `Message` e `NumTimes` ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="41353-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="41353-334">O dicionário `ViewData` é um objeto dinâmico, o que significa que qualquer tipo pode ser usado. O objeto `ViewData` não tem nenhuma propriedade definida até que você insira algo nele.</span><span class="sxs-lookup"><span data-stu-id="41353-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="41353-335">O sistema de [model binding](xref:mvc/models/model-binding) MVC mapeia automaticamente os parâmetros nomeados (`name` e `numTimes`) da cadeia de consulta na barra de endereços para os parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="41353-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="41353-336">O arquivo *HelloWorldController.cs* completo tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="41353-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="41353-337">O objeto de dicionário `ViewData` contém dados que serão passados para a exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="41353-338">Crie um modelo de exibição Boas-vindas chamado *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41353-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="41353-339">Você criará um loop no modelo de exibição *Welcome.cshtml* que exibe “Olá” `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="41353-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="41353-340">Substitua o conteúdo de *Views/HelloWorld/Welcome.cshtml* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="41353-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="41353-341">Salve as alterações e navegue para a seguinte URL:</span><span class="sxs-lookup"><span data-stu-id="41353-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="41353-342">Os dados são obtidos da URL e passados para o controlador usando o [associador de modelo MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="41353-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="41353-343">O controlador empacota os dados em um dicionário `ViewData` e passa esse objeto para a exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="41353-344">Em seguida, a exibição renderiza os dados como HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="41353-344">The view then renders the data as HTML to the browser.</span></span>

![Exibição de privacidade que mostra um rótulo Boas-vindas e a frase Olá, Ricardo mostrada quatro vezes](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="41353-346">No exemplo acima, o dicionário `ViewData` foi usado para passar dados do controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="41353-347">Mais adiante no tutorial, um modelo de exibição será usado para passar dados de um controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="41353-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="41353-348">A abordagem de modelo de exibição para passar dados é geralmente a preferida em relação à abordagem do dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="41353-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="41353-349">Confira mais informações em [Quando usar ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="41353-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="41353-350">No próximo tutorial, será criado um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="41353-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="41353-351">[Anterior](adding-controller.md) 
>  [Avançar](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="41353-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
