---
title: 'Tutorial: introdução às Razor páginas no ASP.NET Core'
author: rick-anderson
description: Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.
ms.author: riande
ms.date: 09/15/2020
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4b8bd9c886e615add6b0d3e372843a8ddb33ae18
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420039"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="4e0c7-103">Tutorial: introdução às Razor páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e0c7-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="4e0c7-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4e0c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="4e0c7-105">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="4e0c7-106">Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às Razor páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="4e0c7-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="4e0c7-108">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4e0c7-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4e0c7-110">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4e0c7-111">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-111">Run the app.</span></span>
> * <span data-ttu-id="4e0c7-112">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-112">Examine the project files.</span></span>

<span data-ttu-id="4e0c7-113">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho que será aprimorado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="4e0c7-115">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4e0c7-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e0c7-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e0c7-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4e0c7-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e0c7-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4e0c7-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4e0c7-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="4e0c7-119">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e0c7-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e0c7-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4e0c7-121">Inicie o Visual Studio e selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="4e0c7-122">Para obter mais informações, consulte [criar um novo projeto no Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![Criar um novo projeto a partir da janela inicial](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="4e0c7-124">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core** e depois selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="4e0c7-126">Na caixa de diálogo **configurar seu novo projeto** , digite `RazorPagesMovie` para **nome do projeto**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="4e0c7-127">É importante nomear o projeto *Razor PagesMovie*, incluindo a correspondência de maiúsculas e minúsculas, de modo que os namespaces corresponderão quando você copiar e colar o código de exemplo.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="4e0c7-128">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-128">Select **Create**.</span></span>

    ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

1. <span data-ttu-id="4e0c7-130">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="4e0c7-131">**.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="4e0c7-132">**Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-132">**Web Application**.</span></span>
    1. <span data-ttu-id="4e0c7-133">**Criar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-133">**Create**.</span></span>

     ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="4e0c7-135">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-135">The following starter project is created:</span></span>

    ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4e0c7-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e0c7-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4e0c7-138">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="4e0c7-139">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="4e0c7-140">Execute os comandos a seguir:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="4e0c7-141">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="4e0c7-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="4e0c7-142">O `code` comando abre a pasta *Razor PagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4e0c7-143">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4e0c7-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4e0c7-144">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-144">Select **File** > **New Solution**.</span></span>

    ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="4e0c7-146">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="4e0c7-147">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="4e0c7-149">Na caixa de diálogo **Configurar o novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="4e0c7-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="4e0c7-150">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="4e0c7-151">Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão mais recente do .NET 5. x.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="4e0c7-152">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-152">Select **Next**.</span></span>

1. <span data-ttu-id="4e0c7-153">Nomeie o projeto *Razor PagesMovie* e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![nome do macOS o projeto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4e0c7-155">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="4e0c7-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="4e0c7-156">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="4e0c7-156">Examine the project files</span></span>

<span data-ttu-id="4e0c7-157">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4e0c7-158">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-158">Pages folder</span></span>

<span data-ttu-id="4e0c7-159">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4e0c7-160">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4e0c7-161">Um arquivo *. cshtml* que tem marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4e0c7-162">Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="4e0c7-163">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4e0c7-164">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4e0c7-165">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4e0c7-166">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4e0c7-167">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="4e0c7-167">wwwroot folder</span></span>

<span data-ttu-id="4e0c7-168">Contém ativos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4e0c7-169">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="4e0c7-170">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="4e0c7-171">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4e0c7-172">Module.vb</span><span class="sxs-lookup"><span data-stu-id="4e0c7-172">Program.cs</span></span>

<span data-ttu-id="4e0c7-173">Contém o ponto de entrada para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-173">Contains the entry point for the app.</span></span> <span data-ttu-id="4e0c7-174">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4e0c7-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4e0c7-175">Startup.cs</span></span>

<span data-ttu-id="4e0c7-176">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="4e0c7-177">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4e0c7-178">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4e0c7-179">Em seguida: adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="4e0c7-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4e0c7-180">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="4e0c7-181">Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às Razor páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="4e0c7-182">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="4e0c7-183">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4e0c7-184">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4e0c7-185">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4e0c7-186">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-186">Run the app.</span></span>
> * <span data-ttu-id="4e0c7-187">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-187">Examine the project files.</span></span>

<span data-ttu-id="4e0c7-188">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="4e0c7-190">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4e0c7-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e0c7-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e0c7-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4e0c7-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e0c7-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4e0c7-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4e0c7-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="4e0c7-194">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e0c7-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e0c7-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e0c7-196">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4e0c7-197">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="4e0c7-198">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="4e0c7-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="4e0c7-199">Nomeie o projeto **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="4e0c7-200">É importante nomear o projeto *Razor PagesMovie* para que os namespaces correspondam quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="4e0c7-201">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="4e0c7-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="4e0c7-202">Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web** e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="4e0c7-204">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-204">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4e0c7-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e0c7-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4e0c7-207">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4e0c7-208">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4e0c7-209">Execute os comandos a seguir:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="4e0c7-210">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="4e0c7-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="4e0c7-211">O `code` comando abre a pasta *Razor PagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4e0c7-212">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' Razor PagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="4e0c7-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="4e0c7-213">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-213">Select **Yes**.</span></span>

  <span data-ttu-id="4e0c7-214">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4e0c7-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4e0c7-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4e0c7-216">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-216">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4e0c7-218">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="4e0c7-219">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="4e0c7-221">Na caixa de diálogo **Configurar o novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="4e0c7-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="4e0c7-222">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="4e0c7-223">Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="4e0c7-224">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-224">Select **Next**.</span></span>

* <span data-ttu-id="4e0c7-225">Nomeie o projeto **Razor PagesMovie** e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nome do macOS o projeto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4e0c7-227">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="4e0c7-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="4e0c7-228">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="4e0c7-228">Examine the project files</span></span>

<span data-ttu-id="4e0c7-229">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4e0c7-230">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-230">Pages folder</span></span>

<span data-ttu-id="4e0c7-231">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4e0c7-232">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4e0c7-233">Um arquivo *. cshtml* que tem marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4e0c7-234">Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="4e0c7-235">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4e0c7-236">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4e0c7-237">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4e0c7-238">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4e0c7-239">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="4e0c7-239">wwwroot folder</span></span>

<span data-ttu-id="4e0c7-240">Contém arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4e0c7-241">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4e0c7-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4e0c7-242">appSettings.json</span></span>

<span data-ttu-id="4e0c7-243">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="4e0c7-244">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4e0c7-245">Module.vb</span><span class="sxs-lookup"><span data-stu-id="4e0c7-245">Program.cs</span></span>

<span data-ttu-id="4e0c7-246">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-246">Contains the entry point for the program.</span></span> <span data-ttu-id="4e0c7-247">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4e0c7-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4e0c7-248">Startup.cs</span></span>

<span data-ttu-id="4e0c7-249">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="4e0c7-250">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4e0c7-251">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4e0c7-252">Em seguida: adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="4e0c7-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e0c7-253">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="4e0c7-254">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas da criação de um Razor aplicativo Web de ASP.NET Core páginas.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="4e0c7-255">Para obter uma introdução mais avançada voltada para os desenvolvedores que estão familiarizados com os controladores e exibições, consulte [introdução às Razor páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="4e0c7-256">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="4e0c7-257">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4e0c7-258">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4e0c7-259">Crie um Razor aplicativo Web de páginas.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4e0c7-260">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-260">Run the app.</span></span>
> * <span data-ttu-id="4e0c7-261">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-261">Examine the project files.</span></span>

<span data-ttu-id="4e0c7-262">No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="4e0c7-264">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4e0c7-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e0c7-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e0c7-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4e0c7-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e0c7-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4e0c7-267">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4e0c7-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="4e0c7-268">Criar um Razor aplicativo Web de páginas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e0c7-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e0c7-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e0c7-270">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="4e0c7-271">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="4e0c7-273">Nomeie o projeto **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="4e0c7-274">É importante nomear o projeto *Razor PagesMovie* para que os namespaces correspondam quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="4e0c7-276">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="4e0c7-278">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-278">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4e0c7-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e0c7-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4e0c7-281">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4e0c7-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4e0c7-282">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4e0c7-283">Execute os comandos a seguir:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="4e0c7-284">O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="4e0c7-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="4e0c7-285">O `code` comando abre a pasta *Razor PagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4e0c7-286">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' Razor PagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="4e0c7-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="4e0c7-287">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-287">Select **Yes**.</span></span>

  <span data-ttu-id="4e0c7-288">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4e0c7-289">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4e0c7-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4e0c7-290">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-290">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4e0c7-292">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="4e0c7-293">Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="4e0c7-294">Na caixa de diálogo **Configurar o novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="4e0c7-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="4e0c7-295">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="4e0c7-296">Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="4e0c7-297">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-297">Select **Next**.</span></span>

* <span data-ttu-id="4e0c7-298">Nomeie o projeto **Razor PagesMovie** e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4e0c7-300">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="4e0c7-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e0c7-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e0c7-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e0c7-302">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="4e0c7-303">Iniciar o aplicativo com <kbd>Ctrl + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4e0c7-304">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="4e0c7-305">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4e0c7-306">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4e0c7-307">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="4e0c7-308">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="4e0c7-309">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="4e0c7-310">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4e0c7-311">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4e0c7-313">A imagem a seguir mostra o aplicativo após o consentimento para acompanhamento é fornecido:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="4e0c7-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e0c7-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="4e0c7-316">Pressione <kbd>Ctrl + F5</kbd> para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="4e0c7-317">Iniciar o aplicativo com <kbd>Ctrl + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4e0c7-318">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="4e0c7-319">Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e vai para `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="4e0c7-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="4e0c7-320">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4e0c7-321">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="4e0c7-322">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="4e0c7-323">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4e0c7-324">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4e0c7-326">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4e0c7-328">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4e0c7-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4e0c7-329">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4e0c7-330">Iniciar o aplicativo com <kbd>cmd + opt + F5</kbd> (modo não depurar) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4e0c7-331">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="4e0c7-332">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e vai para `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="4e0c7-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="4e0c7-333">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4e0c7-334">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="4e0c7-336">A imagem a seguir mostra o aplicativo após o consentimento para acompanhamento é fornecido:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home ou::: no-Loc (index)::: Page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="4e0c7-338">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="4e0c7-338">Examine the project files</span></span>

<span data-ttu-id="4e0c7-339">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4e0c7-340">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-340">Pages folder</span></span>

<span data-ttu-id="4e0c7-341">Contém Razor páginas e arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4e0c7-342">Cada Razor página é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="4e0c7-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4e0c7-343">Um arquivo *. cshtml* que tem marcação HTML com código C# usando a Razor sintaxe.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4e0c7-344">Um arquivo *. cshtml.cs* que tem código C# que manipula eventos de página.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="4e0c7-345">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4e0c7-346">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4e0c7-347">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4e0c7-348">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="4e0c7-349">Razor As páginas são derivadas de `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="4e0c7-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="4e0c7-350">Por convenção, a `PageModel` classe derivada é nomeada `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="4e0c7-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4e0c7-351">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="4e0c7-351">wwwroot folder</span></span>

<span data-ttu-id="4e0c7-352">Contém arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4e0c7-353">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4e0c7-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4e0c7-354">appSettings.json</span></span>

<span data-ttu-id="4e0c7-355">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="4e0c7-356">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4e0c7-357">Module.vb</span><span class="sxs-lookup"><span data-stu-id="4e0c7-357">Program.cs</span></span>

<span data-ttu-id="4e0c7-358">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-358">Contains the entry point for the program.</span></span> <span data-ttu-id="4e0c7-359">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4e0c7-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4e0c7-360">Startup.cs</span></span>

<span data-ttu-id="4e0c7-361">Contém o código que configura o comportamento do aplicativo, como se ele requer consentimento para cookie s.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="4e0c7-362">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4e0c7-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e0c7-363">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4e0c7-363">Additional resources</span></span>

* [<span data-ttu-id="4e0c7-364">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="4e0c7-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="4e0c7-365">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="4e0c7-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4e0c7-366">Em seguida: adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="4e0c7-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
