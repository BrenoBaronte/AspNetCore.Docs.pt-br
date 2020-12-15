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
ms.openlocfilehash: 29f1a1f211688a1edcd31c7230e7216df7c89eef
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506806"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="c38c3-103">Ferramentas para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c38c3-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="c38c3-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c38c3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="c38c3-105">Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="c38c3-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="c38c3-106">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="c38c3-106">Create a new project.</span></span>

1. <span data-ttu-id="c38c3-107">Selecione **Blazor aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-107">Select **Blazor App**.</span></span> <span data-ttu-id="c38c3-108">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-108">Select **Next**.</span></span>

1. <span data-ttu-id="c38c3-109">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="c38c3-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="c38c3-110">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="c38c3-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="c38c3-111">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-111">Select **Create**.</span></span>

1. <span data-ttu-id="c38c3-112">Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="c38c3-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="c38c3-113">Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="c38c3-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="c38c3-114">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-114">Select **Create**.</span></span>

   <span data-ttu-id="c38c3-115">Para uma experiência hospedada Blazor WebAssembly , marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="c38c3-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="c38c3-116">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="c38c3-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="c38c3-117">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c38c3-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="c38c3-118">Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="c38c3-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="c38c3-119">Instale a versão mais recente do [SDK do .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="c38c3-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="c38c3-120">Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="c38c3-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="c38c3-121">Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="c38c3-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="c38c3-122">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="c38c3-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="c38c3-123">Para obter uma Blazor WebAssembly experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="c38c3-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="c38c3-124">Para uma experiência hospedada Blazor WebAssembly , adicione a opção Hosted ( `-ho` ou `--hosted` ) ao comando:</span><span class="sxs-lookup"><span data-stu-id="c38c3-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="c38c3-125">Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="c38c3-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="c38c3-126">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="c38c3-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="c38c3-127">Abra a pasta `WebApplication1` no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c38c3-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="c38c3-128">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="c38c3-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="c38c3-129">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="c38c3-129">Select **Yes**.</span></span>

1. <span data-ttu-id="c38c3-130">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c38c3-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="c38c3-131">Confiar em um certificado de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="c38c3-131">Trust a development certificate</span></span>

<span data-ttu-id="c38c3-132">Não há uma maneira centralizada de confiar em um certificado no Linux.</span><span class="sxs-lookup"><span data-stu-id="c38c3-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="c38c3-133">Normalmente, uma das seguintes abordagens é adotada:</span><span class="sxs-lookup"><span data-stu-id="c38c3-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="c38c3-134">Exclua a URL do aplicativo na lista de exclusões do navegador.</span><span class="sxs-lookup"><span data-stu-id="c38c3-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="c38c3-135">Confiar em todos os certificados autoassinados para `localhost` .</span><span class="sxs-lookup"><span data-stu-id="c38c3-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="c38c3-136">Adicione o certificado à lista de certificados confiáveis no navegador.</span><span class="sxs-lookup"><span data-stu-id="c38c3-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="c38c3-137">Para obter mais informações, consulte a orientação fornecida pelo fabricante do navegador e distribuição do Linux.</span><span class="sxs-lookup"><span data-stu-id="c38c3-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="c38c3-138">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="c38c3-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="c38c3-139">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="c38c3-140">Na barra lateral, selecione **aplicativo Web e de console**  >  .</span><span class="sxs-lookup"><span data-stu-id="c38c3-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="c38c3-141">Para obter uma Blazor WebAssembly experiência, escolha o modelo de **Blazor WebAssembly aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="c38c3-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="c38c3-142">Para obter uma Blazor Server experiência, escolha o modelo de **Blazor Server aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="c38c3-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="c38c3-143">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-143">Select **Next**.</span></span>

   <span data-ttu-id="c38c3-144">Para obter informações sobre os dois Blazor modelos de hospedagem, *Blazor WebAssembly* (autônomo e hospedado) e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="c38c3-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="c38c3-145">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="c38c3-146">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-146">Select **Next**.</span></span>

1. <span data-ttu-id="c38c3-147">Para uma experiência hospedada Blazor WebAssembly , marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="c38c3-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="c38c3-148">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="c38c3-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="c38c3-149">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c38c3-149">Select **Create**.</span></span>

1. <span data-ttu-id="c38c3-150">Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="c38c3-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="c38c3-151">Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="c38c3-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="c38c3-152">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="c38c3-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="c38c3-153">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="c38c3-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="c38c3-154">Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="c38c3-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="c38c3-155">Usar Visual Studio Code para o desenvolvimento de plataforma cruzada Blazor</span><span class="sxs-lookup"><span data-stu-id="c38c3-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="c38c3-156">O [Visual Studio Code](https://code.visualstudio.com/) é um IDE (ambiente de desenvolvimento integrado) de plataforma cruzada de software livre que pode ser usado para desenvolver Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c38c3-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="c38c3-157">Use a CLI do .NET para criar um novo Blazor aplicativo para desenvolvimento com o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c38c3-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="c38c3-158">Para obter mais informações, consulte a [versão do Linux deste artigo](/aspnet/core/blazor/tooling?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="c38c3-158">For more information, see the [Linux version of this article](/aspnet/core/blazor/tooling?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="c38c3-159">Blazor opções de modelo</span><span class="sxs-lookup"><span data-stu-id="c38c3-159">Blazor template options</span></span>

<span data-ttu-id="c38c3-160">A Blazor estrutura fornece modelos para a criação de novos aplicativos para cada um dos dois Blazor modelos de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c38c3-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="c38c3-161">Os modelos são usados para criar novos Blazor projetos e soluções, independentemente das ferramentas que você selecionar para Blazor desenvolvimento (Visual Studio, Visual Studio para Mac, Visual Studio Code ou a CLI do .net):</span><span class="sxs-lookup"><span data-stu-id="c38c3-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="c38c3-162">Blazor WebAssembly modelo de projeto: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="c38c3-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="c38c3-163">Blazor Server modelo de projeto: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="c38c3-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="c38c3-164">Para obter mais informações sobre os Blazor modelos de hospedagem do, consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="c38c3-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="c38c3-165">As opções de modelo estão disponíveis passando a opção de ajuda ( `-h` ou `--help` ) para o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando da CLI em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="c38c3-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm --h
dotnet new blazorserver --h
```
