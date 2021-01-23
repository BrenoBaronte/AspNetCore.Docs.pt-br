---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710747"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="0e19c-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0e19c-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="0e19c-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0e19c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="0e19c-105">Este é o primeiro tutorial de uma série que ensina ASP.NET Core desenvolvimento para a Web MVC com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="0e19c-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="0e19c-106">No final da série, você terá um aplicativo que gerencia e exibe dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="0e19c-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="0e19c-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="0e19c-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0e19c-108">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="0e19c-108">Create a web app.</span></span>
> * <span data-ttu-id="0e19c-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="0e19c-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0e19c-110">Work with a database.</span></span>
> * <span data-ttu-id="0e19c-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="0e19c-111">Add search and validation.</span></span>

<span data-ttu-id="0e19c-112">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0e19c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0e19c-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0e19c-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e19c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e19c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e19c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e19c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0e19c-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e19c-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="0e19c-117">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="0e19c-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e19c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e19c-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e19c-119">Inicie o Visual Studio e selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="0e19c-120">Na caixa de diálogo **criar um novo projeto** , selecione **ASP.NET Core aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="0e19c-121">Na caixa de diálogo **configurar seu novo projeto** , digite `MvcMovie` para **nome do projeto**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="0e19c-122">É importante nomear o projeto *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="0e19c-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="0e19c-123">A capitalização precisa corresponder a cada `namespace` correspondência quando o código é copiado.</span><span class="sxs-lookup"><span data-stu-id="0e19c-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="0e19c-124">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-124">Select **Create**.</span></span>
* <span data-ttu-id="0e19c-125">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione:</span><span class="sxs-lookup"><span data-stu-id="0e19c-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="0e19c-126">**.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="0e19c-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="0e19c-127">**ASP.NET Core aplicativo Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="0e19c-128">**Criar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-128">**Create**.</span></span>

![<span data-ttu-id="0e19c-129">Criar um novo aplicativo Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e19c-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="0e19c-130">Para obter abordagens alternativas para criar o projeto, consulte [criar um novo projeto no Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="0e19c-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="0e19c-131">O Visual Studio usou o modelo de projeto padrão para o projeto MVC criado.</span><span class="sxs-lookup"><span data-stu-id="0e19c-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="0e19c-132">O projeto criado:</span><span class="sxs-lookup"><span data-stu-id="0e19c-132">The created project:</span></span>

* <span data-ttu-id="0e19c-133">É um aplicativo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="0e19c-133">Is a working app.</span></span>
* <span data-ttu-id="0e19c-134">É um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="0e19c-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e19c-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e19c-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e19c-136">O tutorial pressupõe familiaridade com VS Code.</span><span class="sxs-lookup"><span data-stu-id="0e19c-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="0e19c-137">Para obter mais informações, consulte [introdução ao vs Code](https://code.visualstudio.com/docs) e [Visual Studio Code ajuda](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="0e19c-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="0e19c-138">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0e19c-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0e19c-139">Altere para o diretório ( `cd` ) que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="0e19c-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="0e19c-140">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="0e19c-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="0e19c-141">Se uma caixa de diálogo aparecer com **ativos necessários para compilação e depuração estiverem ausentes em ' MvcMovie '. Adicioná-los?**, selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="0e19c-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="0e19c-142">`dotnet new mvc -o MvcMovie`: Cria um novo projeto ASP.NET Core MVC na pasta *MvcMovie*</span><span class="sxs-lookup"><span data-stu-id="0e19c-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="0e19c-143">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0e19c-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0e19c-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e19c-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0e19c-145">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-145">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="0e19c-147">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >    >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="0e19c-148">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >    >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="0e19c-150">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="0e19c-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="0e19c-151">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="0e19c-152">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 5. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="0e19c-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="0e19c-153">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-153">Select **Next**.</span></span>

* <span data-ttu-id="0e19c-154">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="0e19c-156">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e19c-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e19c-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e19c-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e19c-158">Selecione CTRL + F5 para executar o aplicativo sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="0e19c-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0e19c-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0e19c-159">Visual Studio:</span></span>

  * <span data-ttu-id="0e19c-160">Inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="0e19c-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="0e19c-161">Executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-161">Runs the app.</span></span>

  <span data-ttu-id="0e19c-162">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e19c-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0e19c-163">O nome do host padrão para seu computador local é `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0e19c-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="0e19c-164">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="0e19c-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="0e19c-165">Iniciar o aplicativo sem depuração selecionando CTRL + F5 permite que você:</span><span class="sxs-lookup"><span data-stu-id="0e19c-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="0e19c-166">Realize alterações de código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-166">Make code changes.</span></span>
* <span data-ttu-id="0e19c-167">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-167">Save the file.</span></span>
* <span data-ttu-id="0e19c-168">Atualize rapidamente o navegador e veja as alterações no código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="0e19c-169">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="0e19c-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Depurar](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="0e19c-171">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0e19c-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="0e19c-173">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0e19c-173">The following image shows the app:</span></span>

![Página Inicial ou de Índice](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e19c-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e19c-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e19c-176">Selecione CTRL + F5 para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="0e19c-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="0e19c-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="0e19c-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="0e19c-178">Inicia o [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="0e19c-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="0e19c-179">Inicia um navegador.</span><span class="sxs-lookup"><span data-stu-id="0e19c-179">Launches a browser.</span></span>
  * <span data-ttu-id="0e19c-180">Navega para `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="0e19c-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="0e19c-181">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e19c-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="0e19c-182">O nome do host padrão para seu computador local é `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0e19c-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="0e19c-183">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e19c-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="0e19c-184">Iniciar o aplicativo sem depuração selecionando CTRL + F5 permite que você:</span><span class="sxs-lookup"><span data-stu-id="0e19c-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="0e19c-185">Realize alterações de código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-185">Make code changes.</span></span>
* <span data-ttu-id="0e19c-186">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-186">Save the file.</span></span>
* <span data-ttu-id="0e19c-187">Atualize rapidamente o navegador e veja as alterações no código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0e19c-189">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e19c-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0e19c-190">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="0e19c-191">Visual Studio para Mac:</span><span class="sxs-lookup"><span data-stu-id="0e19c-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="0e19c-192">Inicia o servidor do [Kestrel](xref:fundamentals/servers/index#kestrel) .</span><span class="sxs-lookup"><span data-stu-id="0e19c-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="0e19c-193">Inicia um navegador.</span><span class="sxs-lookup"><span data-stu-id="0e19c-193">Launches a browser.</span></span>
  * <span data-ttu-id="0e19c-194">Navega para `http://localhost:port` , em que *porta* é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="0e19c-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="0e19c-195">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e19c-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0e19c-196">O nome do host padrão para seu computador local é `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0e19c-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="0e19c-197">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="0e19c-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="0e19c-198">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="0e19c-199">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0e19c-199">The following image shows the app:</span></span>

![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="0e19c-201">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0e19c-202">Em seguida: adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="0e19c-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="0e19c-203">Este é o primeiro tutorial de uma série que ensina ASP.NET Core desenvolvimento para a Web MVC com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="0e19c-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="0e19c-204">No final da série, você terá um aplicativo que gerencia e exibe dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="0e19c-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="0e19c-205">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="0e19c-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0e19c-206">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="0e19c-206">Create a web app.</span></span>
> * <span data-ttu-id="0e19c-207">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="0e19c-208">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0e19c-208">Work with a database.</span></span>
> * <span data-ttu-id="0e19c-209">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="0e19c-209">Add search and validation.</span></span>

<span data-ttu-id="0e19c-210">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0e19c-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0e19c-211">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0e19c-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e19c-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e19c-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e19c-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e19c-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0e19c-214">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e19c-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="0e19c-215">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="0e19c-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e19c-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e19c-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e19c-217">No Visual Studio, selecione **criar um novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="0e19c-218">Selecione **ASP.NET Core aplicativo Web** a > **seguir**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![Criar um novo projeto de aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="0e19c-220">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="0e19c-221">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Configurar seu novo projeto](start-mvc/_static/config.png)

* <span data-ttu-id="0e19c-223">Selecione **aplicativo Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="0e19c-224">Nas caixas suspensas, selecione **.NET Core** e **ASP.NET Core 3,1** e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="0e19c-225">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e19c-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="0e19c-226">O Visual Studio usou o modelo de projeto padrão para o projeto MVC criado.</span><span class="sxs-lookup"><span data-stu-id="0e19c-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="0e19c-227">O projeto criado:</span><span class="sxs-lookup"><span data-stu-id="0e19c-227">The created project:</span></span>

* <span data-ttu-id="0e19c-228">É um aplicativo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="0e19c-228">Is a working app.</span></span>
* <span data-ttu-id="0e19c-229">É um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="0e19c-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e19c-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e19c-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e19c-231">O tutorial pressupõe familiaridade com VS Code.</span><span class="sxs-lookup"><span data-stu-id="0e19c-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="0e19c-232">Para obter mais informações, consulte [introdução ao vs Code](https://code.visualstudio.com/docs) e [Visual Studio Code ajuda](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="0e19c-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="0e19c-233">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0e19c-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0e19c-234">Altere os diretórios ( `cd` ) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="0e19c-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="0e19c-235">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="0e19c-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="0e19c-236">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="0e19c-237">`dotnet new mvc -o MvcMovie`: Cria um novo projeto ASP.NET Core MVC na pasta *MvcMovie*</span><span class="sxs-lookup"><span data-stu-id="0e19c-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="0e19c-238">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0e19c-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0e19c-239">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e19c-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0e19c-240">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-240">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="0e19c-242">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >    >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="0e19c-243">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >    >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="0e19c-245">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="0e19c-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="0e19c-246">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="0e19c-247">Se for apresentada uma opção para selecionar uma **estrutura de destino** , selecione a versão 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="0e19c-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="0e19c-248">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-248">Select **Next**.</span></span>

* <span data-ttu-id="0e19c-249">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="0e19c-251">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e19c-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e19c-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e19c-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e19c-253">Selecione CTRL + F5 para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="0e19c-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0e19c-254">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0e19c-254">Visual Studio:</span></span>

  * <span data-ttu-id="0e19c-255">Inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="0e19c-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="0e19c-256">Executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-256">Runs the app.</span></span>

  <span data-ttu-id="0e19c-257">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e19c-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0e19c-258">O nome do host padrão para seu computador local é `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0e19c-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="0e19c-259">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="0e19c-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="0e19c-260">Iniciar o aplicativo sem depuração selecionando CTRL + F5 permite que você:</span><span class="sxs-lookup"><span data-stu-id="0e19c-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="0e19c-261">Realize alterações de código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-261">Make code changes.</span></span>
* <span data-ttu-id="0e19c-262">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-262">Save the file.</span></span>
* <span data-ttu-id="0e19c-263">Atualize rapidamente o navegador e veja as alterações no código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="0e19c-264">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="0e19c-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Depurar](start-mvc/_static/debug_menu.png)

<span data-ttu-id="0e19c-266">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0e19c-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="0e19c-268">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0e19c-268">The following image shows the app:</span></span>

![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e19c-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e19c-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e19c-271">Selecione CTRL + F5 para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="0e19c-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="0e19c-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="0e19c-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="0e19c-273">Inicia o [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="0e19c-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="0e19c-274">Inicia um navegador.</span><span class="sxs-lookup"><span data-stu-id="0e19c-274">Launches a browser.</span></span>
  * <span data-ttu-id="0e19c-275">Navega para `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="0e19c-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="0e19c-276">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e19c-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="0e19c-277">O nome do host padrão para seu computador local é `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0e19c-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="0e19c-278">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e19c-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="0e19c-279">Iniciar o aplicativo sem depuração selecionando CTRL + F5 permite que você:</span><span class="sxs-lookup"><span data-stu-id="0e19c-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="0e19c-280">Realize alterações de código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-280">Make code changes.</span></span>
* <span data-ttu-id="0e19c-281">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-281">Save the file.</span></span>
* <span data-ttu-id="0e19c-282">Atualize rapidamente o navegador e veja as alterações no código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0e19c-284">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e19c-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0e19c-285">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e19c-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="0e19c-286">Visual Studio para Mac: inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel) , inicia um navegador e navega para `http://localhost:port` , onde *porta* é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="0e19c-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="0e19c-287">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e19c-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0e19c-288">O nome do host padrão para seu computador local é `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0e19c-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="0e19c-289">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="0e19c-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="0e19c-290">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="0e19c-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="0e19c-291">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="0e19c-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="0e19c-292">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0e19c-292">The following image shows the app:</span></span>

![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="0e19c-294">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="0e19c-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0e19c-295">Próximo</span><span class="sxs-lookup"><span data-stu-id="0e19c-295">Next</span></span>](adding-controller.md)

::: moniker-end
