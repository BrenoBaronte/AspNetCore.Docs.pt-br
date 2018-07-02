---
title: Introdução a Páginas do Razor no ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 05/12/2018
uid: razor-pages/index
ms.openlocfilehash: 601d6ac2cb373c40fb1de5427b0ea6c299fa1f32
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296743"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="bc6db-103">Introdução a Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc6db-103">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="bc6db-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="bc6db-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="bc6db-105">Páginas do Razor é um novo aspecto do ASP.NET Core MVC que torna a codificação de cenários focados em página mais fácil e produtiva.</span><span class="sxs-lookup"><span data-stu-id="bc6db-105">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="bc6db-106">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="bc6db-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="bc6db-107">Este documento proporciona uma introdução a páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="bc6db-108">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="bc6db-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="bc6db-109">Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="bc6db-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="bc6db-110">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="bc6db-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bc6db-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="bc6db-111">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-prereqs.md)]

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a><span data-ttu-id="bc6db-112">Criando um projeto de Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-112">Creating a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc6db-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc6db-113">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="bc6db-114">Consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto de Páginas do Razor usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bc6db-114">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project using Visual Studio.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc6db-115">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="bc6db-115">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="bc6db-116">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-116">Run `dotnet new webapp` from the command line.</span></span>

[!INCLUDE[](~/includes/webapp-alias-notice.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="bc6db-118">Da linha de comando, execute `dotnet new razor`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-118">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

<span data-ttu-id="bc6db-119">Abra o arquivo *.csproj* gerado do Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="bc6db-119">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc6db-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc6db-120">Visual Studio Code</span></span>](#tab/visual-studio-code) 

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="bc6db-121">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-121">Run `dotnet new webapp` from the command line.</span></span>

[!INCLUDE[](~/includes/webapp-alias-notice.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="bc6db-123">Da linha de comando, execute `dotnet new razor`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-123">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bc6db-124">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="bc6db-124">.NET Core CLI</span></span>](#tab/netcore-cli) 

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="bc6db-125">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-125">Run `dotnet new webapp` from the command line.</span></span>

[!INCLUDE[](~/includes/webapp-alias-notice.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="bc6db-127">Da linha de comando, execute `dotnet new razor`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-127">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

---

## <a name="razor-pages"></a><span data-ttu-id="bc6db-128">Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-128">Razor Pages</span></span>

<span data-ttu-id="bc6db-129">O Páginas do Razor está habilitado em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-129">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="bc6db-130">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="bc6db-130">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="bc6db-131">O código anterior é muito parecido com um arquivo de exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-131">The preceding code looks a lot like a Razor view file.</span></span> <span data-ttu-id="bc6db-132">O que o torna diferentes é a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-132">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="bc6db-133">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="bc6db-133">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="bc6db-134">`@page` deve ser a primeira diretiva do Razor em uma página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-134">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bc6db-135">`@page` afeta o comportamento de outros constructos do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-135">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="bc6db-136">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="bc6db-136">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="bc6db-137">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-137">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="bc6db-138">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-138">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="bc6db-139">Por convenção, o arquivo de classe `PageModel` tem o mesmo nome que o arquivo na Página do Razor com *.cs* acrescentado.</span><span class="sxs-lookup"><span data-stu-id="bc6db-139">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="bc6db-140">Por exemplo, a Página do Razor anterior é *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-140">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="bc6db-141">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-141">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="bc6db-142">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="bc6db-142">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="bc6db-143">A tabela a seguir mostra um caminho de Página do Razor e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="bc6db-143">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="bc6db-144">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="bc6db-144">File name and path</span></span>               | <span data-ttu-id="bc6db-145">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="bc6db-145">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="bc6db-146">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bc6db-146">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="bc6db-147">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="bc6db-147">`/` or `/Index`</span></span> |
| <span data-ttu-id="bc6db-148">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bc6db-148">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="bc6db-149">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bc6db-149">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="bc6db-150">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bc6db-150">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="bc6db-151">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="bc6db-151">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="bc6db-152">Notas:</span><span class="sxs-lookup"><span data-stu-id="bc6db-152">Notes:</span></span>

* <span data-ttu-id="bc6db-153">O tempo de execução procura arquivos de Páginas do Razor na pasta *Pages* por padrão.</span><span class="sxs-lookup"><span data-stu-id="bc6db-153">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="bc6db-154">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-154">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="writing-a-basic-form"></a><span data-ttu-id="bc6db-155">Escrevendo um formulário básico</span><span class="sxs-lookup"><span data-stu-id="bc6db-155">Writing a basic form</span></span>

<span data-ttu-id="bc6db-156">Páginas do Razor foi projetado para facilitar a implementação de padrões comuns usados com navegadores da Web ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bc6db-156">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="bc6db-157">[Associação de modelos](xref:mvc/models/model-binding), [auxiliares de marcas](xref:mvc/views/tag-helpers/intro) e auxiliares HTML *funcionam todos apenas* com as propriedades definidas em uma classe de Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-157">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="bc6db-158">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="bc6db-158">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="bc6db-159">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="bc6db-159">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="bc6db-160">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="bc6db-160">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="bc6db-161">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="bc6db-161">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="bc6db-162">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-162">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="bc6db-163">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-163">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="bc6db-164">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-164">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="bc6db-165">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="bc6db-165">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="bc6db-166">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-166">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="bc6db-167">Esta separação permite gerenciar as dependências da página por meio de [injeção de dependência](xref:fundamentals/dependency-injection) e realizar um [teste de unidade](xref:test/razor-pages-tests) nas páginas.</span><span class="sxs-lookup"><span data-stu-id="bc6db-167">This separation allows you to manage page dependencies through [dependency injection](xref:fundamentals/dependency-injection) and to [unit test](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="bc6db-168">A página tem um *método de manipulador* `OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="bc6db-168">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="bc6db-169">Você pode adicionar métodos de manipulador para qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="bc6db-169">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="bc6db-170">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="bc6db-170">The most common handlers are:</span></span>

* <span data-ttu-id="bc6db-171">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-171">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="bc6db-172">Amostra de [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="bc6db-172">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="bc6db-173">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="bc6db-173">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="bc6db-174">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="bc6db-174">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="bc6db-175">O código `OnPostAsync` no exemplo anterior tem aparência semelhante ao que você normalmente escreve em um controlador.</span><span class="sxs-lookup"><span data-stu-id="bc6db-175">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="bc6db-176">O código anterior é comum para as Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-176">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="bc6db-177">A maioria dos primitivos MVC como [associação de modelos](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation) e resultados da ação são compartilhados.</span><span class="sxs-lookup"><span data-stu-id="bc6db-177">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="bc6db-178">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="bc6db-178">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="bc6db-179">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="bc6db-179">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="bc6db-180">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="bc6db-180">Check for validation errors.</span></span>

*  <span data-ttu-id="bc6db-181">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="bc6db-181">If there are no errors, save the data and redirect.</span></span>
*  <span data-ttu-id="bc6db-182">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="bc6db-182">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="bc6db-183">A validação do lado do cliente é idêntica para aplicativos ASP.NET Core MVC tradicionais.</span><span class="sxs-lookup"><span data-stu-id="bc6db-183">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="bc6db-184">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-184">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="bc6db-185">Quando os dados são inseridos com êxito, o método de manipulador `OnPostAsync` chama o método auxiliar `RedirectToPage` para retornar uma instância de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-185">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="bc6db-186">`RedirectToPage` é um novo resultado de ação, semelhante a `RedirectToAction` ou `RedirectToRoute`, mas personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="bc6db-186">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="bc6db-187">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="bc6db-187">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="bc6db-188">`RedirectToPage` é descrito em detalhes na seção [Geração de URLs para páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="bc6db-188">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="bc6db-189">Quando o formulário enviado tem erros de validação (que são passados para o servidor), o método de manipulador `OnPostAsync` chama o método auxiliar `Page`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-189">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="bc6db-190">`Page` retorna uma instância de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-190">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="bc6db-191">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-191">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="bc6db-192">`PageResult` é o tipo de retorno <!-- Review  --> padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="bc6db-192">`PageResult` is the default <!-- Review  --> return type for a handler method.</span></span> <span data-ttu-id="bc6db-193">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-193">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="bc6db-194">A propriedade `Customer` usa o atributo `[BindProperty]` para aceitar a associação de modelos.</span><span class="sxs-lookup"><span data-stu-id="bc6db-194">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="bc6db-195">Páginas do Razor, por padrão, associam as propriedades somente com verbos não GET.</span><span class="sxs-lookup"><span data-stu-id="bc6db-195">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="bc6db-196">A associação de propriedades pode reduzir a quantidade de código que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="bc6db-196">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="bc6db-197">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name" />`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="bc6db-197">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name" />`) and accept the input.</span></span>

> [!NOTE]
> <span data-ttu-id="bc6db-198">Por motivos de segurança, você deve optar por associar os dados da solicitação GET às propriedades do modelo de página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-198">For security reasons, you must opt in to binding GET request data to page model properties.</span></span> <span data-ttu-id="bc6db-199">Verifique a entrada do usuário antes de mapeá-la para as propriedades.</span><span class="sxs-lookup"><span data-stu-id="bc6db-199">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="bc6db-200">Aceitar esse comportamento é útil quando você lida com cenários que contam com a cadeia de caracteres de consulta ou com os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="bc6db-200">Opting in to this behavior is useful when addressing scenarios which rely on query string or route values.</span></span>
>
> <span data-ttu-id="bc6db-201">Para associar uma propriedade às solicitações GET, defina a propriedade `SupportsGet` do atributo `[BindProperty]` como `true`: `[BindProperty(SupportsGet = true)]`</span><span class="sxs-lookup"><span data-stu-id="bc6db-201">To bind a property on GET requests, set the `[BindProperty]` attribute's `SupportsGet` property to `true`: `[BindProperty(SupportsGet = true)]`</span></span>

<span data-ttu-id="bc6db-202">A home page (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="bc6db-202">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="bc6db-203">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="bc6db-203">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="bc6db-204">O arquivo *cshtml* contém a marcação a seguir para criar um link de edição para cada contato:</span><span class="sxs-lookup"><span data-stu-id="bc6db-204">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="bc6db-205">O [auxiliar de marcas de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) usou o atributo `asp-route-{value}` para gerar um link para a página Edit.</span><span class="sxs-lookup"><span data-stu-id="bc6db-205">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="bc6db-206">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="bc6db-206">The link contains route data with the contact ID.</span></span> <span data-ttu-id="bc6db-207">Por exemplo, `http://localhost:5000/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-207">For example, `http://localhost:5000/Edit/1`.</span></span>

<span data-ttu-id="bc6db-208">O arquivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-208">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="bc6db-209">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-209">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="bc6db-210">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-210">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="bc6db-211">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o tempo de execução retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="bc6db-211">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="bc6db-212">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="bc6db-212">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="bc6db-213">O arquivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-213">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="bc6db-214">O arquivo *Index.cshtml* também contém a marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="bc6db-214">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="bc6db-215">Quando o botão de exclusão é renderizado em HTML, seu `formaction` inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="bc6db-215">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="bc6db-216">A ID de contato do cliente especificada pelo atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-216">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="bc6db-217">O `handler` especificado pelo atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-217">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="bc6db-218">Este é um exemplo de um botão de exclusão renderizado com uma ID de contato do cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="bc6db-218">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="bc6db-219">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-219">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="bc6db-220">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-220">By convention, the name of the handler method is selected based the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="bc6db-221">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-221">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="bc6db-222">Se o `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador de página com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="bc6db-222">If the `asp-page-handler` is set to a different value, such as `remove`, a page handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="bc6db-223">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="bc6db-223">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="bc6db-224">Aceita o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="bc6db-224">Accepts the `id` from the query string.</span></span>
* <span data-ttu-id="bc6db-225">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-225">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="bc6db-226">Se o contato do cliente for encontrado, eles serão removidos da lista de contatos do cliente.</span><span class="sxs-lookup"><span data-stu-id="bc6db-226">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="bc6db-227">O banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="bc6db-227">The database is updated.</span></span>
* <span data-ttu-id="bc6db-228">Chama `RedirectToPage` para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="bc6db-228">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-required"></a><span data-ttu-id="bc6db-229">Propriedades de página de marca necessárias</span><span class="sxs-lookup"><span data-stu-id="bc6db-229">Mark page properties required</span></span>

<span data-ttu-id="bc6db-230">As propriedades em um `PageModel` podem ser decoradas com o atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="bc6db-230">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

<span data-ttu-id="bc6db-231">[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]</span><span class="sxs-lookup"><span data-stu-id="bc6db-231">[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]</span></span>

<span data-ttu-id="bc6db-232">Confira [Validação de modelo](xref:mvc/models/validation) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bc6db-232">See [Model validation](xref:mvc/models/validation) for more information.</span></span>

## <a name="manage-head-requests-with-the-onget-handler"></a><span data-ttu-id="bc6db-233">Gerenciar solicitações HEAD com o manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="bc6db-233">Manage HEAD requests with the OnGet handler</span></span>

<span data-ttu-id="bc6db-234">Geralmente, um manipulador HEAD é criado e chamado para solicitações HEAD:</span><span class="sxs-lookup"><span data-stu-id="bc6db-234">Ordinarily, a HEAD handler is created and called for HEAD requests:</span></span>

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="bc6db-235">Caso nenhum manipulador HEAD (`OnHead`) seja definido, as Páginas Razor voltam a chamar o manipulador de página GET (`OnGet`) no ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="bc6db-235">If no HEAD handler (`OnHead`) is defined, Razor Pages falls back to calling the GET page handler (`OnGet`) in ASP.NET Core 2.1 or later.</span></span> <span data-ttu-id="bc6db-236">Aceite o seguinte comportamento com o [método SetCompatibilityVersion](xref:fundamentals/startup#setcompatibilityversion-for-aspnet-core-mvc), no `Startup.Configure` para ASP.NET Core 2.1 a 2.x:</span><span class="sxs-lookup"><span data-stu-id="bc6db-236">Opt in to this behavior with the [SetCompatibilityVersion method](xref:fundamentals/startup#setcompatibilityversion-for-aspnet-core-mvc) in `Startup.Configure` for ASP.NET Core 2.1 to 2.x:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="bc6db-237">`SetCompatibilityVersion` define de forma eficiente a opção de Páginas Razor `AllowMappingHeadRequestsToGetHandler` como `true`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-237">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="bc6db-238">Em vez de aceitar todos os 2.1 comportamentos com `SetCompatibilityVersion`, você pode explicitamente participar de comportamentos específicos.</span><span class="sxs-lookup"><span data-stu-id="bc6db-238">Rather than opting into all 2.1 behaviors with `SetCompatibilityVersion`, you can explicitly opt-in to specific behaviors.</span></span> <span data-ttu-id="bc6db-239">O código a seguir aceita as solicitações de mapeamento HEAD para o manipulador GET.</span><span class="sxs-lookup"><span data-stu-id="bc6db-239">The following code opts into the mapping HEAD requests to the GET handler.</span></span>


```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```
::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="bc6db-240">XSRF/CSRF e Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-240">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="bc6db-241">Você não precisa escrever nenhum código para [validação antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="bc6db-241">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="bc6db-242">Validação e geração de token antifalsificação são automaticamente incluídas nas Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-242">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="bc6db-243">Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-243">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="bc6db-244">As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-244">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="bc6db-245">Layouts, parciais, modelos, auxiliares de marcas, *_ViewStart.cshtml* e *_ViewImports.cshtml* funcionam da mesma forma que funcionam exibições convencionais do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc6db-245">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="bc6db-246">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="bc6db-246">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="bc6db-247">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-247">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="bc6db-248">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="bc6db-248">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="bc6db-249">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="bc6db-249">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="bc6db-250">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="bc6db-250">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="bc6db-251">Veja [página de layout](xref:mvc/views/layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bc6db-251">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="bc6db-252">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-252">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bc6db-253">O layout está na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-253">The layout is in the *Pages* folder.</span></span> <span data-ttu-id="bc6db-254">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="bc6db-254">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="bc6db-255">Um layout na pasta *Pages* pode ser usado em qualquer Página do Razor na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-255">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="bc6db-256">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-256">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="bc6db-257">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="bc6db-257">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="bc6db-258">As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="bc6db-258">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="bc6db-259">A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-259">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="bc6db-260">Os layouts, modelos e parciais que você está usando com controladores MVC e exibições do Razor convencionais *apenas funcionam*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-260">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="bc6db-261">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-261">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="bc6db-262">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="bc6db-262">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="bc6db-263">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-263">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="bc6db-264">Quando a diretiva `@namespace` é usada explicitamente em uma página:</span><span class="sxs-lookup"><span data-stu-id="bc6db-264">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="bc6db-265">A diretiva define o namespace da página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-265">The directive sets the namespace for the page.</span></span> <span data-ttu-id="bc6db-266">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="bc6db-266">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="bc6db-267">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-267">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="bc6db-268">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-268">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="bc6db-269">Por exemplo, a classe `PageModel` *Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="bc6db-269">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="bc6db-270">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="bc6db-270">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="bc6db-271">O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-271">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="bc6db-272">`@namespace` *também funciona com exibições do Razor convencionais.*</span><span class="sxs-lookup"><span data-stu-id="bc6db-272">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="bc6db-273">O arquivo de exibição *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="bc6db-273">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="bc6db-274">O arquivo de exibição *Pages/Create.cshtml* atualizado:</span><span class="sxs-lookup"><span data-stu-id="bc6db-274">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="bc6db-275">O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="bc6db-275">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="bc6db-276">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="bc6db-276">URL generation for Pages</span></span>

<span data-ttu-id="bc6db-277">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="bc6db-277">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="bc6db-278">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="bc6db-278">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="bc6db-279">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="bc6db-279">*/Pages*</span></span>

  * <span data-ttu-id="bc6db-280">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bc6db-280">*Index.cshtml*</span></span>
  * <span data-ttu-id="bc6db-281">*/Clientes*</span><span class="sxs-lookup"><span data-stu-id="bc6db-281">*/Customers*</span></span>

    * <span data-ttu-id="bc6db-282">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bc6db-282">*Create.cshtml*</span></span>
    * <span data-ttu-id="bc6db-283">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bc6db-283">*Edit.cshtml*</span></span>
    * <span data-ttu-id="bc6db-284">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bc6db-284">*Index.cshtml*</span></span>

<span data-ttu-id="bc6db-285">As páginas *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* redirecionam para o *Pages/Index.cshtml* após êxito.</span><span class="sxs-lookup"><span data-stu-id="bc6db-285">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="bc6db-286">A cadeia de caracteres `/Index` faz parte do URI para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="bc6db-286">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="bc6db-287">A cadeia de caracteres `/Index` pode ser usada para gerar URIs para a página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-287">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="bc6db-288">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bc6db-288">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="bc6db-289">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="bc6db-289">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="bc6db-290">Os exemplos anteriores de geração de URL oferecem opções avançadas e recursos funcionais para codificar uma URL.</span><span class="sxs-lookup"><span data-stu-id="bc6db-290">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="bc6db-291">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="bc6db-291">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="bc6db-292">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="bc6db-292">URL generation for pages supports relative names.</span></span> <span data-ttu-id="bc6db-293">A tabela a seguir mostra qual página de Índice é selecionada com diferentes parâmetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bc6db-293">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="bc6db-294">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="bc6db-294">RedirectToPage(x)</span></span>| <span data-ttu-id="bc6db-295">Página</span><span class="sxs-lookup"><span data-stu-id="bc6db-295">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="bc6db-296">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="bc6db-296">RedirectToPage("/Index")</span></span> | <span data-ttu-id="bc6db-297">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="bc6db-297">*Pages/Index*</span></span> |
| <span data-ttu-id="bc6db-298">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="bc6db-298">RedirectToPage("./Index");</span></span> | <span data-ttu-id="bc6db-299">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="bc6db-299">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="bc6db-300">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="bc6db-300">RedirectToPage("../Index")</span></span> | <span data-ttu-id="bc6db-301">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="bc6db-301">*Pages/Index*</span></span> |
| <span data-ttu-id="bc6db-302">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="bc6db-302">RedirectToPage("Index")</span></span>  | <span data-ttu-id="bc6db-303">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="bc6db-303">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="bc6db-304">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são <em>nomes relativos</em>.</span><span class="sxs-lookup"><span data-stu-id="bc6db-304">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are <em>relative names</em>.</span></span> <span data-ttu-id="bc6db-305">O parâmetro `RedirectToPage` é <em>combinado</em> com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="bc6db-305">The `RedirectToPage` parameter is <em>combined</em> with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="bc6db-306">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="bc6db-306">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="bc6db-307">Se você usar nomes relativos para vincular entre páginas em uma pasta, você poderá renomear essa pasta.</span><span class="sxs-lookup"><span data-stu-id="bc6db-307">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="bc6db-308">Todos os links ainda funcionarão (porque eles não incluirão o nome da pasta).</span><span class="sxs-lookup"><span data-stu-id="bc6db-308">All the links still work (because they didn't include the folder name).</span></span>

::: moniker range=">= aspnetcore-2.1"
## <a name="viewdata-attribute"></a><span data-ttu-id="bc6db-309">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="bc6db-309">ViewData attribute</span></span>

<span data-ttu-id="bc6db-310">Os dados podem ser passados para uma página com [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="bc6db-310">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="bc6db-311">As propriedades nos controladores ou nos modelos da Página Razor decoradas com `[ViewData]` têm seus valores armazenados e carregados em [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="bc6db-311">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="bc6db-312">No exemplo a seguir, o `AboutModel` contém uma propriedade `Title` decorada com `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-312">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="bc6db-313">A propriedade `Title` está definida como o título da página Sobre:</span><span class="sxs-lookup"><span data-stu-id="bc6db-313">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="bc6db-314">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="bc6db-314">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="bc6db-315">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="bc6db-315">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```
::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="bc6db-316">TempData</span><span class="sxs-lookup"><span data-stu-id="bc6db-316">TempData</span></span>

<span data-ttu-id="bc6db-317">O ASP.NET Core expõe a propriedade [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) em um [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="bc6db-317">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="bc6db-318">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="bc6db-318">This property stores data until it's read.</span></span> <span data-ttu-id="bc6db-319">Os métodos `Keep` e `Peek` podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="bc6db-319">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="bc6db-320">`TempData` é útil para redirecionamento nos casos em que os dados são necessários para mais de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="bc6db-320">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="bc6db-321">O atributo `[TempData]` é novo no ASP.NET Core 2.0 e tem suporte em controladores e páginas.</span><span class="sxs-lookup"><span data-stu-id="bc6db-321">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="bc6db-322">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="bc6db-322">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="bc6db-323">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-323">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="bc6db-324">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-324">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="bc6db-325">Consulte [TempData](xref:fundamentals/app-state#tempdata) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="bc6db-325">See [TempData](xref:fundamentals/app-state#tempdata) for more information.</span></span>

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a><span data-ttu-id="bc6db-326">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="bc6db-326">Multiple handlers per page</span></span>

<span data-ttu-id="bc6db-327">A página a seguir gera marcação para dois manipuladores de página usando o auxiliar de marcas `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="bc6db-327">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="bc6db-328">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="bc6db-328">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="bc6db-329">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-329">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="bc6db-330">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-330">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="bc6db-331">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="bc6db-331">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="bc6db-332">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="bc6db-332">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="bc6db-333">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-333">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="bc6db-334">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="bc6db-334">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="bc6db-335">No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="bc6db-335">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="bc6db-336">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-336">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="bc6db-337">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-337">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="bc6db-338">O caminho da URL que envia a `OnPostJoinListUCAsync` é `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-338">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="bc6db-339">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="bc6db-339">Custom routes</span></span>

<span data-ttu-id="bc6db-340">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="bc6db-340">Use the `@page` directive to:</span></span>

* <span data-ttu-id="bc6db-341">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-341">Specify a custom route to a page.</span></span> <span data-ttu-id="bc6db-342">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-342">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="bc6db-343">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-343">Append segments to a page's default route.</span></span> <span data-ttu-id="bc6db-344">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-344">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="bc6db-345">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="bc6db-345">Append parameters to a page's default route.</span></span> <span data-ttu-id="bc6db-346">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-346">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="bc6db-347">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="bc6db-347">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="bc6db-348">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-348">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="bc6db-349">Você pode alterar a cadeia de caracteres de consulta `?handler=JoinList` na URL para um segmento de rota `/JoinList` ao especificar o modelo de rota `@page "{handler?}"`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-349">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="bc6db-350">Se você não deseja a cadeia de consulta `?handler=JoinList` na URL, você pode alterar a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="bc6db-350">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="bc6db-351">Você pode personalizar a rota adicionando um modelo de rota entre aspas duplas após a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-351">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="bc6db-352">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `http://localhost:5000/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-352">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="bc6db-353">O caminho da URL que envia a `OnPostJoinListUCAsync` é `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="bc6db-353">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="bc6db-354">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="bc6db-354">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="bc6db-355">Configuração e definições</span><span class="sxs-lookup"><span data-stu-id="bc6db-355">Configuration and settings</span></span>

<span data-ttu-id="bc6db-356">Para configurar opções avançadas, use o método de extensão `AddRazorPagesOptions` no construtor de MVC:</span><span class="sxs-lookup"><span data-stu-id="bc6db-356">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="bc6db-357">No momento, você pode usar o `RazorPagesOptions` para definir o diretório raiz para páginas ou adicionar as convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="bc6db-357">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="bc6db-358">Permitiremos mais extensibilidade dessa maneira no futuro.</span><span class="sxs-lookup"><span data-stu-id="bc6db-358">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="bc6db-359">Para pré-compilar exibições, consulte [Compilação de exibição do Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="bc6db-359">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="bc6db-360">[Baixar ou exibir código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="bc6db-360">[Download or view sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="bc6db-361">Consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="bc6db-361">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="bc6db-362">Especificar que as Páginas Razor estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="bc6db-362">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="bc6db-363">Por padrão, as Páginas Razor estão na raiz do diretório */Pages*.</span><span class="sxs-lookup"><span data-stu-id="bc6db-363">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="bc6db-364">Adicione [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) em [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que as Páginas Razor estão na raiz do conteúdo ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="bc6db-364">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="bc6db-365">Especificar que as Páginas Razor estão em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="bc6db-365">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="bc6db-366">Adicione [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) em [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que as Páginas Razor estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="bc6db-366">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="see-also"></a><span data-ttu-id="bc6db-367">Consulte também</span><span class="sxs-lookup"><span data-stu-id="bc6db-367">See also</span></span>

* [<span data-ttu-id="bc6db-368">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc6db-368">Introduction to ASP.NET Core</span></span>](xref:index)
* [<span data-ttu-id="bc6db-369">Sintaxe Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-369">Razor syntax</span></span>](xref:mvc/views/razor)
* [<span data-ttu-id="bc6db-370">Introdução a Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-370">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="bc6db-371">Convenções de autorização de Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-371">Razor Pages authorization conventions</span></span>](xref:security/authorization/razor-pages-authorization)
* [<span data-ttu-id="bc6db-372">Provedores de modelo personalizado de página e rota de Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-372">Razor Pages custom route and page model providers</span></span>](xref:razor-pages/razor-pages-conventions)
* [<span data-ttu-id="bc6db-373">Testes de unidades de páginas Razor</span><span class="sxs-lookup"><span data-stu-id="bc6db-373">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
