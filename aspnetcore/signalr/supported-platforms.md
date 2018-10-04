---
title: Plataformas com suporte do SignalR do ASP.NET Core
author: tdykstra
description: Plataformas com suporte para o SignalR do ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 09/26/2018
uid: signalr/supported-platforms
ms.openlocfilehash: d6d74a55d35ddb34a6f66a171bfe3f343dd61b63
ms.sourcegitcommit: 7b4e3936feacb1a8fcea7802aab3e2ea9c8af5b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48577620"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="332b1-103">Plataformas com suporte do SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="332b1-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="332b1-104">Requisitos de sistema do servidor</span><span class="sxs-lookup"><span data-stu-id="332b1-104">Server system requirements</span></span>

<span data-ttu-id="332b1-105">SignalR para ASP.NET Core dá suporte a qualquer plataforma de servidor que ASP.NET Core dá suporte.</span><span class="sxs-lookup"><span data-stu-id="332b1-105">SignalR for ASP.NET Core supports any server platform ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="332b1-106">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="332b1-106">JavaScript client</span></span>

<span data-ttu-id="332b1-107">O [cliente JavaScript](https://www.npmjs.com/package/@aspnet/signalr) é executado no NodeJS 8 e versões posteriores e os seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="332b1-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="332b1-108">Navegador</span><span class="sxs-lookup"><span data-stu-id="332b1-108">Browser</span></span> | <span data-ttu-id="332b1-109">Versão</span><span class="sxs-lookup"><span data-stu-id="332b1-109">Version</span></span> |
| ------- | ------- |
| <span data-ttu-id="332b1-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="332b1-110">Microsoft Edge</span></span> | <span data-ttu-id="332b1-111">atual</span><span class="sxs-lookup"><span data-stu-id="332b1-111">current</span></span> |
| <span data-ttu-id="332b1-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="332b1-112">Mozilla Firefox</span></span> | <span data-ttu-id="332b1-113">atual</span><span class="sxs-lookup"><span data-stu-id="332b1-113">current</span></span> |
| <span data-ttu-id="332b1-114">Google Chrome; inclui o Android</span><span class="sxs-lookup"><span data-stu-id="332b1-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="332b1-115">atual</span><span class="sxs-lookup"><span data-stu-id="332b1-115">current</span></span> |
| <span data-ttu-id="332b1-116">Safari; inclui o iOS</span><span class="sxs-lookup"><span data-stu-id="332b1-116">Safari; includes iOS</span></span> | <span data-ttu-id="332b1-117">atual</span><span class="sxs-lookup"><span data-stu-id="332b1-117">current</span></span> |
| <span data-ttu-id="332b1-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="332b1-118">Microsoft Internet Explorer</span></span> | <span data-ttu-id="332b1-119">11</span><span class="sxs-lookup"><span data-stu-id="332b1-119">11</span></span> |
 
## <a name="net-client"></a><span data-ttu-id="332b1-120">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="332b1-120">.NET client</span></span>

<span data-ttu-id="332b1-121">O [cliente .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) é executado em qualquer plataforma de servidor com suporte pelo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="332b1-121">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any server platform supported by ASP.NET Core.</span></span>

<span data-ttu-id="332b1-122">Quando o servidor executa o IIS, o transporte de WebSockets requer o IIS 8.0 ou superior, no Windows Server 2012 ou superior.</span><span class="sxs-lookup"><span data-stu-id="332b1-122">When the server runs IIS, the WebSockets transport requires IIS 8.0 or higher, on Windows Server 2012 or higher.</span></span> <span data-ttu-id="332b1-123">Outros transportes têm suporte em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="332b1-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="332b1-124">Cliente de Java</span><span class="sxs-lookup"><span data-stu-id="332b1-124">Java client</span></span>

<span data-ttu-id="332b1-125">O [cliente Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) dá suporte a Java 8 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="332b1-125">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="332b1-126">Não há suporte para clientes</span><span class="sxs-lookup"><span data-stu-id="332b1-126">Unsupported clients</span></span>

<span data-ttu-id="332b1-127">Os clientes a seguir estão disponíveis, mas são experimentais ou não oficial.</span><span class="sxs-lookup"><span data-stu-id="332b1-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="332b1-128">Eles não são suportados agora e nunca não podem ter suporte.</span><span class="sxs-lookup"><span data-stu-id="332b1-128">They are not supported now and may not ever be supported.</span></span>

* [<span data-ttu-id="332b1-129">Cliente C++</span><span class="sxs-lookup"><span data-stu-id="332b1-129">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="332b1-130">Cliente SWIFT</span><span class="sxs-lookup"><span data-stu-id="332b1-130">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
