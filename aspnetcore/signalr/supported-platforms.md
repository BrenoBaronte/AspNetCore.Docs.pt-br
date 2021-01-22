---
title: ASP.NET Core SignalR plataformas com suporte
author: bradygaster
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
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
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689221"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="a3215-103">ASP.NET Core SignalR plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="a3215-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="a3215-104">Requisitos do sistema do servidor do</span><span class="sxs-lookup"><span data-stu-id="a3215-104">Server system requirements</span></span>

<span data-ttu-id="a3215-105">SignalR para ASP.NET Core dá suporte a qualquer plataforma de servidor que ASP.NET Core suporte.</span><span class="sxs-lookup"><span data-stu-id="a3215-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="a3215-106">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="a3215-106">JavaScript client</span></span>

<span data-ttu-id="a3215-107">O [cliente JavaScript](xref:signalr/javascript-client) é executado no NodeJS 8 e versões posteriores e nos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="a3215-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="a3215-108">Navegador</span><span class="sxs-lookup"><span data-stu-id="a3215-108">Browser</span></span>                          | <span data-ttu-id="a3215-109">Versão</span><span class="sxs-lookup"><span data-stu-id="a3215-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="a3215-110">Apple Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="a3215-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="a3215-111">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="a3215-111">Current&dagger;</span></span> |
| <span data-ttu-id="a3215-112">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="a3215-112">Google Chrome, including Android</span></span> | <span data-ttu-id="a3215-113">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="a3215-113">Current&dagger;</span></span> |
| <span data-ttu-id="a3215-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="a3215-114">Microsoft Edge</span></span>                   | <span data-ttu-id="a3215-115">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="a3215-115">Current&dagger;</span></span> |
| <span data-ttu-id="a3215-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="a3215-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="a3215-117">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="a3215-117">Current&dagger;</span></span> |

<span data-ttu-id="a3215-118">&dagger;*Atual* refere-se à versão mais recente do navegador.</span><span class="sxs-lookup"><span data-stu-id="a3215-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

<span data-ttu-id="a3215-119">O cliente JavaScript não dá suporte ao Internet Explorer e a outros navegadores mais antigos.</span><span class="sxs-lookup"><span data-stu-id="a3215-119">The JavaScript client doesn't support Internet Explorer and other older browsers.</span></span> <span data-ttu-id="a3215-120">O cliente pode ter comportamento inesperado e erros em navegadores sem suporte.</span><span class="sxs-lookup"><span data-stu-id="a3215-120">The client might have unexpected behavior and errors on unsupported browsers.</span></span>

## <a name="net-client"></a><span data-ttu-id="a3215-121">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="a3215-121">.NET client</span></span>

<span data-ttu-id="a3215-122">O [cliente .net](xref:signalr/dotnet-client) é executado em qualquer plataforma com suporte do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3215-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="a3215-123">Por exemplo, [os desenvolvedores do xamarin SignalR podem usar](https://github.com/aspnet/Announcements/issues/305) o para criar aplicativos Android usando xamarin. Android 8.4.0.1 e posterior e aplicativos Ios usando xamarin. Ios 11.14.0.4 e posterior.</span><span class="sxs-lookup"><span data-stu-id="a3215-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="a3215-124">Se o servidor executar o IIS, o transporte do WebSockets exigirá o IIS 8,0 ou posterior no Windows Server 2012 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="a3215-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="a3215-125">Outros transportes têm suporte em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="a3215-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="a3215-126">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="a3215-126">Java client</span></span>

<span data-ttu-id="a3215-127">O [cliente Java](xref:signalr/java-client) dá suporte a Java 8 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="a3215-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="a3215-128">Clientes sem suporte</span><span class="sxs-lookup"><span data-stu-id="a3215-128">Unsupported clients</span></span>

<span data-ttu-id="a3215-129">Os clientes a seguir estão disponíveis, mas são experimentais ou não oficiais.</span><span class="sxs-lookup"><span data-stu-id="a3215-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="a3215-130">Atualmente, eles não têm suporte e podem nunca ser.</span><span class="sxs-lookup"><span data-stu-id="a3215-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="a3215-131">[Cliente C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="a3215-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="a3215-132">[Cliente Swift](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="a3215-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
