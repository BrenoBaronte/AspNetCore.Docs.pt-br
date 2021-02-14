---
title: Ferramentas para ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre as ferramentas disponíveis para criar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
ms.openlocfilehash: a17b16563ac12d634e6bdc32638991f45e2a66d5
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280677"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="3db70-103">Ferramentas para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="3db70-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="3db70-104">Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="3db70-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="3db70-105">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="3db70-105">Create a new project.</span></span>

1. <span data-ttu-id="3db70-106">Selecione **Blazor aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3db70-106">Select **Blazor App**.</span></span> <span data-ttu-id="3db70-107">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3db70-107">Select **Next**.</span></span>

1. <span data-ttu-id="3db70-108">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="3db70-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3db70-109">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="3db70-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3db70-110">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3db70-110">Select **Create**.</span></span>

1. <span data-ttu-id="3db70-111">Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="3db70-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3db70-112">Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="3db70-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3db70-113">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3db70-113">Select **Create**.</span></span>

   <span data-ttu-id="3db70-114">Para uma experiência hospedada Blazor WebAssembly , marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="3db70-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="3db70-115">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3db70-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3db70-116">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3db70-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="3db70-117">Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="3db70-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="3db70-118">Instale a versão mais recente do [SDK do .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="3db70-118">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="3db70-119">Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3db70-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="3db70-120">Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="3db70-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="3db70-121">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="3db70-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="3db70-122">Para obter uma Blazor WebAssembly experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3db70-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="3db70-123">Para uma experiência hospedada Blazor WebAssembly , adicione a opção Hosted ( `-ho` ou `--hosted` ) ao comando:</span><span class="sxs-lookup"><span data-stu-id="3db70-123">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="3db70-124">Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3db70-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="3db70-125">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3db70-125">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3db70-126">Abra a pasta `WebApplication1` no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3db70-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="3db70-127">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="3db70-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3db70-128">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="3db70-128">Select **Yes**.</span></span>

1. <span data-ttu-id="3db70-129">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3db70-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="3db70-130">Confiar em um certificado de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="3db70-130">Trust a development certificate</span></span>

<span data-ttu-id="3db70-131">Não há uma maneira centralizada de confiar em um certificado no Linux.</span><span class="sxs-lookup"><span data-stu-id="3db70-131">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="3db70-132">Normalmente, uma das seguintes abordagens é adotada:</span><span class="sxs-lookup"><span data-stu-id="3db70-132">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="3db70-133">Exclua a URL do aplicativo na lista de exclusões do navegador.</span><span class="sxs-lookup"><span data-stu-id="3db70-133">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="3db70-134">Confiar em todos os certificados autoassinados para `localhost` .</span><span class="sxs-lookup"><span data-stu-id="3db70-134">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="3db70-135">Adicione o certificado à lista de certificados confiáveis no navegador.</span><span class="sxs-lookup"><span data-stu-id="3db70-135">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="3db70-136">Para obter mais informações, consulte a orientação fornecida pelo fabricante do navegador e distribuição do Linux.</span><span class="sxs-lookup"><span data-stu-id="3db70-136">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="3db70-137">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="3db70-137">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="3db70-138">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="3db70-138">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3db70-139">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="3db70-139">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="3db70-140">Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="3db70-140">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3db70-141">Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="3db70-141">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3db70-142">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3db70-142">Select **Next**.</span></span>

   <span data-ttu-id="3db70-143">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3db70-143">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3db70-144">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="3db70-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="3db70-145">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3db70-145">Select **Next**.</span></span>

1. <span data-ttu-id="3db70-146">Para uma experiência hospedada Blazor WebAssembly , marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="3db70-146">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="3db70-147">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="3db70-147">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3db70-148">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3db70-148">Select **Create**.</span></span>

1. <span data-ttu-id="3db70-149">Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="3db70-149">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3db70-150">Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="3db70-150">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="3db70-151">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="3db70-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3db70-152">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="3db70-152">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="3db70-153">Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="3db70-153">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="3db70-154">Usar Visual Studio Code para o desenvolvimento de plataforma cruzada Blazor</span><span class="sxs-lookup"><span data-stu-id="3db70-154">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="3db70-155">O [Visual Studio Code](https://code.visualstudio.com/) é um IDE (ambiente de desenvolvimento integrado) de plataforma cruzada de software livre que pode ser usado para desenvolver Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="3db70-155">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="3db70-156">Use a CLI do .NET para criar um novo Blazor aplicativo para desenvolvimento com o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3db70-156">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="3db70-157">Para obter mais informações, consulte a [versão do Linux deste artigo](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="3db70-157">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="3db70-158">Blazor opções de modelo</span><span class="sxs-lookup"><span data-stu-id="3db70-158">Blazor template options</span></span>

<span data-ttu-id="3db70-159">A Blazor estrutura fornece modelos para a criação de novos aplicativos para cada um dos dois Blazor modelos de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="3db70-159">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="3db70-160">Os modelos são usados para criar novos Blazor projetos e soluções, independentemente das ferramentas que você selecionar para Blazor desenvolvimento (Visual Studio, Visual Studio para Mac, Visual Studio Code ou a CLI do .net):</span><span class="sxs-lookup"><span data-stu-id="3db70-160">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="3db70-161">Blazor WebAssembly modelo de projeto: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="3db70-161">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="3db70-162">Blazor Server modelo de projeto: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="3db70-162">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="3db70-163">Para obter mais informações sobre os Blazor modelos de hospedagem do, consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3db70-163">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="3db70-164">As opções de modelo estão disponíveis passando a opção de ajuda ( `-h` ou `--help` ) para o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando da CLI em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3db70-164">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```