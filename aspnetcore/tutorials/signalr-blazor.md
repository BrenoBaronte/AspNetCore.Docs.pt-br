---
title: Usar ASP.NET Core SignalR com Blazor
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: f4e51b39c4c3b0c444b08025e9bd74eec0747541
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536348"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="ff970-103">Usar ASP.NET Core SignalR com Blazor</span><span class="sxs-lookup"><span data-stu-id="ff970-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="ff970-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor .</span><span class="sxs-lookup"><span data-stu-id="ff970-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="ff970-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="ff970-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ff970-106">Criar um Blazor projeto</span><span class="sxs-lookup"><span data-stu-id="ff970-106">Create a Blazor project</span></span>
> * <span data-ttu-id="ff970-107">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="ff970-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="ff970-108">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="ff970-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="ff970-109">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="ff970-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="ff970-110">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="ff970-110">Add Razor component code for chat</span></span>

<span data-ttu-id="ff970-111">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="ff970-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="ff970-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff970-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff970-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ff970-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff970-115">[Visual Studio 2019 16,8 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="ff970-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ff970-118">Visual Studio para Mac versão 8,8 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ff970-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-119">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff970-121">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="ff970-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ff970-124">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ff970-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-125">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="ff970-126">Criar um Blazor WebAssembly aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="ff970-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="ff970-127">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="ff970-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ff970-129">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="ff970-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ff970-130">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="ff970-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="ff970-131">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-131">Create a new project.</span></span>

1. <span data-ttu-id="ff970-132">Selecione **Blazor aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="ff970-133">Digite `BlazorWebAssemblySignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="ff970-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="ff970-134">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ff970-135">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-135">Select **Create**.</span></span>

1. <span data-ttu-id="ff970-136">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="ff970-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="ff970-137">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="ff970-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="ff970-138">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ff970-140">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="ff970-141">A `-ho|--hosted` opção cria uma solução hospedada Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="ff970-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="ff970-142">Para obter informações sobre como configurar os ativos de VS Code na `.vscode` pasta, consulte as diretrizes do sistema operacional **Linux** em <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="ff970-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="ff970-143">A `-o|--output` opção cria uma pasta para a solução.</span><span class="sxs-lookup"><span data-stu-id="ff970-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="ff970-144">Se você tiver criado uma pasta para a solução e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar a solução.</span><span class="sxs-lookup"><span data-stu-id="ff970-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="ff970-145">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff970-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="ff970-146">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ff970-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="ff970-147">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="ff970-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff970-149">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="ff970-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="ff970-150">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="ff970-151">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="ff970-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="ff970-152">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="ff970-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ff970-153">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-153">Select **Next**.</span></span>

1. <span data-ttu-id="ff970-154">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="ff970-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="ff970-155">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="ff970-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="ff970-156">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-156">Select **Next**.</span></span>

1. <span data-ttu-id="ff970-157">No campo **nome do projeto** , nomeie o aplicativo `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="ff970-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="ff970-158">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-158">Select **Create**.</span></span>

   <span data-ttu-id="ff970-159">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="ff970-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="ff970-160">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="ff970-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="ff970-161">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="ff970-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-162">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff970-163">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="ff970-164">A `-ho|--hosted` opção cria uma solução hospedada Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="ff970-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="ff970-165">A `-o|--output` opção cria uma pasta para a solução.</span><span class="sxs-lookup"><span data-stu-id="ff970-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="ff970-166">Se você tiver criado uma pasta para a solução e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar a solução.</span><span class="sxs-lookup"><span data-stu-id="ff970-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="ff970-167">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="ff970-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ff970-169">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="ff970-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff970-170">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="ff970-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff970-171">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ff970-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ff970-172">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="ff970-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="ff970-173">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff970-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="ff970-174">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-174">Select **Install**.</span></span>

1. <span data-ttu-id="ff970-175">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="ff970-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ff970-176">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="ff970-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ff970-178">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="ff970-179">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="ff970-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-180">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff970-181">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="ff970-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff970-182">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="ff970-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff970-183">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ff970-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ff970-184">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="ff970-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="ff970-185">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff970-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="ff970-186">Selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="ff970-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="ff970-187">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="ff970-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-188">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff970-189">Em um shell de comando da pasta da solução, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="ff970-190">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="ff970-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="ff970-191">Adicione o pacote System. Text. transcodifications. Web</span><span class="sxs-lookup"><span data-stu-id="ff970-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="ff970-192">*Esta seção se aplica somente a aplicativos para ASP.NET Core versão 3. x.*</span><span class="sxs-lookup"><span data-stu-id="ff970-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="ff970-193">Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x em um aplicativo ASP.NET Core 3. x, o `BlazorWebAssemblySignalRApp.Server` projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="ff970-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="ff970-194">O problema subjacente será resolvido em uma versão futura de patch do .NET 5.</span><span class="sxs-lookup"><span data-stu-id="ff970-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="ff970-195">Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="ff970-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="ff970-196">Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao `BlazorWebAssemblySignalRApp.Server` projeto da solução ASP.NET Core 3,1 hospedada Blazor , siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="ff970-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ff970-198">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="ff970-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff970-199">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="ff970-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff970-200">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ff970-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ff970-201">Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote.</span><span class="sxs-lookup"><span data-stu-id="ff970-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="ff970-202">Selecione a versão do pacote que corresponde à estrutura compartilhada em uso.</span><span class="sxs-lookup"><span data-stu-id="ff970-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="ff970-203">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-203">Select **Install**.</span></span>

1. <span data-ttu-id="ff970-204">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="ff970-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ff970-205">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="ff970-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ff970-207">No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ff970-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="ff970-208">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="ff970-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff970-210">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="ff970-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff970-211">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="ff970-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff970-212">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ff970-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ff970-213">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote, selecione a versão correta do pacote que corresponde à estrutura compartilhada em uso e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="ff970-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="ff970-214">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="ff970-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-215">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff970-216">Em um shell de comando da pasta da solução, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="ff970-217">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="ff970-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="ff970-218">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="ff970-218">Add a SignalR hub</span></span>

<span data-ttu-id="ff970-219">No `BlazorWebAssemblySignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="ff970-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="ff970-220">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="ff970-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="ff970-221">No projeto `BlazorWebAssemblySignalRApp.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="ff970-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="ff970-222">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="ff970-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ff970-223">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ff970-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="ff970-224">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff970-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ff970-225">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="ff970-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ff970-226">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="ff970-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ff970-227">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ff970-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="ff970-228">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff970-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ff970-229">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="ff970-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ff970-230">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="ff970-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="ff970-231">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="ff970-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="ff970-232">No projeto `BlazorWebAssemblySignalRApp.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="ff970-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ff970-233">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff970-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ff970-234">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff970-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="ff970-235">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ff970-235">Run the app</span></span>

<span data-ttu-id="ff970-236">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="ff970-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ff970-238">Em **Gerenciador de soluções**, selecione o `BlazorWebAssemblySignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="ff970-239">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="ff970-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff970-240">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="ff970-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff970-241">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="ff970-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff970-242">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="ff970-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff970-244">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff970-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ff970-246">Para obter informações sobre como configurar os ativos de VS Code na `.vscode` pasta, consulte as diretrizes do sistema operacional **Linux** em <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="ff970-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="ff970-247">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="ff970-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff970-248">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="ff970-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff970-249">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="ff970-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff970-250">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="ff970-250">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff970-252">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff970-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-253">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff970-254">Na barra lateral da **solução** , selecione o `BlazorWebAssemblySignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="ff970-255">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="ff970-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff970-256">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="ff970-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff970-257">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="ff970-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff970-258">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="ff970-258">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff970-260">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff970-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-261">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ff970-262">Em um shell de comando da pasta da solução, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ff970-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="ff970-263">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="ff970-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff970-264">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="ff970-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff970-265">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="ff970-265">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff970-267">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff970-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="ff970-268">Criar um Blazor Server aplicativo</span><span class="sxs-lookup"><span data-stu-id="ff970-268">Create a Blazor Server app</span></span>

<span data-ttu-id="ff970-269">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="ff970-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ff970-271">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="ff970-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ff970-272">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="ff970-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="ff970-273">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-273">Create a new project.</span></span>

1. <span data-ttu-id="ff970-274">Selecione **Blazor aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="ff970-275">Digite `BlazorServerSignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="ff970-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="ff970-276">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ff970-277">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-277">Select **Create**.</span></span>

1. <span data-ttu-id="ff970-278">Escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="ff970-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="ff970-279">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ff970-281">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="ff970-282">A `-o|--output` opção cria uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="ff970-283">Se você tiver criado uma pasta para o projeto e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="ff970-284">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff970-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="ff970-285">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ff970-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="ff970-286">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="ff970-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="ff970-287">Para obter informações sobre como configurar os ativos de VS Code na `.vscode` pasta, consulte as diretrizes do sistema operacional **Linux** em <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="ff970-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-288">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff970-289">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="ff970-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="ff970-290">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="ff970-291">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="ff970-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="ff970-292">Escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="ff970-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ff970-293">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-293">Select **Next**.</span></span>

1. <span data-ttu-id="ff970-294">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="ff970-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="ff970-295">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-295">Select **Next**.</span></span>

1. <span data-ttu-id="ff970-296">No campo **nome do projeto** , nomeie o aplicativo `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="ff970-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="ff970-297">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-297">Select **Create**.</span></span>

   <span data-ttu-id="ff970-298">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="ff970-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="ff970-299">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="ff970-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="ff970-300">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="ff970-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-301">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff970-302">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="ff970-303">A `-o|--output` opção cria uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="ff970-304">Se você tiver criado uma pasta para o projeto e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="ff970-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="ff970-305">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="ff970-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ff970-307">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="ff970-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff970-308">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="ff970-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff970-309">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ff970-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ff970-310">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="ff970-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="ff970-311">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff970-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="ff970-312">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-312">Select **Install**.</span></span>

1. <span data-ttu-id="ff970-313">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="ff970-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ff970-314">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="ff970-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ff970-316">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="ff970-317">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="ff970-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-318">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff970-319">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="ff970-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff970-320">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="ff970-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff970-321">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ff970-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ff970-322">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="ff970-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="ff970-323">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff970-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="ff970-324">Selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="ff970-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="ff970-325">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="ff970-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-326">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff970-327">Em um shell de comando da pasta do projeto, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="ff970-328">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="ff970-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="ff970-329">Adicione o pacote System. Text. transcodifications. Web</span><span class="sxs-lookup"><span data-stu-id="ff970-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="ff970-330">*Esta seção se aplica somente a aplicativos para ASP.NET Core versão 3. x.*</span><span class="sxs-lookup"><span data-stu-id="ff970-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="ff970-331">Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x em um aplicativo ASP.NET Core 3. x, o projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="ff970-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="ff970-332">O problema subjacente será resolvido em uma versão futura de patch do .NET 5.</span><span class="sxs-lookup"><span data-stu-id="ff970-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="ff970-333">Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="ff970-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="ff970-334">Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao projeto, siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="ff970-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ff970-336">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="ff970-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff970-337">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="ff970-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff970-338">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ff970-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ff970-339">Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote.</span><span class="sxs-lookup"><span data-stu-id="ff970-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="ff970-340">Selecione a versão do pacote que corresponde à estrutura compartilhada em uso.</span><span class="sxs-lookup"><span data-stu-id="ff970-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="ff970-341">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="ff970-341">Select **Install**.</span></span>

1. <span data-ttu-id="ff970-342">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="ff970-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ff970-343">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="ff970-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ff970-345">No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ff970-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="ff970-346">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="ff970-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-347">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff970-348">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="ff970-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff970-349">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="ff970-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff970-350">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ff970-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ff970-351">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote, selecione a versão correta do pacote que corresponde à estrutura compartilhada em uso e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="ff970-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="ff970-352">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="ff970-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-353">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff970-354">Em um shell de comando da pasta do projeto, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff970-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="ff970-355">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="ff970-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="ff970-356">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="ff970-356">Add a SignalR hub</span></span>

<span data-ttu-id="ff970-357">Crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="ff970-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="ff970-358">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="ff970-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="ff970-359">Abra o arquivo `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="ff970-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="ff970-360">Adicione os namespaces para <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> e a `ChatHub` classe à parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="ff970-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ff970-361">Adicionar serviços de middleware de compactação de resposta a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ff970-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="ff970-362">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff970-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ff970-363">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="ff970-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ff970-364">Entre os pontos de extremidade para mapear o Blazor Hub e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="ff970-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ff970-365">Adicionar serviços de middleware de compactação de resposta a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ff970-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="ff970-366">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff970-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ff970-367">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="ff970-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ff970-368">Entre os pontos de extremidade para mapear o Blazor Hub e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="ff970-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="ff970-369">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="ff970-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="ff970-370">Abra o arquivo `Pages/Index.razor` .</span><span class="sxs-lookup"><span data-stu-id="ff970-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ff970-371">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff970-371">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ff970-372">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff970-372">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="ff970-373">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ff970-373">Run the app</span></span>

<span data-ttu-id="ff970-374">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="ff970-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff970-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff970-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ff970-376">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="ff970-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff970-377">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="ff970-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff970-378">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="ff970-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff970-379">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="ff970-379">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff970-381">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff970-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff970-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff970-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ff970-383">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="ff970-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff970-384">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="ff970-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff970-385">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="ff970-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff970-386">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="ff970-386">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff970-388">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff970-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff970-389">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ff970-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff970-390">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="ff970-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff970-391">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="ff970-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff970-392">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="ff970-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff970-393">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="ff970-393">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff970-395">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff970-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff970-396">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff970-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ff970-397">Em um shell de comando da pasta do projeto, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ff970-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="ff970-398">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="ff970-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff970-399">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="ff970-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff970-400">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="ff970-400">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff970-402">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff970-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="ff970-403">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="ff970-403">Next steps</span></span>

<span data-ttu-id="ff970-404">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="ff970-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ff970-405">Criar um Blazor projeto</span><span class="sxs-lookup"><span data-stu-id="ff970-405">Create a Blazor project</span></span>
> * <span data-ttu-id="ff970-406">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="ff970-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="ff970-407">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="ff970-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="ff970-408">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="ff970-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="ff970-409">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="ff970-409">Add Razor component code for chat</span></span>

<span data-ttu-id="ff970-410">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="ff970-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="ff970-411"><xref:blazor/index>
> [Autenticação de token de portador com Identity eventos de servidor, WebSocket e Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="ff970-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff970-412">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ff970-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="ff970-413">SignalR negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="ff970-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
