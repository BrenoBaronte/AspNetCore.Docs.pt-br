---
title: Escolher entre o ASP.NET 4.x e o ASP.NET Core
author: rick-anderson
description: Explica ASP.NET Core vs. ASP.NET 4.x e como escolher entre eles.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665238"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="b331d-103">Escolher entre o ASP.NET 4.x e o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b331d-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="b331d-104">O ASP.NET Core é uma reformulação do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="b331d-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="b331d-105">Este artigo lista as diferenças entre eles.</span><span class="sxs-lookup"><span data-stu-id="b331d-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="b331d-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b331d-106">ASP.NET Core</span></span>

<span data-ttu-id="b331d-107">O ASP.NET Core é uma estrutura de software livre, multiplataforma, para a criação de aplicativos Web modernos e baseados em nuvem, no Windows, no macOS ou no Linux.</span><span class="sxs-lookup"><span data-stu-id="b331d-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="b331d-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="b331d-108">ASP.NET 4.x</span></span>

<span data-ttu-id="b331d-109">O ASP.NET 4.x é uma estrutura consolidada que fornece os serviços necessários para criar aplicativos Web baseados em servidor, de nível empresarial, no Windows.</span><span class="sxs-lookup"><span data-stu-id="b331d-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="b331d-110">Seleção de estrutura</span><span class="sxs-lookup"><span data-stu-id="b331d-110">Framework selection</span></span>

<span data-ttu-id="b331d-111">A tabela a seguir compara o ASP.NET Core com o ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="b331d-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="b331d-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b331d-112">ASP.NET Core</span></span> | <span data-ttu-id="b331d-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="b331d-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="b331d-114">Build para Windows, macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="b331d-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="b331d-115">Build para Windows</span><span class="sxs-lookup"><span data-stu-id="b331d-115">Build for Windows</span></span>|
|<span data-ttu-id="b331d-116">[Páginas Razor](xref:razor-pages/index) é a abordagem recomendada para criar uma interface do usuário da Web começando com o ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="b331d-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="b331d-117">Consulte também [MVC,](xref:mvc/overview)Web [SignalR](xref:signalr/introduction) [API](xref:tutorials/first-web-api)e .</span><span class="sxs-lookup"><span data-stu-id="b331d-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="b331d-118">Use [formulários web,](/aspnet/web-forms) [SignalR](/aspnet/signalr) [MVC,](/aspnet/mvc) [Web API,](/aspnet/web-api/) [WebHooks](/aspnet/webhooks/)ou [Páginas da Web](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="b331d-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="b331d-119">Várias versões por computador</span><span class="sxs-lookup"><span data-stu-id="b331d-119">Multiple versions per machine</span></span>|<span data-ttu-id="b331d-120">Uma versão por computador</span><span class="sxs-lookup"><span data-stu-id="b331d-120">One version per machine</span></span>|
|<span data-ttu-id="b331d-121">Desenvolva com o [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) ou [Visual Studio Code](https://code.visualstudio.com/) usando C# ou F#</span><span class="sxs-lookup"><span data-stu-id="b331d-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="b331d-122">Desenvolver com [o Visual Studio](https://visualstudio.microsoft.com/vs/) usando C#, VB ou F #</span><span class="sxs-lookup"><span data-stu-id="b331d-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="b331d-123">Desempenho superior ao do ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="b331d-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="b331d-124">Bom desempenho</span><span class="sxs-lookup"><span data-stu-id="b331d-124">Good performance</span></span>|
|[<span data-ttu-id="b331d-125">Usar o runtime do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b331d-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="b331d-126">Use o runtime do .NET Framework</span><span class="sxs-lookup"><span data-stu-id="b331d-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="b331d-127">Confira [ASP.NET Core targeting .NET Framework](xref:index#target-framework) (ASP.NET Core direcionado para o .NET Framework) para obter informações sobre o suporte do ASP.NET Core 2.x no .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b331d-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="b331d-128">Cenários do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b331d-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="b331d-129">Sites</span><span class="sxs-lookup"><span data-stu-id="b331d-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="b331d-130">APIs</span><span class="sxs-lookup"><span data-stu-id="b331d-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="b331d-131">Tempo real</span><span class="sxs-lookup"><span data-stu-id="b331d-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="b331d-132">Implantar um aplicativo ASP.NET Core no Azure</span><span class="sxs-lookup"><span data-stu-id="b331d-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="b331d-133">Cenários do ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="b331d-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="b331d-134">Sites</span><span class="sxs-lookup"><span data-stu-id="b331d-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="b331d-135">APIs</span><span class="sxs-lookup"><span data-stu-id="b331d-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="b331d-136">Tempo real</span><span class="sxs-lookup"><span data-stu-id="b331d-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="b331d-137">Criar um aplicativo Web ASP.NET 4.x no Azure</span><span class="sxs-lookup"><span data-stu-id="b331d-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="b331d-138">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b331d-138">Additional resources</span></span>

* [<span data-ttu-id="b331d-139">Introdução ao ASP.NET</span><span class="sxs-lookup"><span data-stu-id="b331d-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="b331d-140">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b331d-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
