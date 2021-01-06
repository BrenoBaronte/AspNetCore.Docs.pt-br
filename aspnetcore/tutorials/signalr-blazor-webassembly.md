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
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797347"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="44a1e-103">Usar ASP.NET Core SignalR com um Blazor WebAssembly aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="44a1e-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="44a1e-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="44a1e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="44a1e-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR com o Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="44a1e-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="44a1e-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="44a1e-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="44a1e-107">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="44a1e-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="44a1e-108">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="44a1e-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="44a1e-109">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="44a1e-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="44a1e-110">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="44a1e-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="44a1e-111">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="44a1e-111">Add Razor component code for chat</span></span>

<span data-ttu-id="44a1e-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="44a1e-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="44a1e-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44a1e-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="44a1e-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="44a1e-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="44a1e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a1e-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44a1e-116">[Visual Studio 2019 16,8 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="44a1e-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="44a1e-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44a1e-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44a1e-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44a1e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="44a1e-119">Visual Studio para Mac versão 8,8 ou posterior</span><span class="sxs-lookup"><span data-stu-id="44a1e-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="44a1e-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="44a1e-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="44a1e-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a1e-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44a1e-122">[Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="44a1e-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="44a1e-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44a1e-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44a1e-124">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44a1e-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="44a1e-125">Visual Studio para Mac versão 8,6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="44a1e-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="44a1e-126">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="44a1e-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="44a1e-127">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="44a1e-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="44a1e-128">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="44a1e-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44a1e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a1e-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="44a1e-130">O Visual Studio 16,8 ou posterior e o SDK do .NET Core 5.0.0 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="44a1e-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="44a1e-131">O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.</span><span class="sxs-lookup"><span data-stu-id="44a1e-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="44a1e-132">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="44a1e-132">Create a new project.</span></span>

1. <span data-ttu-id="44a1e-133">Selecione **Blazor aplicativo** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="44a1e-134">Digite `BlazorSignalRApp` o campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="44a1e-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="44a1e-135">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="44a1e-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="44a1e-136">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-136">Select **Create**.</span></span>

1. <span data-ttu-id="44a1e-137">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="44a1e-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="44a1e-138">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="44a1e-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="44a1e-139">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="44a1e-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44a1e-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="44a1e-141">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="44a1e-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="44a1e-142">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="44a1e-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="44a1e-143">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="44a1e-144">Visual Studio Code adiciona automaticamente a `.vscode` pasta com `launch.json` arquivos gerados e `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="44a1e-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44a1e-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44a1e-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="44a1e-146">Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="44a1e-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="44a1e-147">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="44a1e-148">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="44a1e-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="44a1e-149">Escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="44a1e-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="44a1e-150">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-150">Select **Next**.</span></span>

1. <span data-ttu-id="44a1e-151">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="44a1e-152">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="44a1e-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="44a1e-153">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-153">Select **Next**.</span></span>

1. <span data-ttu-id="44a1e-154">No campo **nome do projeto** , nomeie o aplicativo `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="44a1e-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="44a1e-155">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-155">Select **Create**.</span></span>

   <span data-ttu-id="44a1e-156">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="44a1e-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="44a1e-157">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="44a1e-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="44a1e-158">Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="44a1e-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="44a1e-159">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="44a1e-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="44a1e-160">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="44a1e-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="44a1e-161">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="44a1e-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44a1e-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a1e-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="44a1e-163">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="44a1e-164">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="44a1e-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="44a1e-165">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="44a1e-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="44a1e-166">Nos resultados da pesquisa, selecione o [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="44a1e-167">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="44a1e-168">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="44a1e-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="44a1e-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44a1e-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="44a1e-170">No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="44a1e-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44a1e-171">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44a1e-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="44a1e-172">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorSignalRApp.Client` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="44a1e-173">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="44a1e-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="44a1e-174">Com **procurar** selecionado, digite `Microsoft.AspNetCore.SignalR.Client` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="44a1e-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="44a1e-175">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacote e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="44a1e-176">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="44a1e-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="44a1e-177">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="44a1e-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="44a1e-178">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="44a1e-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="44a1e-179">Adicione o pacote System. Text. transcodifications. Web</span><span class="sxs-lookup"><span data-stu-id="44a1e-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="44a1e-180">Devido a um problema de resolução de pacote ao usar o [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 em um aplicativo ASP.NET Core 3,1, o `BlazorSignalRApp.Server` projeto requer uma referência de pacote para o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="44a1e-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="44a1e-181">O problema subjacente será resolvido em uma versão futura de patch do .NET 5.</span><span class="sxs-lookup"><span data-stu-id="44a1e-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="44a1e-182">Para obter mais informações, consulte [System.Text.Jsem define netcoreapp 3.0 sem dependências (dotNet/tempo de execução #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="44a1e-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="44a1e-183">Para adicionar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ao `BlazorSignalRApp.Server` projeto da solução ASP.NET Core 3,1 hospedada Blazor , siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="44a1e-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44a1e-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a1e-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="44a1e-185">Em **Gerenciador de soluções**, clique com o botão direito do mouse no `BlazorSignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="44a1e-186">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="44a1e-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="44a1e-187">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="44a1e-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="44a1e-188">Nos resultados da pesquisa, selecione o [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="44a1e-189">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="44a1e-190">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="44a1e-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="44a1e-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44a1e-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="44a1e-192">No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="44a1e-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44a1e-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44a1e-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="44a1e-194">Na barra lateral da **solução** , clique com o botão direito do mouse no `BlazorSignalRApp.Server` projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="44a1e-195">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="44a1e-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="44a1e-196">Com **procurar** selecionado, digite `System.Text.Encodings.Web` na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="44a1e-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="44a1e-197">Nos resultados da pesquisa, marque a caixa de seleção ao lado do [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pacote e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="44a1e-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="44a1e-198">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="44a1e-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="44a1e-199">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="44a1e-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="44a1e-200">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="44a1e-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="44a1e-201">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="44a1e-201">Add a SignalR hub</span></span>

<span data-ttu-id="44a1e-202">No `BlazorSignalRApp.Server` projeto, crie uma `Hubs` pasta (plural) e adicione a seguinte `ChatHub` classe ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="44a1e-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="44a1e-203">Adicionar serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="44a1e-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="44a1e-204">No projeto `BlazorSignalRApp.Server`, abra o arquivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="44a1e-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="44a1e-205">Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="44a1e-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="44a1e-206">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44a1e-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="44a1e-207">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="44a1e-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="44a1e-208">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="44a1e-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="44a1e-209">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="44a1e-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="44a1e-210">Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44a1e-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="44a1e-211">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="44a1e-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="44a1e-212">Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.</span><span class="sxs-lookup"><span data-stu-id="44a1e-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="44a1e-213">Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.</span><span class="sxs-lookup"><span data-stu-id="44a1e-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="44a1e-214">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="44a1e-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="44a1e-215">No projeto `BlazorSignalRApp.Client`, abra o arquivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="44a1e-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="44a1e-216">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="44a1e-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="44a1e-217">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="44a1e-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="44a1e-218">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="44a1e-218">Run the app</span></span>

<span data-ttu-id="44a1e-219">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="44a1e-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44a1e-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a1e-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="44a1e-221">Em **Gerenciador de soluções**, selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="44a1e-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="44a1e-222">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="44a1e-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="44a1e-223">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="44a1e-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="44a1e-224">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="44a1e-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="44a1e-225">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="44a1e-225">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="44a1e-227">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="44a1e-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="44a1e-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44a1e-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="44a1e-229">Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor ( `.vscode/launch.json` ), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="44a1e-229">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="44a1e-230">Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor ( `.vscode/launch.json` ), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="44a1e-230">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="44a1e-231">Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="44a1e-231">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="44a1e-232">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="44a1e-232">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="44a1e-233">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="44a1e-233">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="44a1e-234">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="44a1e-234">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="44a1e-236">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="44a1e-236">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44a1e-237">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="44a1e-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="44a1e-238">Na barra lateral da **solução** , selecione o `BlazorSignalRApp.Server` projeto.</span><span class="sxs-lookup"><span data-stu-id="44a1e-238">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="44a1e-239">Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="44a1e-239">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="44a1e-240">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="44a1e-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="44a1e-241">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="44a1e-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="44a1e-242">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="44a1e-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="44a1e-244">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="44a1e-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="44a1e-245">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="44a1e-245">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="44a1e-246">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="44a1e-246">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="44a1e-247">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="44a1e-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="44a1e-248">Escolha o navegador, insira um nome e uma mensagem e selecione o botão para enviar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="44a1e-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="44a1e-249">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="44a1e-249">The name and message are displayed on both pages instantly:</span></span>

   ![::: no-Loc (Signalr):::::: no-Loc (Webassembly do mais alto)::: aplicativo de exemplo aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="44a1e-251">Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="44a1e-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="44a1e-252">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="44a1e-252">Next steps</span></span>

<span data-ttu-id="44a1e-253">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="44a1e-253">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="44a1e-254">Criar um Blazor WebAssembly projeto de aplicativo hospedado</span><span class="sxs-lookup"><span data-stu-id="44a1e-254">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="44a1e-255">Adicionar a SignalR biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="44a1e-255">Add the SignalR client library</span></span>
> * <span data-ttu-id="44a1e-256">Adicionar um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="44a1e-256">Add a SignalR hub</span></span>
> * <span data-ttu-id="44a1e-257">Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="44a1e-257">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="44a1e-258">Adicionar Razor código de componente para o chat</span><span class="sxs-lookup"><span data-stu-id="44a1e-258">Add Razor component code for chat</span></span>

<span data-ttu-id="44a1e-259">Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:</span><span class="sxs-lookup"><span data-stu-id="44a1e-259">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="44a1e-260"><xref:blazor/index>
> [Autenticação de token de portador com Identity eventos de servidor, WebSocket e Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="44a1e-260"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44a1e-261">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="44a1e-261">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="44a1e-262">SignalR negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="44a1e-262">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
