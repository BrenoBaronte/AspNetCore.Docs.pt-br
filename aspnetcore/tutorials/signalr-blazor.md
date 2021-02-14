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
ms.openlocfilehash: 355db5ad5462747be0058096bc0132ed1071f290
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280995"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="26b3f-103">Usar ASP.NET Core SignalR com Blazor</span><span class="sxs-lookup"><span data-stu-id="26b3f-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="26b3f-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor .</span><span class="sxs-lookup"><span data-stu-id="26b3f-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="26b3f-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="26b3f-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="26b3f-106">Criar um Blazor projeto</span><span class="sxs-lookup"><span data-stu-id="26b3f-106">Create a Blazor project</span></span>
> * <span data-ttu-id="26b3f-107">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="26b3f-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="26b3f-108">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="26b3f-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="26b3f-109">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="26b3f-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="26b3f-110">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="26b3f-110">Add Razor component code for chat</span></span>

<span data-ttu-id="26b3f-111">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="26b3f-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="26b3f-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26b3f-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="26b3f-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="26b3f-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b3f-115">[Visual Studio 2019 16,8 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="26b3f-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="26b3f-118">Visual Studio para Mac versão 8,8 ou posterior</span><span class="sxs-lookup"><span data-stu-id="26b3f-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-119">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b3f-121">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="26b3f-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="26b3f-124">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="26b3f-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-125">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="26b3f-126">Criar um Blazor WebAssembly aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="26b3f-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="26b3f-127">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="26b3f-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26b3f-129">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="26b3f-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26b3f-130">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="26b3f-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="26b3f-131">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-131">Create a new project.</span></span>

1. <span data-ttu-id="26b3f-132">Selecione **Blazor aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="26b3f-133">Digite `BlazorWebAssemblySignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="26b3f-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="26b3f-134">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="26b3f-135">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-135">Select **Create**.</span></span>

1. <span data-ttu-id="26b3f-136">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="26b3f-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="26b3f-137">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="26b3f-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="26b3f-138">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="26b3f-140">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="26b3f-141">A `-ho|--hosted` opção cria uma solução hospedada Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="26b3f-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

   <span data-ttu-id="26b3f-142">A `-o|--output` opção cria uma pasta para a solução.</span><span class="sxs-lookup"><span data-stu-id="26b3f-142">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="26b3f-143">Se você tiver criado uma pasta para a solução e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar a solução.</span><span class="sxs-lookup"><span data-stu-id="26b3f-143">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="26b3f-144">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26b3f-144">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="26b3f-145">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-145">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="26b3f-146">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-146">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-147">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-147">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26b3f-148">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="26b3f-148">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="26b3f-149">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-149">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="26b3f-150">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="26b3f-150">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="26b3f-151">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="26b3f-151">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="26b3f-152">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-152">Select **Next**.</span></span>

1. <span data-ttu-id="26b3f-153">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-153">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="26b3f-154">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="26b3f-154">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="26b3f-155">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-155">Select **Next**.</span></span>

1. <span data-ttu-id="26b3f-156">No campo **nome do projeto** , nomeie o aplicativo `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-156">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="26b3f-157">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-157">Select **Create**.</span></span>

   <span data-ttu-id="26b3f-158">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="26b3f-158">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="26b3f-159">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-159">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="26b3f-160">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="26b3f-160">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-161">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-161">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26b3f-162">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-162">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="26b3f-163">A `-ho|--hosted` opção cria uma solução hospedada Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="26b3f-163">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="26b3f-164">A `-o|--output` opção cria uma pasta para a solução.</span><span class="sxs-lookup"><span data-stu-id="26b3f-164">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="26b3f-165">Se você tiver criado uma pasta para a solução e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar a solução.</span><span class="sxs-lookup"><span data-stu-id="26b3f-165">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="26b3f-166">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="26b3f-166">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-167">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-167">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="26b3f-168">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-168">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26b3f-169">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-169">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26b3f-170">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="26b3f-170">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="26b3f-171">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="26b3f-171">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="26b3f-172">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26b3f-172">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="26b3f-173">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-173">Select **Install**.</span></span>

1. <span data-ttu-id="26b3f-174">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-174">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="26b3f-175">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="26b3f-175">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-176">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-176">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="26b3f-177">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-177">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="26b3f-178">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-178">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-179">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26b3f-180">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-180">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26b3f-181">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-181">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26b3f-182">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="26b3f-182">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="26b3f-183">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="26b3f-183">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="26b3f-184">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26b3f-184">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="26b3f-185">Selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-185">Select **Add Package**.</span></span>

1. <span data-ttu-id="26b3f-186">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="26b3f-186">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-187">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-187">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26b3f-188">Em um shell de comando da pasta da solução, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-188">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="26b3f-189">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-189">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="26b3f-190">Adicione o pacote System. Text. transcodifications. Web</span><span class="sxs-lookup"><span data-stu-id="26b3f-190">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="26b3f-191">*Esta seção se aplica somente a aplicativos para ASP.NET Core versão 3. x.*</span><span class="sxs-lookup"><span data-stu-id="26b3f-191">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="26b3f-192">Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x em um aplicativo ASP.NET Core 3. x, o `BlazorWebAssemblySignalRApp.Server` projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="26b3f-192">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="26b3f-193">O problema subjacente será resolvido em uma versão futura de patch do .NET 5.</span><span class="sxs-lookup"><span data-stu-id="26b3f-193">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="26b3f-194">Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="26b3f-194">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="26b3f-195">Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao `BlazorWebAssemblySignalRApp.Server` projeto da solução ASP.NET Core 3,1 hospedada Blazor , siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="26b3f-195">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-196">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="26b3f-197">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-197">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26b3f-198">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-198">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26b3f-199">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="26b3f-199">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="26b3f-200">Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote.</span><span class="sxs-lookup"><span data-stu-id="26b3f-200">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="26b3f-201">Selecione a versão do pacote que corresponde à estrutura compartilhada em uso.</span><span class="sxs-lookup"><span data-stu-id="26b3f-201">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="26b3f-202">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-202">Select **Install**.</span></span>

1. <span data-ttu-id="26b3f-203">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-203">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="26b3f-204">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="26b3f-204">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-205">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="26b3f-206">No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="26b3f-206">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="26b3f-207">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-207">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-208">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26b3f-209">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-209">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26b3f-210">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-210">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26b3f-211">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="26b3f-211">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="26b3f-212">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote, selecione a versão correta do pacote que corresponde à estrutura compartilhada em uso e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-212">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="26b3f-213">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="26b3f-213">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-214">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-214">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26b3f-215">Em um shell de comando da pasta da solução, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-215">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="26b3f-216">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-216">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="26b3f-217">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="26b3f-217">Add a SignalR hub</span></span>

<span data-ttu-id="26b3f-218">No `BlazorWebAssemblySignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="26b3f-218">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="26b3f-219">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="26b3f-219">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="26b3f-220">No projeto `BlazorWebAssemblySignalRApp.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="26b3f-220">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="26b3f-221">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="26b3f-221">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="26b3f-222">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26b3f-222">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="26b3f-223">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="26b3f-223">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="26b3f-224">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="26b3f-224">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="26b3f-225">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="26b3f-225">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="26b3f-226">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26b3f-226">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="26b3f-227">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="26b3f-227">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="26b3f-228">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="26b3f-228">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="26b3f-229">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="26b3f-229">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="26b3f-230">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="26b3f-230">Add Razor component code for chat</span></span>

1. <span data-ttu-id="26b3f-231">No projeto `BlazorWebAssemblySignalRApp.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="26b3f-231">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="26b3f-232">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="26b3f-232">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="26b3f-233">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="26b3f-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="26b3f-234">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="26b3f-234">Run the app</span></span>

<span data-ttu-id="26b3f-235">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="26b3f-235">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-236">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="26b3f-237">Em **Gerenciador de soluções**, selecione o `BlazorWebAssemblySignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-237">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="26b3f-238">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="26b3f-238">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26b3f-239">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="26b3f-239">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26b3f-240">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="26b3f-240">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26b3f-241">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="26b3f-241">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="26b3f-243">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26b3f-243">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-244">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-244">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="26b3f-245">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="26b3f-245">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26b3f-246">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="26b3f-246">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26b3f-247">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="26b3f-247">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26b3f-248">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="26b3f-248">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="26b3f-250">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26b3f-250">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-251">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26b3f-252">Na barra lateral da **solução** , selecione o `BlazorWebAssemblySignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-252">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="26b3f-253">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="26b3f-253">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26b3f-254">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="26b3f-254">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26b3f-255">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="26b3f-255">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26b3f-256">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="26b3f-256">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="26b3f-258">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26b3f-258">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-259">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-259">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="26b3f-260">Em um shell de comando da pasta da solução, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="26b3f-260">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="26b3f-261">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="26b3f-261">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26b3f-262">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="26b3f-262">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26b3f-263">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="26b3f-263">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="26b3f-265">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26b3f-265">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="26b3f-266">Criar um Blazor Server aplicativo</span><span class="sxs-lookup"><span data-stu-id="26b3f-266">Create a Blazor Server app</span></span>

<span data-ttu-id="26b3f-267">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="26b3f-267">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-268">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26b3f-269">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="26b3f-269">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26b3f-270">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="26b3f-270">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="26b3f-271">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-271">Create a new project.</span></span>

1. <span data-ttu-id="26b3f-272">Selecione **Blazor aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-272">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="26b3f-273">Digite `BlazorServerSignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="26b3f-273">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="26b3f-274">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-274">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="26b3f-275">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-275">Select **Create**.</span></span>

1. <span data-ttu-id="26b3f-276">Escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="26b3f-276">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="26b3f-277">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-277">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-278">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-278">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="26b3f-279">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-279">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="26b3f-280">A `-o|--output` opção cria uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-280">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="26b3f-281">Se você tiver criado uma pasta para o projeto e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-281">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="26b3f-282">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26b3f-282">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="26b3f-283">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-283">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="26b3f-284">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-284">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-285">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-285">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26b3f-286">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="26b3f-286">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="26b3f-287">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-287">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="26b3f-288">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="26b3f-288">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="26b3f-289">Escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="26b3f-289">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="26b3f-290">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-290">Select **Next**.</span></span>

1. <span data-ttu-id="26b3f-291">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-291">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="26b3f-292">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-292">Select **Next**.</span></span>

1. <span data-ttu-id="26b3f-293">No campo **nome do projeto** , nomeie o aplicativo `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-293">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="26b3f-294">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-294">Select **Create**.</span></span>

   <span data-ttu-id="26b3f-295">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="26b3f-295">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="26b3f-296">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-296">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="26b3f-297">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="26b3f-297">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-298">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-298">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26b3f-299">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-299">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="26b3f-300">A `-o|--output` opção cria uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-300">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="26b3f-301">Se você tiver criado uma pasta para o projeto e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="26b3f-301">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="26b3f-302">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="26b3f-302">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-303">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-303">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="26b3f-304">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-304">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26b3f-305">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-305">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26b3f-306">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="26b3f-306">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="26b3f-307">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="26b3f-307">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="26b3f-308">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26b3f-308">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="26b3f-309">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-309">Select **Install**.</span></span>

1. <span data-ttu-id="26b3f-310">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-310">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="26b3f-311">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="26b3f-311">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-312">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-312">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="26b3f-313">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-313">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="26b3f-314">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-314">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-315">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-315">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26b3f-316">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-316">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26b3f-317">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-317">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26b3f-318">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="26b3f-318">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="26b3f-319">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="26b3f-319">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="26b3f-320">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26b3f-320">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="26b3f-321">Selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-321">Select **Add Package**.</span></span>

1. <span data-ttu-id="26b3f-322">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="26b3f-322">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-323">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-323">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26b3f-324">Em um shell de comando da pasta do projeto, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-324">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="26b3f-325">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-325">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="26b3f-326">Adicione o pacote System. Text. transcodifications. Web</span><span class="sxs-lookup"><span data-stu-id="26b3f-326">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="26b3f-327">*Esta seção se aplica somente a aplicativos para ASP.NET Core versão 3. x.*</span><span class="sxs-lookup"><span data-stu-id="26b3f-327">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="26b3f-328">Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x em um aplicativo ASP.NET Core 3. x, o projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="26b3f-328">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="26b3f-329">O problema subjacente será resolvido em uma versão futura de patch do .NET 5.</span><span class="sxs-lookup"><span data-stu-id="26b3f-329">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="26b3f-330">Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="26b3f-330">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="26b3f-331">Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao projeto, siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="26b3f-331">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-332">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="26b3f-333">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-333">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26b3f-334">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-334">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26b3f-335">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="26b3f-335">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="26b3f-336">Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote.</span><span class="sxs-lookup"><span data-stu-id="26b3f-336">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="26b3f-337">Selecione a versão do pacote que corresponde à estrutura compartilhada em uso.</span><span class="sxs-lookup"><span data-stu-id="26b3f-337">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="26b3f-338">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-338">Select **Install**.</span></span>

1. <span data-ttu-id="26b3f-339">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-339">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="26b3f-340">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="26b3f-340">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-341">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-341">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="26b3f-342">No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="26b3f-342">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="26b3f-343">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-343">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-344">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-344">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26b3f-345">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-345">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26b3f-346">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="26b3f-346">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26b3f-347">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="26b3f-347">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="26b3f-348">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote, selecione a versão correta do pacote que corresponde à estrutura compartilhada em uso e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="26b3f-348">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="26b3f-349">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="26b3f-349">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-350">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-350">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26b3f-351">Em um shell de comando da pasta do projeto, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="26b3f-351">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="26b3f-352">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="26b3f-352">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="26b3f-353">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="26b3f-353">Add a SignalR hub</span></span>

<span data-ttu-id="26b3f-354">Crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="26b3f-354">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="26b3f-355">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="26b3f-355">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="26b3f-356">Abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="26b3f-356">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="26b3f-357">Adicione os namespaces para <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> e a `ChatHub` classe à parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="26b3f-357">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="26b3f-358">Adicionar serviços de middleware de compactação de resposta a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26b3f-358">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="26b3f-359">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="26b3f-359">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="26b3f-360">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="26b3f-360">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="26b3f-361">Entre os pontos de extremidade para mapear o Blazor Hub e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="26b3f-361">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="26b3f-362">Adicionar serviços de middleware de compactação de resposta a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26b3f-362">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="26b3f-363">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="26b3f-363">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="26b3f-364">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="26b3f-364">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="26b3f-365">Entre os pontos de extremidade para mapear o Blazor Hub e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="26b3f-365">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="26b3f-366">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="26b3f-366">Add Razor component code for chat</span></span>

1. <span data-ttu-id="26b3f-367">Abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="26b3f-367">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="26b3f-368">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="26b3f-368">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="26b3f-369">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="26b3f-369">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="26b3f-370">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="26b3f-370">Run the app</span></span>

<span data-ttu-id="26b3f-371">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="26b3f-371">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b3f-372">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b3f-372">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="26b3f-373">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="26b3f-373">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26b3f-374">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="26b3f-374">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26b3f-375">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="26b3f-375">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26b3f-376">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="26b3f-376">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="26b3f-378">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26b3f-378">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26b3f-379">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26b3f-379">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="26b3f-380">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="26b3f-380">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26b3f-381">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="26b3f-381">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26b3f-382">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="26b3f-382">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26b3f-383">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="26b3f-383">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="26b3f-385">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26b3f-385">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26b3f-386">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26b3f-386">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26b3f-387">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="26b3f-387">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26b3f-388">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="26b3f-388">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26b3f-389">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="26b3f-389">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26b3f-390">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="26b3f-390">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="26b3f-392">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26b3f-392">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b3f-393">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b3f-393">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="26b3f-394">Em um shell de comando da pasta do projeto, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="26b3f-394">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="26b3f-395">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="26b3f-395">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26b3f-396">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="26b3f-396">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26b3f-397">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="26b3f-397">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="26b3f-399">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26b3f-399">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="26b3f-400">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="26b3f-400">Next steps</span></span>

<span data-ttu-id="26b3f-401">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="26b3f-401">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="26b3f-402">Criar um Blazor projeto</span><span class="sxs-lookup"><span data-stu-id="26b3f-402">Create a Blazor project</span></span>
> * <span data-ttu-id="26b3f-403">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="26b3f-403">Add the SignalR client library</span></span>
> * <span data-ttu-id="26b3f-404">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="26b3f-404">Add a SignalR hub</span></span>
> * <span data-ttu-id="26b3f-405">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="26b3f-405">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="26b3f-406">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="26b3f-406">Add Razor component code for chat</span></span>

<span data-ttu-id="26b3f-407">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="26b3f-407">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="26b3f-408"><xref:blazor/index>
> [Autenticação de token de portador com Identity eventos de servidor, WebSocket e Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="26b3f-408"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26b3f-409">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="26b3f-409">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="26b3f-410">SignalR negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="26b3f-410">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
