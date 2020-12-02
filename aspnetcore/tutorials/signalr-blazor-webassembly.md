---
title: Usar ASP.NET Core SignalR com um Blazor WebAssembly aplicativo hospedado
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 89aeb20d5566447ff86581dfa1d7946d20b9ed2d
ms.sourcegitcommit: 94c8cc1a8ce2bdba0ebdd9d37c155bf42d3cc62b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96473710"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="54cf9-103">Usar ASP.NET Core SignalR com um Blazor WebAssembly aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="54cf9-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="54cf9-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="54cf9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="54cf9-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="54cf9-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="54cf9-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="54cf9-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="54cf9-107">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="54cf9-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="54cf9-108">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="54cf9-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="54cf9-109">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="54cf9-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="54cf9-110">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="54cf9-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="54cf9-111">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="54cf9-111">Add Razor component code for chat</span></span>

<span data-ttu-id="54cf9-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="54cf9-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="54cf9-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54cf9-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54cf9-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="54cf9-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="54cf9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54cf9-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="54cf9-116">[Visual Studio 2019 16,8 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="54cf9-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="54cf9-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="54cf9-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="54cf9-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="54cf9-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="54cf9-119">Visual Studio para Mac versão 8,8 ou posterior</span><span class="sxs-lookup"><span data-stu-id="54cf9-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="54cf9-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="54cf9-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="54cf9-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54cf9-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="54cf9-122">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="54cf9-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="54cf9-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="54cf9-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="54cf9-124">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="54cf9-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="54cf9-125">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="54cf9-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="54cf9-126">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="54cf9-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="54cf9-127">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="54cf9-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="54cf9-128">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="54cf9-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54cf9-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54cf9-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="54cf9-130">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="54cf9-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="54cf9-131">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="54cf9-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="54cf9-132">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="54cf9-132">Create a new project.</span></span>

1. <span data-ttu-id="54cf9-133">Selecione **Blazor aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="54cf9-134">Digite `BlazorSignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="54cf9-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="54cf9-135">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="54cf9-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="54cf9-136">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-136">Select **Create**.</span></span>

1. <span data-ttu-id="54cf9-137">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="54cf9-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="54cf9-138">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="54cf9-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="54cf9-139">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="54cf9-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="54cf9-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="54cf9-141">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="54cf9-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="54cf9-142">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54cf9-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="54cf9-143">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="54cf9-144">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="54cf9-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="54cf9-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="54cf9-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="54cf9-146">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="54cf9-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="54cf9-147">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="54cf9-148">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="54cf9-149">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="54cf9-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="54cf9-150">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-150">Select **Next**.</span></span>

1. <span data-ttu-id="54cf9-151">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="54cf9-152">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="54cf9-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="54cf9-153">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-153">Select **Next**.</span></span>

1. <span data-ttu-id="54cf9-154">No campo **nome do projeto** , nomeie o aplicativo `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="54cf9-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="54cf9-155">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-155">Select **Create**.</span></span>

   <span data-ttu-id="54cf9-156">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="54cf9-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="54cf9-157">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="54cf9-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="54cf9-158">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="54cf9-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="54cf9-159">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="54cf9-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="54cf9-160">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="54cf9-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="54cf9-161">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="54cf9-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54cf9-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54cf9-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="54cf9-163">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="54cf9-164">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="54cf9-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="54cf9-165">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="54cf9-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="54cf9-166">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="54cf9-167">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="54cf9-168">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="54cf9-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="54cf9-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="54cf9-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="54cf9-170">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="54cf9-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="54cf9-171">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="54cf9-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="54cf9-172">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="54cf9-173">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="54cf9-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="54cf9-174">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="54cf9-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="54cf9-175">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="54cf9-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="54cf9-176">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="54cf9-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="54cf9-177">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="54cf9-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="54cf9-178">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="54cf9-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="54cf9-179">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="54cf9-179">Add a SignalR hub</span></span>

<span data-ttu-id="54cf9-180">No `BlazorSignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="54cf9-180">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="54cf9-181">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="54cf9-181">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="54cf9-182">No projeto `BlazorSignalRApp.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="54cf9-182">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="54cf9-183">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="54cf9-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="54cf9-184">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="54cf9-184">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="54cf9-185">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="54cf9-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="54cf9-186">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="54cf9-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="54cf9-187">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="54cf9-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="54cf9-188">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="54cf9-188">Add Razor component code for chat</span></span>

1. <span data-ttu-id="54cf9-189">No projeto `BlazorSignalRApp.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="54cf9-189">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="54cf9-190">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="54cf9-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="54cf9-191">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="54cf9-191">Run the app</span></span>

1. <span data-ttu-id="54cf9-192">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="54cf9-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54cf9-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54cf9-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="54cf9-194">Em **Gerenciador de soluções**, selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="54cf9-194">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="54cf9-195">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="54cf9-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="54cf9-196">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="54cf9-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="54cf9-197">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="54cf9-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="54cf9-198">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="54cf9-198">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="54cf9-200">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="54cf9-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="54cf9-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="54cf9-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="54cf9-202">Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor ( `.vscode/launch.json` ), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="54cf9-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="54cf9-203">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="54cf9-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="54cf9-204">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="54cf9-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="54cf9-205">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="54cf9-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="54cf9-206">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="54cf9-206">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="54cf9-208">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="54cf9-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="54cf9-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="54cf9-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="54cf9-210">Na barra lateral da **solução** , selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="54cf9-210">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="54cf9-211">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="54cf9-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="54cf9-212">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="54cf9-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="54cf9-213">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="54cf9-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="54cf9-214">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="54cf9-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="54cf9-216">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="54cf9-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="54cf9-217">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="54cf9-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="54cf9-218">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="54cf9-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="54cf9-219">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="54cf9-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="54cf9-220">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="54cf9-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="54cf9-221">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="54cf9-221">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="54cf9-223">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="54cf9-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="54cf9-224">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="54cf9-224">Next steps</span></span>

<span data-ttu-id="54cf9-225">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="54cf9-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="54cf9-226">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="54cf9-226">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="54cf9-227">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="54cf9-227">Add the SignalR client library</span></span>
> * <span data-ttu-id="54cf9-228">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="54cf9-228">Add a SignalR hub</span></span>
> * <span data-ttu-id="54cf9-229">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="54cf9-229">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="54cf9-230">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="54cf9-230">Add Razor component code for chat</span></span>

<span data-ttu-id="54cf9-231">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="54cf9-231">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="54cf9-232"><xref:blazor/index>
> [Autenticação de token de portador com Identity eventos de servidor, WebSocket e Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="54cf9-232"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54cf9-233">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="54cf9-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="54cf9-234">SignalR negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="54cf9-234">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
