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
ms.openlocfilehash: 226813f3b975e207a51eba24d6cbb57f00464073
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107201"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="d8c00-103">Usar ASP.NET Core SignalR com Blazor</span><span class="sxs-lookup"><span data-stu-id="d8c00-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="d8c00-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d8c00-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d8c00-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor .</span><span class="sxs-lookup"><span data-stu-id="d8c00-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="d8c00-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="d8c00-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d8c00-107">Criar um Blazor projeto</span><span class="sxs-lookup"><span data-stu-id="d8c00-107">Create a Blazor project</span></span>
> * <span data-ttu-id="d8c00-108">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="d8c00-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="d8c00-109">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d8c00-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="d8c00-110">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d8c00-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d8c00-111">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="d8c00-111">Add Razor component code for chat</span></span>

<span data-ttu-id="d8c00-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="d8c00-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="d8c00-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8c00-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8c00-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d8c00-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8c00-116">[Visual Studio 2019 16,8 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="d8c00-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d8c00-119">Visual Studio para Mac versão 8,8 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d8c00-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8c00-122">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="d8c00-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-124">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d8c00-125">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d8c00-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-126">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="d8c00-127">Criar um Blazor WebAssembly aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="d8c00-127">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="d8c00-128">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="d8c00-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d8c00-130">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="d8c00-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d8c00-131">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="d8c00-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="d8c00-132">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-132">Create a new project.</span></span>

1. <span data-ttu-id="d8c00-133">Selecione **Blazor aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="d8c00-134">Digite `BlazorWebAssemblySignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="d8c00-134">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="d8c00-135">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d8c00-136">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-136">Select **Create**.</span></span>

1. <span data-ttu-id="d8c00-137">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="d8c00-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="d8c00-138">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="d8c00-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="d8c00-139">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d8c00-141">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="d8c00-142">A `-ho|--hosted` opção cria uma solução hospedada Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="d8c00-142">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

   <span data-ttu-id="d8c00-143">A `-o|--output` opção cria uma pasta para a solução.</span><span class="sxs-lookup"><span data-stu-id="d8c00-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="d8c00-144">Se você tiver criado uma pasta para a solução e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar a solução.</span><span class="sxs-lookup"><span data-stu-id="d8c00-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="d8c00-145">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8c00-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="d8c00-146">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="d8c00-147">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8c00-149">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="d8c00-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="d8c00-150">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d8c00-151">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="d8c00-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="d8c00-152">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="d8c00-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d8c00-153">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-153">Select **Next**.</span></span>

1. <span data-ttu-id="d8c00-154">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d8c00-155">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="d8c00-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="d8c00-156">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-156">Select **Next**.</span></span>

1. <span data-ttu-id="d8c00-157">No campo **nome do projeto** , nomeie o aplicativo `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="d8c00-158">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-158">Select **Create**.</span></span>

   <span data-ttu-id="d8c00-159">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="d8c00-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d8c00-160">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="d8c00-161">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="d8c00-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-162">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d8c00-163">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="d8c00-164">A `-ho|--hosted` opção cria uma solução hospedada Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="d8c00-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="d8c00-165">A `-o|--output` opção cria uma pasta para a solução.</span><span class="sxs-lookup"><span data-stu-id="d8c00-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="d8c00-166">Se você tiver criado uma pasta para a solução e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar a solução.</span><span class="sxs-lookup"><span data-stu-id="d8c00-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="d8c00-167">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="d8c00-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d8c00-169">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d8c00-170">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d8c00-171">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d8c00-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d8c00-172">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="d8c00-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d8c00-173">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8c00-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d8c00-174">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-174">Select **Install**.</span></span>

1. <span data-ttu-id="d8c00-175">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d8c00-176">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="d8c00-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d8c00-178">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d8c00-179">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-180">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8c00-181">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d8c00-182">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d8c00-183">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d8c00-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d8c00-184">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="d8c00-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d8c00-185">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8c00-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d8c00-186">Selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="d8c00-187">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="d8c00-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-188">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d8c00-189">Em um shell de comando da pasta da solução, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d8c00-190">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="d8c00-191">Adicione o pacote System. Text. transcodifications. Web</span><span class="sxs-lookup"><span data-stu-id="d8c00-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="d8c00-192">*Esta seção se aplica somente a aplicativos para ASP.NET Core versão 3. x.*</span><span class="sxs-lookup"><span data-stu-id="d8c00-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="d8c00-193">Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x em um aplicativo ASP.NET Core 3. x, o `BlazorWebAssemblySignalRApp.Server` projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="d8c00-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="d8c00-194">O problema subjacente será resolvido em uma versão futura de patch do .NET 5.</span><span class="sxs-lookup"><span data-stu-id="d8c00-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="d8c00-195">Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="d8c00-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="d8c00-196">Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao `BlazorWebAssemblySignalRApp.Server` projeto da solução ASP.NET Core 3,1 hospedada Blazor , siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="d8c00-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d8c00-198">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d8c00-199">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d8c00-200">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d8c00-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d8c00-201">Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote.</span><span class="sxs-lookup"><span data-stu-id="d8c00-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="d8c00-202">Selecione a versão do pacote que corresponde à estrutura compartilhada em uso.</span><span class="sxs-lookup"><span data-stu-id="d8c00-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="d8c00-203">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-203">Select **Install**.</span></span>

1. <span data-ttu-id="d8c00-204">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d8c00-205">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="d8c00-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d8c00-207">No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d8c00-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="d8c00-208">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8c00-210">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorWebAssemblySignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d8c00-211">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d8c00-212">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d8c00-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d8c00-213">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote, selecione a versão correta do pacote que corresponde à estrutura compartilhada em uso e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="d8c00-214">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="d8c00-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-215">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d8c00-216">Em um shell de comando da pasta da solução, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="d8c00-217">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="d8c00-218">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d8c00-218">Add a SignalR hub</span></span>

<span data-ttu-id="d8c00-219">No `BlazorWebAssemblySignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="d8c00-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="d8c00-220">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d8c00-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="d8c00-221">No projeto `BlazorWebAssemblySignalRApp.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="d8c00-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="d8c00-222">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="d8c00-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d8c00-223">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d8c00-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="d8c00-224">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d8c00-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d8c00-225">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="d8c00-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d8c00-226">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="d8c00-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d8c00-227">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d8c00-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="d8c00-228">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d8c00-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d8c00-229">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="d8c00-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d8c00-230">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="d8c00-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="d8c00-231">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="d8c00-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="d8c00-232">No projeto `BlazorWebAssemblySignalRApp.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="d8c00-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d8c00-233">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d8c00-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d8c00-234">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d8c00-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="d8c00-235">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d8c00-235">Run the app</span></span>

<span data-ttu-id="d8c00-236">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="d8c00-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d8c00-238">Em **Gerenciador de soluções**, selecione o `BlazorWebAssemblySignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="d8c00-239">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="d8c00-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d8c00-240">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="d8c00-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d8c00-241">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d8c00-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d8c00-242">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="d8c00-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d8c00-244">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d8c00-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d8c00-246">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="d8c00-246">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d8c00-247">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="d8c00-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d8c00-248">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d8c00-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d8c00-249">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="d8c00-249">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d8c00-251">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d8c00-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-252">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-252">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8c00-253">Na barra lateral da **solução** , selecione o `BlazorWebAssemblySignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-253">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="d8c00-254">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="d8c00-254">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d8c00-255">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="d8c00-255">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d8c00-256">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d8c00-256">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d8c00-257">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="d8c00-257">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d8c00-259">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d8c00-259">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-260">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-260">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d8c00-261">Em um shell de comando da pasta da solução, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d8c00-261">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="d8c00-262">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="d8c00-262">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d8c00-263">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d8c00-263">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d8c00-264">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="d8c00-264">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d8c00-266">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d8c00-266">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="d8c00-267">Criar um Blazor Server aplicativo</span><span class="sxs-lookup"><span data-stu-id="d8c00-267">Create a Blazor Server app</span></span>

<span data-ttu-id="d8c00-268">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="d8c00-268">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-269">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d8c00-270">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="d8c00-270">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d8c00-271">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="d8c00-271">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="d8c00-272">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-272">Create a new project.</span></span>

1. <span data-ttu-id="d8c00-273">Selecione **Blazor aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-273">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="d8c00-274">Digite `BlazorServerSignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="d8c00-274">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="d8c00-275">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-275">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d8c00-276">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-276">Select **Create**.</span></span>

1. <span data-ttu-id="d8c00-277">Escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="d8c00-277">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="d8c00-278">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-278">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d8c00-280">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-280">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="d8c00-281">A `-o|--output` opção cria uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-281">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="d8c00-282">Se você tiver criado uma pasta para o projeto e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-282">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="d8c00-283">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8c00-283">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="d8c00-284">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-284">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="d8c00-285">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-285">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-286">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8c00-287">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="d8c00-287">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="d8c00-288">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-288">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d8c00-289">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="d8c00-289">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="d8c00-290">Escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="d8c00-290">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d8c00-291">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-291">Select **Next**.</span></span>

1. <span data-ttu-id="d8c00-292">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-292">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d8c00-293">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-293">Select **Next**.</span></span>

1. <span data-ttu-id="d8c00-294">No campo **nome do projeto** , nomeie o aplicativo `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-294">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="d8c00-295">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-295">Select **Create**.</span></span>

   <span data-ttu-id="d8c00-296">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="d8c00-296">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d8c00-297">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-297">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="d8c00-298">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="d8c00-298">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-299">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-299">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d8c00-300">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-300">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="d8c00-301">A `-o|--output` opção cria uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-301">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="d8c00-302">Se você tiver criado uma pasta para o projeto e o Shell de comando estiver aberto nessa pasta, omita a `-o|--output` opção e o valor para criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="d8c00-302">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="d8c00-303">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="d8c00-303">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-304">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-304">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d8c00-305">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-305">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d8c00-306">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-306">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d8c00-307">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d8c00-307">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d8c00-308">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="d8c00-308">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d8c00-309">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8c00-309">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d8c00-310">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-310">Select **Install**.</span></span>

1. <span data-ttu-id="d8c00-311">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-311">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d8c00-312">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="d8c00-312">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-313">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d8c00-314">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-314">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d8c00-315">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-315">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-316">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8c00-317">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-317">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d8c00-318">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-318">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d8c00-319">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d8c00-319">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d8c00-320">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote.</span><span class="sxs-lookup"><span data-stu-id="d8c00-320">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d8c00-321">Defina a versão para corresponder à estrutura compartilhada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8c00-321">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d8c00-322">Selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-322">Select **Add Package**.</span></span>

1. <span data-ttu-id="d8c00-323">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="d8c00-323">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-324">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-324">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d8c00-325">Em um shell de comando da pasta do projeto, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-325">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d8c00-326">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-326">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="d8c00-327">Adicione o pacote System. Text. transcodifications. Web</span><span class="sxs-lookup"><span data-stu-id="d8c00-327">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="d8c00-328">*Esta seção se aplica somente a aplicativos para ASP.NET Core versão 3. x.*</span><span class="sxs-lookup"><span data-stu-id="d8c00-328">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="d8c00-329">Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x em um aplicativo ASP.NET Core 3. x, o projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="d8c00-329">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="d8c00-330">O problema subjacente será resolvido em uma versão futura de patch do .NET 5.</span><span class="sxs-lookup"><span data-stu-id="d8c00-330">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="d8c00-331">Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="d8c00-331">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="d8c00-332">Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao projeto, siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="d8c00-332">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-333">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d8c00-334">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-334">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d8c00-335">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-335">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d8c00-336">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d8c00-336">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d8c00-337">Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote.</span><span class="sxs-lookup"><span data-stu-id="d8c00-337">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="d8c00-338">Selecione a versão do pacote que corresponde à estrutura compartilhada em uso.</span><span class="sxs-lookup"><span data-stu-id="d8c00-338">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="d8c00-339">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-339">Select **Install**.</span></span>

1. <span data-ttu-id="d8c00-340">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-340">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d8c00-341">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="d8c00-341">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-342">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-342">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d8c00-343">No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d8c00-343">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="d8c00-344">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-344">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-345">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-345">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8c00-346">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorServerSignalRApp` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-346">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d8c00-347">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d8c00-347">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d8c00-348">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d8c00-348">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d8c00-349">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote, selecione a versão correta do pacote que corresponde à estrutura compartilhada em uso e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="d8c00-349">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="d8c00-350">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="d8c00-350">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-351">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-351">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d8c00-352">Em um shell de comando da pasta do projeto, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8c00-352">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="d8c00-353">Para adicionar uma versão anterior do pacote, forneça a `--version {VERSION}` opção, em que o `{VERSION}` espaço reservado é a versão do pacote a ser adicionado.</span><span class="sxs-lookup"><span data-stu-id="d8c00-353">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="d8c00-354">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d8c00-354">Add a SignalR hub</span></span>

<span data-ttu-id="d8c00-355">Crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="d8c00-355">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="d8c00-356">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d8c00-356">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="d8c00-357">Abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="d8c00-357">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="d8c00-358">Adicione os namespaces para <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> e a `ChatHub` classe à parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="d8c00-358">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d8c00-359">Adicionar serviços de middleware de compactação de resposta a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d8c00-359">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="d8c00-360">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d8c00-360">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d8c00-361">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="d8c00-361">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d8c00-362">Entre os pontos de extremidade para mapear o Blazor Hub e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="d8c00-362">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d8c00-363">Adicionar serviços de middleware de compactação de resposta a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d8c00-363">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="d8c00-364">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d8c00-364">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d8c00-365">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="d8c00-365">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d8c00-366">Entre os pontos de extremidade para mapear o Blazor Hub e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="d8c00-366">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="d8c00-367">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="d8c00-367">Add Razor component code for chat</span></span>

1. <span data-ttu-id="d8c00-368">Abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="d8c00-368">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d8c00-369">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d8c00-369">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d8c00-370">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d8c00-370">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="d8c00-371">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d8c00-371">Run the app</span></span>

<span data-ttu-id="d8c00-372">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="d8c00-372">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8c00-373">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8c00-373">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d8c00-374">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="d8c00-374">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d8c00-375">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="d8c00-375">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d8c00-376">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d8c00-376">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d8c00-377">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="d8c00-377">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d8c00-379">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d8c00-379">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8c00-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8c00-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d8c00-381">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="d8c00-381">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d8c00-382">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="d8c00-382">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d8c00-383">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d8c00-383">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d8c00-384">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="d8c00-384">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d8c00-386">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d8c00-386">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8c00-387">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d8c00-387">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8c00-388">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="d8c00-388">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d8c00-389">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="d8c00-389">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d8c00-390">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d8c00-390">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d8c00-391">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="d8c00-391">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d8c00-393">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d8c00-393">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8c00-394">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d8c00-394">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d8c00-395">Em um shell de comando da pasta do projeto, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d8c00-395">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="d8c00-396">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="d8c00-396">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d8c00-397">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="d8c00-397">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d8c00-398">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="d8c00-398">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (mais alto)::: aplicativo de exemplo aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d8c00-400">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d8c00-400">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="d8c00-401">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="d8c00-401">Next steps</span></span>

<span data-ttu-id="d8c00-402">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="d8c00-402">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d8c00-403">Criar um Blazor projeto</span><span class="sxs-lookup"><span data-stu-id="d8c00-403">Create a Blazor project</span></span>
> * <span data-ttu-id="d8c00-404">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="d8c00-404">Add the SignalR client library</span></span>
> * <span data-ttu-id="d8c00-405">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d8c00-405">Add a SignalR hub</span></span>
> * <span data-ttu-id="d8c00-406">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d8c00-406">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d8c00-407">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="d8c00-407">Add Razor component code for chat</span></span>

<span data-ttu-id="d8c00-408">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="d8c00-408">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="d8c00-409"><xref:blazor/index>
> [Autenticação de token de portador com Identity eventos de servidor, WebSocket e Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="d8c00-409"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8c00-410">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d8c00-410">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="d8c00-411">SignalR negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="d8c00-411">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
