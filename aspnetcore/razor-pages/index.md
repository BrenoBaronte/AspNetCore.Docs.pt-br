---
title: Introdução às Razor páginas no ASP.NET Core
author: Rick-Anderson
description: Explica como as Razor páginas no ASP.NET Core tornam cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: bc5f3c85d7ace120dec8369f4550a097b4f258ce
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417637"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="bea25-103">Introdução às Razor páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bea25-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="bea25-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="bea25-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="bea25-105">Razor As páginas podem tornar os cenários voltados para a página de codificação mais fáceis e produtivos do que usar controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="bea25-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="bea25-106">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="bea25-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="bea25-107">Este documento fornece uma introdução às Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="bea25-108">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="bea25-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="bea25-109">Se você encontrar algumas das seções muito avançadas, consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="bea25-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="bea25-110">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="bea25-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bea25-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="bea25-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="bea25-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bea25-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bea25-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bea25-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bea25-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bea25-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="bea25-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bea25-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bea25-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bea25-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bea25-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bea25-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="bea25-118">Criar um Razor projeto de páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bea25-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bea25-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bea25-120">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um Razor projeto de páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bea25-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bea25-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bea25-122">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="bea25-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bea25-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bea25-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bea25-124">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um Razor projeto de páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="bea25-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="bea25-125">Razor Pages</span></span>

<span data-ttu-id="bea25-126">Razor As páginas estão habilitadas no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bea25-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="bea25-127">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="bea25-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="bea25-128">O código anterior é muito parecido com um [ Razor arquivo de exibição](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="bea25-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="bea25-129">O que o torna diferente é a [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="bea25-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="bea25-130">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="bea25-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="bea25-131">`@page` deve ser a primeira Razor diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bea25-132">`@page` afeta o comportamento de outras [Razor](xref:mvc/views/razor) construções.</span><span class="sxs-lookup"><span data-stu-id="bea25-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="bea25-133">Razor Os nomes de arquivo de páginas têm um sufixo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bea25-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="bea25-134">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="bea25-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="bea25-135">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="bea25-136">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bea25-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="bea25-137">Por convenção, o `PageModel` arquivo de classe tem o mesmo nome que o Razor arquivo de paginação com *. cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="bea25-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="bea25-138">Por exemplo, a Razor página anterior é *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bea25-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="bea25-139">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="bea25-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="bea25-140">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="bea25-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="bea25-141">A tabela a seguir mostra um Razor caminho de página e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="bea25-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="bea25-142">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="bea25-142">File name and path</span></span>               | <span data-ttu-id="bea25-143">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="bea25-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="bea25-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="bea25-145">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="bea25-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="bea25-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="bea25-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="bea25-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="bea25-149">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="bea25-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="bea25-150">Observações:</span><span class="sxs-lookup"><span data-stu-id="bea25-150">Notes:</span></span>

* <span data-ttu-id="bea25-151">O tempo de execução procura por Razor arquivos de páginas na pasta *páginas* por padrão.</span><span class="sxs-lookup"><span data-stu-id="bea25-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="bea25-152">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="bea25-153">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="bea25-153">Write a basic form</span></span>

<span data-ttu-id="bea25-154">Razor As páginas são projetadas para tornar os padrões comuns usados com navegadores da Web fáceis de implementar ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bea25-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="bea25-155">[Associação de modelo](xref:mvc/models/model-binding), [auxiliares de marca](xref:mvc/views/tag-helpers/intro)e auxiliares HTML *funcionam apenas* com as propriedades definidas em uma Razor classe de página.</span><span class="sxs-lookup"><span data-stu-id="bea25-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="bea25-156">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="bea25-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="bea25-157">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="bea25-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="bea25-158">O banco de dados na memória requer o `Microsoft.EntityFrameworkCore.InMemory` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="bea25-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="bea25-159">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="bea25-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="bea25-160">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="bea25-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="bea25-161">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="bea25-162">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bea25-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="bea25-163">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="bea25-164">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="bea25-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="bea25-165">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="bea25-166">Essa separação permite:</span><span class="sxs-lookup"><span data-stu-id="bea25-166">This separation allows:</span></span>

* <span data-ttu-id="bea25-167">Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bea25-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="bea25-168">Testes de unidade</span><span class="sxs-lookup"><span data-stu-id="bea25-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="bea25-169">A página tem um  *método de manipulador*`OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="bea25-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="bea25-170">Métodos de manipulador para qualquer verbo HTTP podem ser adicionados.</span><span class="sxs-lookup"><span data-stu-id="bea25-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="bea25-171">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="bea25-171">The most common handlers are:</span></span>

* <span data-ttu-id="bea25-172">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="bea25-173">No código anterior, o `OnGet` método exibe a página *CREATEMODEL. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="bea25-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="bea25-174">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="bea25-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="bea25-175">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="bea25-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="bea25-176">O código anterior é típico para Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="bea25-177">Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:</span><span class="sxs-lookup"><span data-stu-id="bea25-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="bea25-178">O `OnPostAsync` código no exemplo anterior é semelhante ao código de controlador típico.</span><span class="sxs-lookup"><span data-stu-id="bea25-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="bea25-179">A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation)e resultados de ação, funciona da mesma forma com controladores e Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="bea25-180">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="bea25-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="bea25-181">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="bea25-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="bea25-182">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="bea25-182">Check for validation errors.</span></span>

* <span data-ttu-id="bea25-183">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="bea25-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="bea25-184">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="bea25-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="bea25-185">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="bea25-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="bea25-186">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="bea25-187">O HTML renderizado de *pages/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="bea25-188">No código anterior, postando o formulário:</span><span class="sxs-lookup"><span data-stu-id="bea25-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="bea25-189">Com dados válidos:</span><span class="sxs-lookup"><span data-stu-id="bea25-189">With valid data:</span></span>

  * <span data-ttu-id="bea25-190">O `OnPostAsync` método de manipulador chama o <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> método auxiliar.</span><span class="sxs-lookup"><span data-stu-id="bea25-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="bea25-191">`RedirectToPage` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="bea25-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="bea25-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="bea25-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="bea25-193">É um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="bea25-193">Is an action result.</span></span>
    * <span data-ttu-id="bea25-194">É semelhante a `RedirectToAction` ou `RedirectToRoute` (usado em controladores e exibições).</span><span class="sxs-lookup"><span data-stu-id="bea25-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="bea25-195">É personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-195">Is customized for pages.</span></span> <span data-ttu-id="bea25-196">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="bea25-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="bea25-197">`RedirectToPage` é detalhado na seção [geração de URL para páginas](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="bea25-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="bea25-198">Com erros de validação que são passados para o servidor:</span><span class="sxs-lookup"><span data-stu-id="bea25-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="bea25-199">O `OnPostAsync` método de manipulador chama o <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> método auxiliar.</span><span class="sxs-lookup"><span data-stu-id="bea25-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="bea25-200">`Page` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="bea25-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="bea25-201">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="bea25-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="bea25-202">`PageResult` é o tipo de retorno padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="bea25-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="bea25-203">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="bea25-204">No exemplo anterior, a postagem do formulário sem nenhum resultado em [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) retorna false.</span><span class="sxs-lookup"><span data-stu-id="bea25-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="bea25-205">Neste exemplo, nenhum erro de validação é exibido no cliente.</span><span class="sxs-lookup"><span data-stu-id="bea25-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="bea25-206">A entrega do erro de validação será abordada posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="bea25-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="bea25-207">Com erros de validação detectados pela validação no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="bea25-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="bea25-208">Os dados **não** são postados no servidor.</span><span class="sxs-lookup"><span data-stu-id="bea25-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="bea25-209">A validação no lado do cliente é explicada posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="bea25-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="bea25-210">A `Customer` propriedade usa o [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atributo para aceitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="bea25-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="bea25-211">`[BindProperty]`**não** deve ser usado em modelos que contêm propriedades que não devem ser alteradas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="bea25-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="bea25-212">Para obter mais informações, consulte [superpostando](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="bea25-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="bea25-213">Razor As páginas, por padrão, associam propriedades somente a não `GET` verbos.</span><span class="sxs-lookup"><span data-stu-id="bea25-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="bea25-214">A associação a Propriedades remove a necessidade de escrever código para converter dados HTTP no tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="bea25-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="bea25-215">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="bea25-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="bea25-216">Examinando o arquivo de exibição *páginas/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bea25-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="bea25-217">No código anterior, o [auxiliar de marca de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` associa o `<input>` elemento HTML à `Customer.Name` expressão do modelo.</span><span class="sxs-lookup"><span data-stu-id="bea25-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="bea25-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) torna os auxiliares de marca disponíveis.</span><span class="sxs-lookup"><span data-stu-id="bea25-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="bea25-219">Home page</span><span class="sxs-lookup"><span data-stu-id="bea25-219">The home page</span></span>

<span data-ttu-id="bea25-220">*Index. cshtml* é o Home Page:</span><span class="sxs-lookup"><span data-stu-id="bea25-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="bea25-221">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="bea25-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="bea25-222">O arquivo *index. cshtml* contém a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="bea25-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="bea25-223">O `<a /a>` [auxiliar de marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) usou o `asp-route-{value}` atributo para gerar um link para a página de edição.</span><span class="sxs-lookup"><span data-stu-id="bea25-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="bea25-224">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="bea25-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="bea25-225">Por exemplo, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="bea25-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="bea25-226">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="bea25-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="bea25-227">O arquivo *index. cshtml* contém marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="bea25-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="bea25-228">O HTML renderizado:</span><span class="sxs-lookup"><span data-stu-id="bea25-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="bea25-229">Quando o botão de exclusão é renderizado em HTML, seu [formsaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="bea25-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="bea25-230">A ID de contato do cliente, especificada pelo `asp-route-id` atributo.</span><span class="sxs-lookup"><span data-stu-id="bea25-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="bea25-231">O `handler` , especificado pelo `asp-page-handler` atributo.</span><span class="sxs-lookup"><span data-stu-id="bea25-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="bea25-232">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="bea25-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="bea25-233">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="bea25-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="bea25-234">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="bea25-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="bea25-235">Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="bea25-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="bea25-236">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="bea25-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="bea25-237">Obtém o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="bea25-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="bea25-238">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="bea25-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="bea25-239">Se o contato do cliente for encontrado, ele é removido e o banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="bea25-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="bea25-240">Chama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="bea25-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="bea25-241">O arquivo Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="bea25-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="bea25-242">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="bea25-243">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="bea25-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="bea25-244">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="bea25-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="bea25-245">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="bea25-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="bea25-246">O arquivo *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="bea25-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="bea25-247">Validação</span><span class="sxs-lookup"><span data-stu-id="bea25-247">Validation</span></span>

<span data-ttu-id="bea25-248">Regras de validação:</span><span class="sxs-lookup"><span data-stu-id="bea25-248">Validation rules:</span></span>

* <span data-ttu-id="bea25-249">São especificadas declarativamente na classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="bea25-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="bea25-250">São impostos em todos os lugares no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bea25-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="bea25-251">O <xref:System.ComponentModel.DataAnnotations> namespace fornece um conjunto de atributos de validação internos que são aplicados declarativamente a uma classe ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="bea25-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="bea25-252">Annotations também contém atributos de formatação como [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) essa ajuda com a formatação e não fornecem nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="bea25-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="bea25-253">Considere o `Customer` modelo:</span><span class="sxs-lookup"><span data-stu-id="bea25-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="bea25-254">Usando o seguinte arquivo de exibição *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bea25-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="bea25-255">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="bea25-255">The preceding code:</span></span>

* <span data-ttu-id="bea25-256">Inclui scripts de validação jQuery e jQuery.</span><span class="sxs-lookup"><span data-stu-id="bea25-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="bea25-257">Usa os `<div />` `<span />` [auxiliares de marcação](xref:mvc/views/tag-helpers/intro) e para habilitar:</span><span class="sxs-lookup"><span data-stu-id="bea25-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="bea25-258">Validação no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="bea25-258">Client-side validation.</span></span>
  * <span data-ttu-id="bea25-259">Renderização de erro de validação.</span><span class="sxs-lookup"><span data-stu-id="bea25-259">Validation error rendering.</span></span>

* <span data-ttu-id="bea25-260">Gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="bea25-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="bea25-261">A postagem do valor de criar formulário sem um nome exibe a mensagem de erro "o campo nome é obrigatório".</span><span class="sxs-lookup"><span data-stu-id="bea25-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="bea25-262">no formulário.</span><span class="sxs-lookup"><span data-stu-id="bea25-262">on the form.</span></span> <span data-ttu-id="bea25-263">Se o JavaScript estiver habilitado no cliente, o navegador exibirá o erro sem postar no servidor.</span><span class="sxs-lookup"><span data-stu-id="bea25-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="bea25-264">O `[StringLength(10)]` atributo é gerado `data-val-length-max="10"` no HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="bea25-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="bea25-265">`data-val-length-max` impede que os navegadores insiram mais do que o comprimento máximo especificado.</span><span class="sxs-lookup"><span data-stu-id="bea25-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="bea25-266">Se uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) for usada para editar e reproduzir a postagem:</span><span class="sxs-lookup"><span data-stu-id="bea25-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="bea25-267">Com o nome maior que 10.</span><span class="sxs-lookup"><span data-stu-id="bea25-267">With the name longer than 10.</span></span>
* <span data-ttu-id="bea25-268">A mensagem de erro "o nome do campo deve ser uma cadeia de caracteres com um comprimento máximo de 10".</span><span class="sxs-lookup"><span data-stu-id="bea25-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="bea25-269">é retornado.</span><span class="sxs-lookup"><span data-stu-id="bea25-269">is returned.</span></span>

<span data-ttu-id="bea25-270">Considere o seguinte `Movie` modelo:</span><span class="sxs-lookup"><span data-stu-id="bea25-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="bea25-271">Os atributos de validação especificam o comportamento a ser aplicado nas propriedades de modelo às quais eles são aplicados:</span><span class="sxs-lookup"><span data-stu-id="bea25-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="bea25-272">Os `Required` `MinimumLength` atributos e indicam que uma propriedade deve ter um valor, mas nada impede que um usuário insira espaço em branco para atender a essa validação.</span><span class="sxs-lookup"><span data-stu-id="bea25-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="bea25-273">O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos.</span><span class="sxs-lookup"><span data-stu-id="bea25-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="bea25-274">No código anterior, "Gênero":</span><span class="sxs-lookup"><span data-stu-id="bea25-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="bea25-275">Deve usar apenas letras.</span><span class="sxs-lookup"><span data-stu-id="bea25-275">Must only use letters.</span></span>
  * <span data-ttu-id="bea25-276">A primeira letra deve ser maiúscula.</span><span class="sxs-lookup"><span data-stu-id="bea25-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="bea25-277">Espaços em branco, números e caracteres especiais não são permitidos.</span><span class="sxs-lookup"><span data-stu-id="bea25-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="bea25-278">A "Classificação" `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="bea25-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="bea25-279">Exige que o primeiro caractere seja uma letra maiúscula.</span><span class="sxs-lookup"><span data-stu-id="bea25-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="bea25-280">Permite caracteres especiais e números em espaços subsequentes.</span><span class="sxs-lookup"><span data-stu-id="bea25-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="bea25-281">"PG-13" é válido para uma classificação, mas é um erro em "Gênero".</span><span class="sxs-lookup"><span data-stu-id="bea25-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="bea25-282">O atributo `Range` restringe um valor a um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="bea25-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="bea25-283">O `StringLength` atributo define o comprimento máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu comprimento mínimo.</span><span class="sxs-lookup"><span data-stu-id="bea25-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="bea25-284">Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="bea25-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="bea25-285">A página criar para o `Movie` modelo mostra exibe erros com valores inválidos:</span><span class="sxs-lookup"><span data-stu-id="bea25-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="bea25-287">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="bea25-287">For more information, see:</span></span>

* [<span data-ttu-id="bea25-288">Adicionar validação ao aplicativo de filme</span><span class="sxs-lookup"><span data-stu-id="bea25-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="bea25-289">[Validação de modelo no ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="bea25-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="bea25-290">Manipular solicitações HEAD com um fallback de manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="bea25-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="bea25-291">`HEAD` as solicitações permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="bea25-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="bea25-292">Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="bea25-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="bea25-293">Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="bea25-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="bea25-294">Razor As páginas voltarão para chamar o `OnGet` manipulador se nenhum `OnHead` manipulador for definido.</span><span class="sxs-lookup"><span data-stu-id="bea25-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="bea25-295">XSRF/CSRF e Razor páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="bea25-296">Razor As páginas são protegidas pela [validação de antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="bea25-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="bea25-297">O [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens de antifalsificação em elementos de formulário HTML.</span><span class="sxs-lookup"><span data-stu-id="bea25-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="bea25-298">Usando layouts, parciais, modelos e auxiliares de marca com Razor páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="bea25-299">As páginas funcionam com todos os recursos do Razor mecanismo de exibição.</span><span class="sxs-lookup"><span data-stu-id="bea25-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="bea25-300">Layouts, parciais, modelos, auxiliares de marca, *_ViewStart. cshtml* e *_ViewImports. cshtml* funcionam da mesma maneira que fazem para Razor exibições convencionais.</span><span class="sxs-lookup"><span data-stu-id="bea25-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="bea25-301">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="bea25-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="bea25-302">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="bea25-303">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="bea25-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="bea25-304">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="bea25-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="bea25-305">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="bea25-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="bea25-306">O conteúdo da Razor página é renderizado onde `@RenderBody()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="bea25-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="bea25-307">Para obter mais informações, consulte [página de layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="bea25-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="bea25-308">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bea25-309">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="bea25-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="bea25-310">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="bea25-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="bea25-311">Um layout na pasta *páginas/compartilhadas* pode ser usado em qualquer Razor página na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="bea25-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="bea25-312">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="bea25-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="bea25-313">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="bea25-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="bea25-314">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="bea25-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="bea25-315">Razor As páginas destinam-se a contar com a hierarquia de pastas, não com convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="bea25-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="bea25-316">Exibir pesquisa de uma Razor página inclui a pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="bea25-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="bea25-317">Os layouts, modelos e parciais usados com controladores MVC e Razor exibições convencionais *funcionam apenas*.</span><span class="sxs-lookup"><span data-stu-id="bea25-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="bea25-318">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="bea25-319">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="bea25-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="bea25-320">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="bea25-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="bea25-321">A `@namespace` diretiva definida em uma página:</span><span class="sxs-lookup"><span data-stu-id="bea25-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="bea25-322">A `@namespace` diretiva define o namespace para a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="bea25-323">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="bea25-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="bea25-324">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="bea25-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="bea25-325">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="bea25-326">Por exemplo, a classe `PageModel`*Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="bea25-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="bea25-327">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="bea25-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="bea25-328">O namespace gerado para a página *pages/Customers/Edit. cshtml* Razor é o mesmo que a `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="bea25-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="bea25-329">`@namespace`*também funciona com Razor exibições convencionais.*</span><span class="sxs-lookup"><span data-stu-id="bea25-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="bea25-330">Considere o arquivo de exibição *páginas/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bea25-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="bea25-331">O arquivo de exibição *páginas/Create. cshtml* atualizado com *_ViewImports. cshtml* e o arquivo de layout anterior:</span><span class="sxs-lookup"><span data-stu-id="bea25-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="bea25-332">No código anterior, o *_ViewImports. cshtml* importou os auxiliares de namespace e de marca.</span><span class="sxs-lookup"><span data-stu-id="bea25-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="bea25-333">O arquivo de layout importou os arquivos JavaScript.</span><span class="sxs-lookup"><span data-stu-id="bea25-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="bea25-334">O [ Razor projeto inicial de páginas](#rpvs17) contém as *páginas/_ValidationScriptsPartial. cshtml*, que conectam a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="bea25-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="bea25-335">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="bea25-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="bea25-336">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-336">URL generation for Pages</span></span>

<span data-ttu-id="bea25-337">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="bea25-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="bea25-338">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="bea25-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="bea25-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="bea25-339">*/Pages*</span></span>

  * <span data-ttu-id="bea25-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="bea25-341">*Privacidade. cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="bea25-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="bea25-342">*/Customers*</span></span>

    * <span data-ttu-id="bea25-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="bea25-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="bea25-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-345">*Index.cshtml*</span></span>

<span data-ttu-id="bea25-346">As páginas *pages/Customers/Create. cshtml* e *pages/Customers/Edit. cshtml* redirecionam para *pages/Customers/index. cshtml* após o êxito.</span><span class="sxs-lookup"><span data-stu-id="bea25-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="bea25-347">A cadeia de caracteres `./Index` é um nome de página relativo usado para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="bea25-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="bea25-348">Ele é usado para gerar URLs para a página *pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bea25-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="bea25-349">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="bea25-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="bea25-350">O nome de página absoluto `/Index` é usado para gerar URLs para a página *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bea25-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="bea25-351">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="bea25-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="bea25-352">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="bea25-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="bea25-353">Os exemplos de geração de URL anteriores oferecem opções avançadas e recursos funcionais por meio da codificação de uma URL.</span><span class="sxs-lookup"><span data-stu-id="bea25-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="bea25-354">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="bea25-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="bea25-355">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="bea25-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="bea25-356">A tabela a seguir mostra qual página de índice é selecionada usando `RedirectToPage` parâmetros diferentes em *pages/Customers/Create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bea25-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="bea25-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="bea25-357">RedirectToPage(x)</span></span>| <span data-ttu-id="bea25-358">Página</span><span class="sxs-lookup"><span data-stu-id="bea25-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="bea25-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="bea25-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="bea25-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="bea25-360">*Pages/Index*</span></span> |
| <span data-ttu-id="bea25-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="bea25-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="bea25-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="bea25-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="bea25-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="bea25-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="bea25-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="bea25-364">*Pages/Index*</span></span> |
| <span data-ttu-id="bea25-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="bea25-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="bea25-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="bea25-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="bea25-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="bea25-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="bea25-368">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="bea25-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="bea25-369">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="bea25-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="bea25-370">Quando nomes relativos são usados para vincular entre páginas em uma pasta:</span><span class="sxs-lookup"><span data-stu-id="bea25-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="bea25-371">Renomear uma pasta não interrompe os links relativos.</span><span class="sxs-lookup"><span data-stu-id="bea25-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="bea25-372">Os links não são desfeitos porque não incluem o nome da pasta.</span><span class="sxs-lookup"><span data-stu-id="bea25-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="bea25-373">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="bea25-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="bea25-374">Para obter mais informações, consulte <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="bea25-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="bea25-375">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="bea25-375">ViewData attribute</span></span>

<span data-ttu-id="bea25-376">Os dados podem ser passados para uma página com <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="bea25-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="bea25-377">As propriedades com o `[ViewData]` atributo têm seus valores armazenados e carregados do <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="bea25-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="bea25-378">No exemplo a seguir, o `AboutModel` aplica o `[ViewData]` atributo à `Title` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="bea25-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="bea25-379">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="bea25-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="bea25-380">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="bea25-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="bea25-381">TempData</span><span class="sxs-lookup"><span data-stu-id="bea25-381">TempData</span></span>

<span data-ttu-id="bea25-382">ASP.NET Core expõe o <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="bea25-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="bea25-383">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="bea25-383">This property stores data until it's read.</span></span> <span data-ttu-id="bea25-384">Os métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="bea25-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="bea25-385">`TempData` é útil para redirecionamento, quando os dados são necessários para mais do que uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="bea25-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="bea25-386">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="bea25-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="bea25-387">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="bea25-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="bea25-388">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="bea25-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="bea25-389">Para obter mais informações, consulte [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="bea25-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="bea25-390">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="bea25-390">Multiple handlers per page</span></span>

<span data-ttu-id="bea25-391">A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="bea25-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="bea25-392">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="bea25-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="bea25-393">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="bea25-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="bea25-394">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="bea25-395">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="bea25-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="bea25-396">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="bea25-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="bea25-397">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="bea25-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="bea25-398">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="bea25-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="bea25-399">No exemplo anterior, os métodos de página são OnPost **JoinList** Async e OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="bea25-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="bea25-400">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bea25-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="bea25-401">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="bea25-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="bea25-402">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bea25-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="bea25-403">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="bea25-403">Custom routes</span></span>

<span data-ttu-id="bea25-404">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="bea25-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="bea25-405">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-405">Specify a custom route to a page.</span></span> <span data-ttu-id="bea25-406">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="bea25-407">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-407">Append segments to a page's default route.</span></span> <span data-ttu-id="bea25-408">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="bea25-409">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="bea25-410">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="bea25-411">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="bea25-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="bea25-412">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="bea25-413">Se você não gostar da cadeia de caracteres de consulta `?handler=JoinList` na URL, altere a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="bea25-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="bea25-414">A rota pode ser personalizada adicionando um modelo de rota entre aspas duplas após a `@page` diretiva.</span><span class="sxs-lookup"><span data-stu-id="bea25-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="bea25-415">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="bea25-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="bea25-416">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bea25-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="bea25-417">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="bea25-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="bea25-418">Configuração e configurações avançadas</span><span class="sxs-lookup"><span data-stu-id="bea25-418">Advanced configuration and settings</span></span>

<span data-ttu-id="bea25-419">A configuração e as configurações nas seções a seguir não são exigidas pela maioria dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="bea25-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="bea25-420">Para configurar opções avançadas, use a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> sobrecarga que configura <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="bea25-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="bea25-421">Use o <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para definir o diretório raiz para páginas ou adicione convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="bea25-422">Para obter mais informações sobre convenções, consulte [ Razor páginas convenções de autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="bea25-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="bea25-423">Para pré-compilar exibições, consulte [ Razor Exibir compilação](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="bea25-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="bea25-424">Especificar que Razor as páginas estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="bea25-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="bea25-425">Por padrão, Razor as páginas têm raiz no diretório */pages*</span><span class="sxs-lookup"><span data-stu-id="bea25-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="bea25-426">Adicionar <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que suas Razor páginas estão na [raiz do conteúdo](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="bea25-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="bea25-427">Especificar que as Razor páginas estejam em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="bea25-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="bea25-428">Adicionar <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que as Razor páginas estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="bea25-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="bea25-429">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bea25-429">Additional resources</span></span>

* <span data-ttu-id="bea25-430">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="bea25-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="bea25-431">Autorizar atributo e Razor páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="bea25-432">Baixar ou exibir código de exemplo</span><span class="sxs-lookup"><span data-stu-id="bea25-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="bea25-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bea25-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bea25-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bea25-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bea25-435">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bea25-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="bea25-436">Criar um Razor projeto de páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-436">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bea25-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bea25-437">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bea25-438">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um Razor projeto de páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-438">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bea25-439">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bea25-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bea25-440">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="bea25-440">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="bea25-441">Abra o arquivo *.csproj* gerado do Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="bea25-441">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bea25-442">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bea25-442">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bea25-443">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="bea25-443">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="bea25-444">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="bea25-444">Razor Pages</span></span>

<span data-ttu-id="bea25-445">Razor As páginas estão habilitadas no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bea25-445">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="bea25-446">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="bea25-446">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="bea25-447">O código anterior é muito parecido com um [ Razor arquivo de exibição](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="bea25-447">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="bea25-448">O que o torna diferentes é a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="bea25-448">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="bea25-449">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="bea25-449">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="bea25-450">`@page` deve ser a primeira Razor diretiva em uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-450">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bea25-451">`@page` afeta o comportamento de outras Razor construções.</span><span class="sxs-lookup"><span data-stu-id="bea25-451">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="bea25-452">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="bea25-452">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="bea25-453">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-453">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="bea25-454">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bea25-454">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="bea25-455">Por convenção, o `PageModel` arquivo de classe tem o mesmo nome que o Razor arquivo de paginação com *. cs* anexado.</span><span class="sxs-lookup"><span data-stu-id="bea25-455">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="bea25-456">Por exemplo, a Razor página anterior é *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bea25-456">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="bea25-457">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="bea25-457">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="bea25-458">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="bea25-458">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="bea25-459">A tabela a seguir mostra um Razor caminho de página e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="bea25-459">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="bea25-460">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="bea25-460">File name and path</span></span>               | <span data-ttu-id="bea25-461">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="bea25-461">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="bea25-462">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-462">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="bea25-463">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="bea25-463">`/` or `/Index`</span></span> |
| <span data-ttu-id="bea25-464">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-464">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="bea25-465">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-465">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="bea25-466">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-466">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="bea25-467">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="bea25-467">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="bea25-468">Observações:</span><span class="sxs-lookup"><span data-stu-id="bea25-468">Notes:</span></span>

* <span data-ttu-id="bea25-469">O tempo de execução procura por Razor arquivos de páginas na pasta *páginas* por padrão.</span><span class="sxs-lookup"><span data-stu-id="bea25-469">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="bea25-470">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-470">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="bea25-471">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="bea25-471">Write a basic form</span></span>

<span data-ttu-id="bea25-472">Razor As páginas são projetadas para tornar os padrões comuns usados com navegadores da Web fáceis de implementar ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bea25-472">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="bea25-473">[Associação de modelo](xref:mvc/models/model-binding), [auxiliares de marca](xref:mvc/views/tag-helpers/intro)e auxiliares HTML *funcionam apenas* com as propriedades definidas em uma Razor classe de página.</span><span class="sxs-lookup"><span data-stu-id="bea25-473">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="bea25-474">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="bea25-474">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="bea25-475">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="bea25-475">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="bea25-476">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="bea25-476">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="bea25-477">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="bea25-477">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="bea25-478">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-478">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="bea25-479">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bea25-479">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="bea25-480">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-480">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="bea25-481">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="bea25-481">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="bea25-482">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-482">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="bea25-483">Essa separação permite:</span><span class="sxs-lookup"><span data-stu-id="bea25-483">This separation allows:</span></span>

* <span data-ttu-id="bea25-484">Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bea25-484">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="bea25-485">[Teste de unidade](xref:test/razor-pages-tests) das páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-485">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="bea25-486">A página tem um  *método de manipulador*`OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="bea25-486">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="bea25-487">Você pode adicionar métodos de manipulador para qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="bea25-487">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="bea25-488">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="bea25-488">The most common handlers are:</span></span>

* <span data-ttu-id="bea25-489">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-489">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="bea25-490">Amostra de [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="bea25-490">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="bea25-491">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="bea25-491">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="bea25-492">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="bea25-492">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="bea25-493">O código anterior é típico para Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-493">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="bea25-494">Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:</span><span class="sxs-lookup"><span data-stu-id="bea25-494">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="bea25-495">O `OnPostAsync` código no exemplo anterior é semelhante ao código de controlador típico.</span><span class="sxs-lookup"><span data-stu-id="bea25-495">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="bea25-496">A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation), [validação](xref:mvc/models/validation)e resultados de ação, é compartilhada.</span><span class="sxs-lookup"><span data-stu-id="bea25-496">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="bea25-497">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="bea25-497">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="bea25-498">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="bea25-498">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="bea25-499">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="bea25-499">Check for validation errors.</span></span>

* <span data-ttu-id="bea25-500">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="bea25-500">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="bea25-501">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="bea25-501">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="bea25-502">A validação do lado do cliente é idêntica para aplicativos ASP.NET Core MVC tradicionais.</span><span class="sxs-lookup"><span data-stu-id="bea25-502">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="bea25-503">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="bea25-503">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="bea25-504">Quando os dados são inseridos com êxito, o método de manipulador `OnPostAsync` chama o método auxiliar `RedirectToPage` para retornar uma instância de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="bea25-504">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="bea25-505">`RedirectToPage` é um novo resultado de ação, semelhante a `RedirectToAction` ou `RedirectToRoute`, mas personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-505">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="bea25-506">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="bea25-506">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="bea25-507">`RedirectToPage` é detalhado na seção [geração de URL para páginas](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="bea25-507">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="bea25-508">Quando o formulário enviado tem erros de validação (que são passados para o servidor), o método de manipulador `OnPostAsync` chama o método auxiliar `Page`.</span><span class="sxs-lookup"><span data-stu-id="bea25-508">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="bea25-509">`Page` retorna uma instância de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="bea25-509">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="bea25-510">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="bea25-510">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="bea25-511">`PageResult` é o tipo de retorno padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="bea25-511">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="bea25-512">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-512">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="bea25-513">A propriedade `Customer` usa o atributo `[BindProperty]` para aceitar o model binding.</span><span class="sxs-lookup"><span data-stu-id="bea25-513">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="bea25-514">Razor As páginas, por padrão, associam propriedades somente a não `GET` verbos.</span><span class="sxs-lookup"><span data-stu-id="bea25-514">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="bea25-515">A associação a propriedades pode reduzir a quantidade de código que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="bea25-515">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="bea25-516">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="bea25-516">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="bea25-517">A home page (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="bea25-517">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="bea25-518">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="bea25-518">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="bea25-519">O arquivo *cshtml* contém a marcação a seguir para criar um link de edição para cada contato:</span><span class="sxs-lookup"><span data-stu-id="bea25-519">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="bea25-520">O `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [auxiliar de marca de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) usou o `asp-route-{value}` atributo para gerar um link para a página de edição.</span><span class="sxs-lookup"><span data-stu-id="bea25-520">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="bea25-521">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="bea25-521">The link contains route data with the contact ID.</span></span> <span data-ttu-id="bea25-522">Por exemplo, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="bea25-522">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="bea25-523">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em Razor arquivos.</span><span class="sxs-lookup"><span data-stu-id="bea25-523">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="bea25-524">Os Auxiliares de Marcação são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="bea25-524">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="bea25-525">O arquivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-525">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="bea25-526">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-526">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="bea25-527">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="bea25-527">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="bea25-528">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="bea25-528">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="bea25-529">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="bea25-529">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="bea25-530">O arquivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bea25-530">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="bea25-531">O arquivo *Index.cshtml* também contém a marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="bea25-531">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="bea25-532">Quando o botão de exclusão é renderizado em HTML, seu `formaction` inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="bea25-532">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="bea25-533">A ID de contato do cliente especificada pelo atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="bea25-533">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="bea25-534">O `handler` especificado pelo atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="bea25-534">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="bea25-535">Este é um exemplo de um botão de exclusão renderizado com uma ID de contato do cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="bea25-535">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="bea25-536">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="bea25-536">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="bea25-537">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="bea25-537">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="bea25-538">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="bea25-538">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="bea25-539">Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="bea25-539">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="bea25-540">O código a seguir mostra o `OnPostDeleteAsync` manipulador:</span><span class="sxs-lookup"><span data-stu-id="bea25-540">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="bea25-541">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="bea25-541">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="bea25-542">Aceita o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="bea25-542">Accepts the `id` from the query string.</span></span> <span data-ttu-id="bea25-543">Se a diretiva de página *index. cshtml* continha a restrição `"{id:int?}"` de roteamento, ela `id` virá de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="bea25-543">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="bea25-544">Os dados de rota para `id` são especificados no URI, como `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="bea25-544">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="bea25-545">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="bea25-545">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="bea25-546">Se o contato do cliente for encontrado, eles serão removidos da lista de contatos do cliente.</span><span class="sxs-lookup"><span data-stu-id="bea25-546">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="bea25-547">O banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="bea25-547">The database is updated.</span></span>
* <span data-ttu-id="bea25-548">Chama `RedirectToPage` para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="bea25-548">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="bea25-549">Marque as propriedades da página conforme necessário</span><span class="sxs-lookup"><span data-stu-id="bea25-549">Mark page properties as required</span></span>

<span data-ttu-id="bea25-550">As propriedades em um `PageModel` podem ser marcadas com o atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) :</span><span class="sxs-lookup"><span data-stu-id="bea25-550">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="bea25-551">Para obter mais informações, confira [Validação de modelo](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="bea25-551">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="bea25-552">Manipular solicitações HEAD com um fallback de manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="bea25-552">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="bea25-553">As solicitações `HEAD` permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="bea25-553">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="bea25-554">Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="bea25-554">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="bea25-555">Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="bea25-555">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="bea25-556">No ASP.NET Core 2,1 ou posterior, as Razor páginas voltarão para chamar o `OnGet` manipulador se nenhum `OnHead` manipulador for definido.</span><span class="sxs-lookup"><span data-stu-id="bea25-556">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="bea25-557">Esse comportamento é habilitado pela chamada para [SetCompatibilityVersion](xref:mvc/compatibility-version) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bea25-557">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="bea25-558">Os modelos padrão geram a chamada `SetCompatibilityVersion` no ASP.NET Core 2.1 e 2.2.</span><span class="sxs-lookup"><span data-stu-id="bea25-558">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="bea25-559">`SetCompatibilityVersion` define efetivamente a Razor opção Pages `AllowMappingHeadRequestsToGetHandler` como `true` .</span><span class="sxs-lookup"><span data-stu-id="bea25-559">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="bea25-560">Em vez de aceitar todos os comportamentos com `SetCompatibilityVersion`, você pode aceitar explicitamente participar de comportamentos *específicos*.</span><span class="sxs-lookup"><span data-stu-id="bea25-560">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="bea25-561">O código a seguir aceita permitir que solicitações `HEAD` sejam mapeadas para o manipulador `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="bea25-561">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="bea25-562">XSRF/CSRF e Razor páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-562">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="bea25-563">Você não precisa escrever nenhum código para [validação antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="bea25-563">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="bea25-564">A geração e a validação de tokens antifalsificação são incluídas automaticamente nas Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-564">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="bea25-565">Usando layouts, parciais, modelos e auxiliares de marca com Razor páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-565">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="bea25-566">As páginas funcionam com todos os recursos do Razor mecanismo de exibição.</span><span class="sxs-lookup"><span data-stu-id="bea25-566">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="bea25-567">Layouts, parciais, modelos, auxiliares de marca, *_ViewStart. cshtml*, *_ViewImports. cshtml* funcionam da mesma maneira que fazem para Razor exibições convencionais.</span><span class="sxs-lookup"><span data-stu-id="bea25-567">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="bea25-568">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="bea25-568">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="bea25-569">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-569">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="bea25-570">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="bea25-570">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="bea25-571">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="bea25-571">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="bea25-572">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="bea25-572">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="bea25-573">Veja [página de layout](xref:mvc/views/layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bea25-573">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="bea25-574">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-574">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bea25-575">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="bea25-575">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="bea25-576">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="bea25-576">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="bea25-577">Um layout na pasta *páginas/compartilhadas* pode ser usado em qualquer Razor página na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="bea25-577">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="bea25-578">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="bea25-578">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="bea25-579">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="bea25-579">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="bea25-580">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="bea25-580">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="bea25-581">Razor As páginas destinam-se a contar com a hierarquia de pastas, não com convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="bea25-581">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="bea25-582">Exibir pesquisa de uma Razor página inclui a pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="bea25-582">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="bea25-583">Os layouts, modelos e parciais que você está usando com controladores MVC e Razor exibições convencionais *funcionam apenas*.</span><span class="sxs-lookup"><span data-stu-id="bea25-583">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="bea25-584">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-584">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="bea25-585">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="bea25-585">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="bea25-586">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="bea25-586">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="bea25-587">Quando a diretiva `@namespace` é usada explicitamente em uma página:</span><span class="sxs-lookup"><span data-stu-id="bea25-587">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="bea25-588">A diretiva define o namespace da página.</span><span class="sxs-lookup"><span data-stu-id="bea25-588">The directive sets the namespace for the page.</span></span> <span data-ttu-id="bea25-589">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="bea25-589">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="bea25-590">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="bea25-590">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="bea25-591">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="bea25-591">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="bea25-592">Por exemplo, a classe `PageModel`*Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="bea25-592">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="bea25-593">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="bea25-593">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="bea25-594">O namespace gerado para a página *pages/Customers/Edit. cshtml* Razor é o mesmo que a `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="bea25-594">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="bea25-595">`@namespace`*também funciona com Razor exibições convencionais.*</span><span class="sxs-lookup"><span data-stu-id="bea25-595">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="bea25-596">O arquivo de exibição *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="bea25-596">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="bea25-597">O arquivo de exibição *Pages/Create.cshtml* atualizado:</span><span class="sxs-lookup"><span data-stu-id="bea25-597">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="bea25-598">O [ Razor projeto inicial de páginas](#rpvs17) contém as *páginas/_ValidationScriptsPartial. cshtml*, que conectam a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="bea25-598">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="bea25-599">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="bea25-599">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="bea25-600">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-600">URL generation for Pages</span></span>

<span data-ttu-id="bea25-601">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="bea25-601">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="bea25-602">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="bea25-602">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="bea25-603">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="bea25-603">*/Pages*</span></span>

  * <span data-ttu-id="bea25-604">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-604">*Index.cshtml*</span></span>
  * <span data-ttu-id="bea25-605">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="bea25-605">*/Customers*</span></span>

    * <span data-ttu-id="bea25-606">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-606">*Create.cshtml*</span></span>
    * <span data-ttu-id="bea25-607">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-607">*Edit.cshtml*</span></span>
    * <span data-ttu-id="bea25-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bea25-608">*Index.cshtml*</span></span>

<span data-ttu-id="bea25-609">As páginas *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* redirecionam para o *Pages/Index.cshtml* após êxito.</span><span class="sxs-lookup"><span data-stu-id="bea25-609">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="bea25-610">A cadeia de caracteres `/Index` faz parte do URI para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="bea25-610">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="bea25-611">A cadeia de caracteres `/Index` pode ser usada para gerar URIs para a página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bea25-611">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="bea25-612">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="bea25-612">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="bea25-613">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="bea25-613">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="bea25-614">Os exemplos anteriores de geração de URL oferecem opções avançadas e recursos funcionais para codificar uma URL.</span><span class="sxs-lookup"><span data-stu-id="bea25-614">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="bea25-615">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="bea25-615">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="bea25-616">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="bea25-616">URL generation for pages supports relative names.</span></span> <span data-ttu-id="bea25-617">A tabela a seguir mostra qual página de Índice é selecionada com diferentes parâmetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bea25-617">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="bea25-618">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="bea25-618">RedirectToPage(x)</span></span>| <span data-ttu-id="bea25-619">Página</span><span class="sxs-lookup"><span data-stu-id="bea25-619">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="bea25-620">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="bea25-620">RedirectToPage("/Index")</span></span> | <span data-ttu-id="bea25-621">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="bea25-621">*Pages/Index*</span></span> |
| <span data-ttu-id="bea25-622">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="bea25-622">RedirectToPage("./Index");</span></span> | <span data-ttu-id="bea25-623">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="bea25-623">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="bea25-624">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="bea25-624">RedirectToPage("../Index")</span></span> | <span data-ttu-id="bea25-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="bea25-625">*Pages/Index*</span></span> |
| <span data-ttu-id="bea25-626">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="bea25-626">RedirectToPage("Index")</span></span>  | <span data-ttu-id="bea25-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="bea25-627">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="bea25-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")`  são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="bea25-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="bea25-629">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="bea25-629">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="bea25-630">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="bea25-630">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="bea25-631">Se você usar nomes relativos para vincular entre páginas em uma pasta, você poderá renomear essa pasta.</span><span class="sxs-lookup"><span data-stu-id="bea25-631">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="bea25-632">Todos os links ainda funcionarão (porque eles não incluirão o nome da pasta).</span><span class="sxs-lookup"><span data-stu-id="bea25-632">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="bea25-633">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="bea25-633">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="bea25-634">Para obter mais informações, consulte <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="bea25-634">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="bea25-635">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="bea25-635">ViewData attribute</span></span>

<span data-ttu-id="bea25-636">Os dados podem ser passados para uma página com [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="bea25-636">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="bea25-637">As propriedades em controladores ou Razor modelos de página com o `[ViewData]` atributo têm seus valores armazenados e carregados do [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="bea25-637">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="bea25-638">No exemplo a seguir, o `AboutModel` contém uma `Title` propriedade marcada com `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="bea25-638">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="bea25-639">A propriedade `Title` está definida como o título da página Sobre:</span><span class="sxs-lookup"><span data-stu-id="bea25-639">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="bea25-640">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="bea25-640">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="bea25-641">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="bea25-641">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="bea25-642">TempData</span><span class="sxs-lookup"><span data-stu-id="bea25-642">TempData</span></span>

<span data-ttu-id="bea25-643">O ASP.NET Core expõe a propriedade [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) em um [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="bea25-643">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="bea25-644">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="bea25-644">This property stores data until it's read.</span></span> <span data-ttu-id="bea25-645">Os métodos `Keep` e `Peek` podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="bea25-645">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="bea25-646">`TempData` é útil para redirecionamento nos casos em que os dados são necessários para mais de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="bea25-646">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="bea25-647">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="bea25-647">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="bea25-648">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="bea25-648">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="bea25-649">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="bea25-649">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="bea25-650">Para obter mais informações, confira [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="bea25-650">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="bea25-651">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="bea25-651">Multiple handlers per page</span></span>

<span data-ttu-id="bea25-652">A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="bea25-652">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="bea25-653">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="bea25-653">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="bea25-654">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="bea25-654">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="bea25-655">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-655">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="bea25-656">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="bea25-656">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="bea25-657">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="bea25-657">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="bea25-658">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="bea25-658">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="bea25-659">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="bea25-659">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="bea25-660">No exemplo anterior, os métodos de página são OnPost **JoinList** Async e OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="bea25-660">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="bea25-661">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bea25-661">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="bea25-662">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="bea25-662">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="bea25-663">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bea25-663">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="bea25-664">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="bea25-664">Custom routes</span></span>

<span data-ttu-id="bea25-665">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="bea25-665">Use the `@page` directive to:</span></span>

* <span data-ttu-id="bea25-666">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-666">Specify a custom route to a page.</span></span> <span data-ttu-id="bea25-667">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-667">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="bea25-668">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-668">Append segments to a page's default route.</span></span> <span data-ttu-id="bea25-669">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-669">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="bea25-670">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="bea25-670">Append parameters to a page's default route.</span></span> <span data-ttu-id="bea25-671">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-671">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="bea25-672">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="bea25-672">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="bea25-673">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="bea25-673">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="bea25-674">Se você não gostar da cadeia de caracteres de consulta `?handler=JoinList` na URL, altere a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="bea25-674">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="bea25-675">A rota pode ser personalizada adicionando um modelo de rota entre aspas duplas após a `@page` diretiva.</span><span class="sxs-lookup"><span data-stu-id="bea25-675">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="bea25-676">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="bea25-676">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="bea25-677">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bea25-677">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="bea25-678">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="bea25-678">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="bea25-679">Configuração e definições</span><span class="sxs-lookup"><span data-stu-id="bea25-679">Configuration and settings</span></span>

<span data-ttu-id="bea25-680">Para configurar opções avançadas, use o método de extensão `AddRazorPagesOptions` no construtor de MVC:</span><span class="sxs-lookup"><span data-stu-id="bea25-680">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="bea25-681">No momento, você pode usar o `RazorPagesOptions` para definir o diretório raiz para páginas ou adicionar as convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="bea25-681">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="bea25-682">Permitiremos mais extensibilidade dessa maneira no futuro.</span><span class="sxs-lookup"><span data-stu-id="bea25-682">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="bea25-683">Para pré-compilar exibições, consulte [ Razor Exibir compilação](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="bea25-683">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="bea25-684">[Baixar ou exibir código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="bea25-684">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="bea25-685">Consulte Introdução [às Razor páginas](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="bea25-685">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="bea25-686">Especificar que Razor as páginas estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="bea25-686">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="bea25-687">Por padrão, Razor as páginas têm raiz no diretório */pages*</span><span class="sxs-lookup"><span data-stu-id="bea25-687">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="bea25-688">Adicione [with Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) ao [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que suas Razor páginas estão na [raiz do conteúdo](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="bea25-688">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="bea25-689">Especificar que as Razor páginas estejam em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="bea25-689">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="bea25-690">Adicione [with Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) ao [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que suas Razor páginas estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="bea25-690">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="bea25-691">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bea25-691">Additional resources</span></span>

* [<span data-ttu-id="bea25-692">Autorizar atributo e Razor páginas</span><span class="sxs-lookup"><span data-stu-id="bea25-692">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
