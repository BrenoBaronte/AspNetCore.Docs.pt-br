---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629984"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="48ba0-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="48ba0-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="48ba0-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48ba0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="48ba0-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="48ba0-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="48ba0-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="48ba0-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="48ba0-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="48ba0-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="48ba0-108">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="48ba0-108">Create a web app.</span></span>
> * <span data-ttu-id="48ba0-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="48ba0-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="48ba0-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="48ba0-110">Work with a database.</span></span>
> * <span data-ttu-id="48ba0-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="48ba0-111">Add search and validation.</span></span>

<span data-ttu-id="48ba0-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="48ba0-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="48ba0-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="48ba0-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48ba0-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ba0-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="48ba0-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ba0-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="48ba0-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="48ba0-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="48ba0-117">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="48ba0-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48ba0-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ba0-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48ba0-119">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="48ba0-120">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="48ba0-122">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="48ba0-123">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="48ba0-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="48ba0-125">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="48ba0-126">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48ba0-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="48ba0-127">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="48ba0-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="48ba0-128">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="48ba0-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="48ba0-129">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="48ba0-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="48ba0-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ba0-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48ba0-131">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="48ba0-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="48ba0-132">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="48ba0-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="48ba0-133">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="48ba0-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="48ba0-134">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="48ba0-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="48ba0-135">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="48ba0-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="48ba0-136">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="48ba0-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="48ba0-137">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="48ba0-137">Select **Yes**</span></span>

  * <span data-ttu-id="48ba0-138">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="48ba0-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="48ba0-139">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="48ba0-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="48ba0-140">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="48ba0-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="48ba0-141">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-141">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="48ba0-143">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="48ba0-144">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![seleção de modelo de aplicativo Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="48ba0-146">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="48ba0-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="48ba0-147">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="48ba0-148">Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="48ba0-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="48ba0-149">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-149">Select **Next**.</span></span>

* <span data-ttu-id="48ba0-150">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![nome do macOS o projeto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="48ba0-152">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="48ba0-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48ba0-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ba0-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48ba0-154">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="48ba0-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="48ba0-155">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="48ba0-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="48ba0-156">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48ba0-157">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="48ba0-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="48ba0-158">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="48ba0-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="48ba0-159">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="48ba0-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="48ba0-160">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="48ba0-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="48ba0-161">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="48ba0-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="48ba0-163">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="48ba0-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="48ba0-165">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="48ba0-165">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="48ba0-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ba0-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48ba0-168">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="48ba0-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="48ba0-169">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="48ba0-170">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="48ba0-171">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="48ba0-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="48ba0-172">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="48ba0-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="48ba0-173">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="48ba0-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="48ba0-174">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="48ba0-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="48ba0-176">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="48ba0-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="48ba0-177">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="48ba0-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="48ba0-178">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="48ba0-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="48ba0-179">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48ba0-180">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="48ba0-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="48ba0-181">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="48ba0-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="48ba0-182">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="48ba0-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="48ba0-183">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="48ba0-184">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="48ba0-184">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="48ba0-186">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="48ba0-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="48ba0-187">Próximo</span><span class="sxs-lookup"><span data-stu-id="48ba0-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="48ba0-188">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="48ba0-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="48ba0-189">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="48ba0-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="48ba0-190">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="48ba0-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="48ba0-191">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="48ba0-191">Create a web app.</span></span>
> * <span data-ttu-id="48ba0-192">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="48ba0-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="48ba0-193">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="48ba0-193">Work with a database.</span></span>
> * <span data-ttu-id="48ba0-194">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="48ba0-194">Add search and validation.</span></span>

<span data-ttu-id="48ba0-195">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="48ba0-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="48ba0-196">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="48ba0-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48ba0-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ba0-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="48ba0-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ba0-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="48ba0-199">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="48ba0-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="48ba0-200">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="48ba0-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48ba0-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ba0-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48ba0-202">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="48ba0-203">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="48ba0-205">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="48ba0-206">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="48ba0-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="48ba0-208">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="48ba0-209">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48ba0-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="48ba0-210">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="48ba0-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="48ba0-211">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="48ba0-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="48ba0-212">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="48ba0-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="48ba0-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ba0-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48ba0-214">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="48ba0-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="48ba0-215">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="48ba0-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="48ba0-216">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="48ba0-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="48ba0-217">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="48ba0-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="48ba0-218">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="48ba0-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="48ba0-219">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="48ba0-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="48ba0-220">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="48ba0-220">Select **Yes**</span></span>

  * <span data-ttu-id="48ba0-221">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="48ba0-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="48ba0-222">`code -r MvcMovie`: Carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="48ba0-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="48ba0-223">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="48ba0-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="48ba0-224">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-224">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="48ba0-226">Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **(Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="48ba0-227">Na versão 8,6 ou posterior, selecione **Web e aplicativo de console**  >  **App**  >  **aplicativos Web (Model-View-Controller)**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="48ba0-228">Na caixa de diálogo **configurar seu novo aplicativo Web** :</span><span class="sxs-lookup"><span data-stu-id="48ba0-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="48ba0-229">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="48ba0-230">Se for apresentada uma opção para selecionar uma **estrutura de destino**, selecione a versão 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="48ba0-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="48ba0-231">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-231">Select **Next**.</span></span>

* <span data-ttu-id="48ba0-232">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="48ba0-233">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="48ba0-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48ba0-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ba0-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48ba0-235">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="48ba0-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="48ba0-236">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="48ba0-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="48ba0-237">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48ba0-238">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="48ba0-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="48ba0-239">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="48ba0-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="48ba0-240">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="48ba0-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="48ba0-241">Muitos desenvolvedores preferem usar o modo sem depuração para iniciar o aplicativo rapidamente e exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="48ba0-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="48ba0-242">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="48ba0-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="48ba0-244">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="48ba0-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="48ba0-246">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="48ba0-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="48ba0-247">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="48ba0-247">This app doesn't track personal information.</span></span> <span data-ttu-id="48ba0-248">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="48ba0-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="48ba0-250">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="48ba0-250">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="48ba0-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ba0-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48ba0-253">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="48ba0-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="48ba0-254">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="48ba0-255">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="48ba0-256">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="48ba0-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="48ba0-257">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="48ba0-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="48ba0-258">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="48ba0-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="48ba0-259">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="48ba0-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="48ba0-260">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="48ba0-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="48ba0-261">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="48ba0-261">This app doesn't track personal information.</span></span> <span data-ttu-id="48ba0-262">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="48ba0-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="48ba0-264">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="48ba0-264">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="48ba0-266">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="48ba0-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="48ba0-267">Selecione **executar**  >  **Iniciar sem depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="48ba0-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="48ba0-268">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="48ba0-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="48ba0-269">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48ba0-270">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="48ba0-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="48ba0-271">Uma porta aleatória é usada para o servidor Web quando o Visual Studio cria um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="48ba0-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="48ba0-272">Quando você executar o aplicativo, você verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="48ba0-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="48ba0-273">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="48ba0-274">Selecione **Aceitar** para consentir com o acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="48ba0-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="48ba0-275">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="48ba0-275">This app doesn't track personal information.</span></span> <span data-ttu-id="48ba0-276">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="48ba0-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="48ba0-278">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="48ba0-278">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="48ba0-280">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="48ba0-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="48ba0-281">Próximo</span><span class="sxs-lookup"><span data-stu-id="48ba0-281">Next</span></span>](adding-controller.md)

::: moniker-end
