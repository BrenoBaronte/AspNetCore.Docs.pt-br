---
title: Escolher entre o ASP.NET 4.x e o ASP.NET Core
author: rick-anderson
description: Explica ASP.NET Core vs. ASP.NET 4. x e como escolher entre eles.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: e0b0311622a841183433363a55fcf33e22b6b115
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633468"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="933bf-103">Escolher entre o ASP.NET 4.x e o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="933bf-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="933bf-104">O ASP.NET Core é uma reformulação do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="933bf-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="933bf-105">Este artigo lista as diferenças entre eles.</span><span class="sxs-lookup"><span data-stu-id="933bf-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="933bf-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="933bf-106">ASP.NET Core</span></span>

<span data-ttu-id="933bf-107">O ASP.NET Core é uma estrutura de software livre, multiplataforma, para a criação de aplicativos Web modernos e baseados em nuvem, no Windows, no macOS ou no Linux.</span><span class="sxs-lookup"><span data-stu-id="933bf-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="933bf-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="933bf-108">ASP.NET 4.x</span></span>

<span data-ttu-id="933bf-109">O ASP.NET 4.x é uma estrutura consolidada que fornece os serviços necessários para criar aplicativos Web baseados em servidor, de nível empresarial, no Windows.</span><span class="sxs-lookup"><span data-stu-id="933bf-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="933bf-110">Seleção de estrutura</span><span class="sxs-lookup"><span data-stu-id="933bf-110">Framework selection</span></span>

<span data-ttu-id="933bf-111">A tabela a seguir compara o ASP.NET Core com o ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="933bf-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="933bf-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="933bf-112">ASP.NET Core</span></span> | <span data-ttu-id="933bf-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="933bf-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="933bf-114">Build para Windows, macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="933bf-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="933bf-115">Build para Windows</span><span class="sxs-lookup"><span data-stu-id="933bf-115">Build for Windows</span></span>|
|<span data-ttu-id="933bf-116">[ Razor Páginas](xref:razor-pages/index) é a abordagem recomendada para criar uma interface do usuário da Web a partir do ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="933bf-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="933bf-117">Consulte também [MVC](xref:mvc/overview), [API da Web](xref:tutorials/first-web-api)e [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="933bf-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="933bf-118">Usar [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr) , [MVC](/aspnet/mvc), [API da Web](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/)ou [páginas da Web](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="933bf-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="933bf-119">Várias versões por computador</span><span class="sxs-lookup"><span data-stu-id="933bf-119">Multiple versions per machine</span></span>|<span data-ttu-id="933bf-120">Uma versão por computador</span><span class="sxs-lookup"><span data-stu-id="933bf-120">One version per machine</span></span>|
|<span data-ttu-id="933bf-121">Desenvolva com o [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) ou [Visual Studio Code](https://code.visualstudio.com/) usando C# ou F#</span><span class="sxs-lookup"><span data-stu-id="933bf-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="933bf-122">Desenvolva com o [Visual Studio](https://visualstudio.microsoft.com/vs/) usando C#, vb ou F #</span><span class="sxs-lookup"><span data-stu-id="933bf-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="933bf-123">Desempenho superior ao do ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="933bf-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="933bf-124">Bom desempenho</span><span class="sxs-lookup"><span data-stu-id="933bf-124">Good performance</span></span>|
|[<span data-ttu-id="933bf-125">Usar o runtime do .NET Core</span><span class="sxs-lookup"><span data-stu-id="933bf-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="933bf-126">Use o runtime do .NET Framework</span><span class="sxs-lookup"><span data-stu-id="933bf-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="933bf-127">Confira [ASP.NET Core targeting .NET Framework](xref:index#target-framework) (ASP.NET Core direcionado para o .NET Framework) para obter informações sobre o suporte do ASP.NET Core 2.x no .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="933bf-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="933bf-128">Cenários do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="933bf-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="933bf-129">Sites</span><span class="sxs-lookup"><span data-stu-id="933bf-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="933bf-130">APIs</span><span class="sxs-lookup"><span data-stu-id="933bf-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="933bf-131">Tempo real</span><span class="sxs-lookup"><span data-stu-id="933bf-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="933bf-132">Implantar um aplicativo do ASP.NET Core no Azure</span><span class="sxs-lookup"><span data-stu-id="933bf-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="933bf-133">Cenários do ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="933bf-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="933bf-134">Sites</span><span class="sxs-lookup"><span data-stu-id="933bf-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="933bf-135">APIs</span><span class="sxs-lookup"><span data-stu-id="933bf-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="933bf-136">Tempo real</span><span class="sxs-lookup"><span data-stu-id="933bf-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="933bf-137">Criar um aplicativo Web ASP.NET 4.x no Azure</span><span class="sxs-lookup"><span data-stu-id="933bf-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="933bf-138">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="933bf-138">Additional resources</span></span>

* [<span data-ttu-id="933bf-139">Introdução ao ASP.NET</span><span class="sxs-lookup"><span data-stu-id="933bf-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="933bf-140">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="933bf-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
