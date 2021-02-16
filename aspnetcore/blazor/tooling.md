---
title: Ferramentas para ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre as ferramentas disponíveis para criar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 6b61d9a4645d273b0c78fae0388d569771c43a2d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536240"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="0b8de-103">Ferramentas para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="0b8de-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="0b8de-104">Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="0b8de-105">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="0b8de-105">Create a new project.</span></span>

1. <span data-ttu-id="0b8de-106">Selecione **Blazor aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-106">Select **Blazor App**.</span></span> <span data-ttu-id="0b8de-107">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-107">Select **Next**.</span></span>

1. <span data-ttu-id="0b8de-108">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="0b8de-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="0b8de-109">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="0b8de-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0b8de-110">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-110">Select **Create**.</span></span>

1. <span data-ttu-id="0b8de-111">Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0b8de-112">Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="0b8de-113">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-113">Select **Create**.</span></span>

   <span data-ttu-id="0b8de-114">Para uma experiência hospedada Blazor WebAssembly , marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="0b8de-115">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0b8de-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0b8de-116">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0b8de-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="0b8de-117">Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="0b8de-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="0b8de-118">Ao executar um Blazor WebAssembly aplicativo hospedado, execute o aplicativo do projeto da solução **`Server`** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="0b8de-119">Instale a versão mais recente do [SDK do .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="0b8de-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="0b8de-120">Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="0b8de-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="0b8de-121">Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="0b8de-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="0b8de-122">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="0b8de-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="0b8de-123">Para obter uma Blazor WebAssembly experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="0b8de-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="0b8de-124">Para uma experiência hospedada Blazor WebAssembly , adicione a opção Hosted ( `-ho` ou `--hosted` ) ao comando:</span><span class="sxs-lookup"><span data-stu-id="0b8de-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="0b8de-125">Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="0b8de-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="0b8de-126">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0b8de-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0b8de-127">Abra a pasta `WebApplication1` no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0b8de-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="0b8de-128">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="0b8de-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="0b8de-129">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-129">Select **Yes**.</span></span>

   <span data-ttu-id="0b8de-130">**Blazor WebAssemblyConfiguração de tarefa e inicialização hospedada**</span><span class="sxs-lookup"><span data-stu-id="0b8de-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="0b8de-131">Para soluções hospedadas Blazor WebAssembly , adicione (ou mova) a `.vscode` pasta com `launch.json` e `tasks.json` arquivos para a pasta pai da solução, que é a pasta que contém os nomes de pasta de projeto típicos de `Client` , `Server` e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="0b8de-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="0b8de-132">Atualize ou confirme se a configuração nos `launch.json` arquivos e `tasks.json` executa um aplicativo hospedado Blazor WebAssembly do **`Server`** projeto.</span><span class="sxs-lookup"><span data-stu-id="0b8de-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="0b8de-133">**`.vscode/launch.json`** ( `launch` configuração):</span><span class="sxs-lookup"><span data-stu-id="0b8de-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="0b8de-134">Na configuração anterior para o diretório de trabalho atual ( `cwd` ), o `{SERVER APP FOLDER}` espaço reservado é a **`Server`** pasta do projeto, normalmente " `Server` ".</span><span class="sxs-lookup"><span data-stu-id="0b8de-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="0b8de-135">Se o Microsoft Edge for usado e o Google Chrome não estiver instalado no sistema, adicione uma propriedade adicional de `"browser": "edge"` à configuração.</span><span class="sxs-lookup"><span data-stu-id="0b8de-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="0b8de-136">Exemplo de uma pasta de projeto do `Server` e que gera o Microsoft Edge como o navegador para execuções de depuração em vez do navegador padrão Google Chrome:</span><span class="sxs-lookup"><span data-stu-id="0b8de-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="0b8de-137">**`.vscode/tasks.json`**(argumentos de [ `dotnet` comando](/dotnet/core/tools/dotnet) ):</span><span class="sxs-lookup"><span data-stu-id="0b8de-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="0b8de-138">No argumento anterior:</span><span class="sxs-lookup"><span data-stu-id="0b8de-138">In the preceding argument:</span></span>

   * <span data-ttu-id="0b8de-139">O `{SERVER APP FOLDER}` espaço reservado é a **`Server`** pasta do projeto, normalmente " `Server` ".</span><span class="sxs-lookup"><span data-stu-id="0b8de-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="0b8de-140">O `{PROJECT NAME}` espaço reservado é o nome do aplicativo, normalmente com base no nome da solução seguido por " `.Server` " em um aplicativo gerado a partir do Blazor modelo de projeto.</span><span class="sxs-lookup"><span data-stu-id="0b8de-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the Blazor project template.</span></span>

   <span data-ttu-id="0b8de-141">O exemplo a seguir do [tutorial para usar SignalR com um Blazor WebAssembly aplicativo](xref:tutorials/signalr-blazor) usa um nome de pasta de projeto de `Server` e um nome de projeto de `BlazorWebAssemblySignalRApp.Server` :</span><span class="sxs-lookup"><span data-stu-id="0b8de-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="0b8de-142">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0b8de-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="0b8de-143">Confiar em um certificado de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="0b8de-143">Trust a development certificate</span></span>

<span data-ttu-id="0b8de-144">Não há uma maneira centralizada de confiar em um certificado no Linux.</span><span class="sxs-lookup"><span data-stu-id="0b8de-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="0b8de-145">Normalmente, uma das seguintes abordagens é adotada:</span><span class="sxs-lookup"><span data-stu-id="0b8de-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="0b8de-146">Exclua a URL do aplicativo na lista de exclusões do navegador.</span><span class="sxs-lookup"><span data-stu-id="0b8de-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="0b8de-147">Confiar em todos os certificados autoassinados para `localhost` .</span><span class="sxs-lookup"><span data-stu-id="0b8de-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="0b8de-148">Adicione o certificado à lista de certificados confiáveis no navegador.</span><span class="sxs-lookup"><span data-stu-id="0b8de-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="0b8de-149">Para obter mais informações, consulte a orientação fornecida pelo fabricante do navegador e distribuição do Linux.</span><span class="sxs-lookup"><span data-stu-id="0b8de-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="0b8de-150">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="0b8de-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="0b8de-151">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="0b8de-152">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="0b8de-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="0b8de-153">Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0b8de-154">Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="0b8de-155">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-155">Select **Next**.</span></span>

   <span data-ttu-id="0b8de-156">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0b8de-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0b8de-157">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="0b8de-158">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-158">Select **Next**.</span></span>

1. <span data-ttu-id="0b8de-159">Para uma experiência hospedada Blazor WebAssembly , marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="0b8de-160">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="0b8de-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="0b8de-161">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0b8de-161">Select **Create**.</span></span>

1. <span data-ttu-id="0b8de-162">Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="0b8de-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="0b8de-163">Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="0b8de-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="0b8de-164">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="0b8de-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="0b8de-165">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="0b8de-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="0b8de-166">Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="0b8de-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="0b8de-167">Ao executar um Blazor WebAssembly aplicativo hospedado, execute o aplicativo do projeto da solução **`Server`** .</span><span class="sxs-lookup"><span data-stu-id="0b8de-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="0b8de-168">Usar Visual Studio Code para o desenvolvimento de plataforma cruzada Blazor</span><span class="sxs-lookup"><span data-stu-id="0b8de-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="0b8de-169">O [Visual Studio Code](https://code.visualstudio.com/) é um IDE (ambiente de desenvolvimento integrado) de plataforma cruzada de software livre que pode ser usado para desenvolver Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0b8de-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="0b8de-170">Use a CLI do .NET para criar um novo Blazor aplicativo para desenvolvimento com o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0b8de-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="0b8de-171">Para obter mais informações, consulte a [versão do Linux deste artigo](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="0b8de-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="0b8de-172">Blazor opções de modelo</span><span class="sxs-lookup"><span data-stu-id="0b8de-172">Blazor template options</span></span>

<span data-ttu-id="0b8de-173">A Blazor estrutura fornece modelos para a criação de novos aplicativos para cada um dos dois Blazor modelos de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="0b8de-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="0b8de-174">Os modelos são usados para criar novos Blazor projetos e soluções, independentemente das ferramentas que você selecionar para Blazor desenvolvimento (Visual Studio, Visual Studio para Mac, Visual Studio Code ou a CLI do .net):</span><span class="sxs-lookup"><span data-stu-id="0b8de-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="0b8de-175">Blazor WebAssembly modelo de projeto: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="0b8de-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="0b8de-176">Blazor Server modelo de projeto: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="0b8de-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="0b8de-177">Para obter mais informações sobre os Blazor modelos de hospedagem do, consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0b8de-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="0b8de-178">As opções de modelo estão disponíveis passando a opção de ajuda ( `-h` ou `--help` ) para o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando da CLI em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="0b8de-178">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
