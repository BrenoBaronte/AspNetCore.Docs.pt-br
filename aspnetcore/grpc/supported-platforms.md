---
title: gRPC em plataformas com suporte do .NET
author: jamesnk
description: Saiba mais sobre as plataformas com suporte para o gRPC no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 88d371f460839261b618a32564a723c257b0b119
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217486"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="28ab2-103">gRPC em plataformas com suporte do .NET</span><span class="sxs-lookup"><span data-stu-id="28ab2-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="28ab2-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="28ab2-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="28ab2-105">Este artigo aborda os requisitos e as plataformas com suporte para o uso do gRPC com o .NET.</span><span class="sxs-lookup"><span data-stu-id="28ab2-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="28ab2-106">o gRPC aproveita os recursos avançados disponíveis no HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="28ab2-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="28ab2-107">O HTTP/2 não tem suporte em todos os lugares, mas um segundo formato de conexão usando HTTP/1.1 está disponível para gRPC:</span><span class="sxs-lookup"><span data-stu-id="28ab2-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="28ab2-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sobre HTTP/2 é como o gRPC normalmente é usado.</span><span class="sxs-lookup"><span data-stu-id="28ab2-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="28ab2-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifica o protocolo gRPC para que seja compatível com HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="28ab2-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="28ab2-110">o gRPC-Web pode ser usado em mais lugares, notadamente que é possível chamá-lo por aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="28ab2-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="28ab2-111">Dois recursos avançados do gRPC não têm mais suporte: streaming de cliente e streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="28ab2-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="28ab2-112">o gRPC no .NET dá suporte a formatos de fios.</span><span class="sxs-lookup"><span data-stu-id="28ab2-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="28ab2-113">o gRPC sobre HTTP/2 é usado por padrão.</span><span class="sxs-lookup"><span data-stu-id="28ab2-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="28ab2-114">Para obter informações sobre como configurar o gRPC-Web, consulte <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="28ab2-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="28ab2-115">Requisitos do dispositivo</span><span class="sxs-lookup"><span data-stu-id="28ab2-115">Device requirements</span></span>

<span data-ttu-id="28ab2-116">o gRPC no .NET dá suporte a qualquer dispositivo compatível com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="28ab2-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="28ab2-117">Windows</span><span class="sxs-lookup"><span data-stu-id="28ab2-117">Windows</span></span>
> * <span data-ttu-id="28ab2-118">Linux</span><span class="sxs-lookup"><span data-stu-id="28ab2-118">Linux</span></span>
> * <span data-ttu-id="28ab2-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="28ab2-119">macOS&dagger;</span></span>
> * <span data-ttu-id="28ab2-120">Navegadores&Dagger;</span><span class="sxs-lookup"><span data-stu-id="28ab2-120">Browsers&Dagger;</span></span>

<span data-ttu-id="28ab2-121">&dagger;o [MacOS não dá suporte à Hospedagem de aplicativos ASP.NET Core com HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="28ab2-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="28ab2-122">Os clientes do gRPC no macOS podem chamar serviços remotos que usam HTTPS.</span><span class="sxs-lookup"><span data-stu-id="28ab2-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="28ab2-123">&Dagger;Blazor WebAssembly os aplicativos podem chamar serviços gRPCs com gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="28ab2-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="28ab2-124">Requisitos do ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="28ab2-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="28ab2-125">os serviços gRPCs podem ser hospedados em todos os servidores de ASP.NET Core internos.</span><span class="sxs-lookup"><span data-stu-id="28ab2-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="28ab2-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="28ab2-126">Kestrel</span></span>
> * <span data-ttu-id="28ab2-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="28ab2-127">TestServer</span></span>
> * <span data-ttu-id="28ab2-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="28ab2-128">IIS&dagger;</span></span>
> * <span data-ttu-id="28ab2-129">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="28ab2-129">HTTP.sys&dagger;</span></span>

<span data-ttu-id="28ab2-130">&dagger;O IIS e o HTTP.sys exigem o .NET 5 e o Windows 10 Build 20241 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="28ab2-130">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="28ab2-131">Para obter mais informações, consulte <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="28ab2-131">For more information, see <xref:grpc/aspnetcore>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="28ab2-132">Requisitos de versão do .NET</span><span class="sxs-lookup"><span data-stu-id="28ab2-132">.NET version requirements</span></span>

<span data-ttu-id="28ab2-133">o gRPC no .NET dá suporte ao .NET Core 3 e ao .NET 5 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="28ab2-133">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="28ab2-134">.NET 5 ou posterior</span><span class="sxs-lookup"><span data-stu-id="28ab2-134">.NET 5 or later</span></span>
> * <span data-ttu-id="28ab2-135">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="28ab2-135">.NET Core 3</span></span>

<span data-ttu-id="28ab2-136">o gRPC no .NET não dá suporte à execução em .NET Framework e Xamarin.</span><span class="sxs-lookup"><span data-stu-id="28ab2-136">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="28ab2-137">[GRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) é uma biblioteca de terceiros que dá suporte a .NET Framework e Xamarin.</span><span class="sxs-lookup"><span data-stu-id="28ab2-137">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="28ab2-138">o gRPC C-Core não tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="28ab2-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="28ab2-139">Serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="28ab2-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="28ab2-140">AKS (Serviço de Kubernetes do Azure)</span><span class="sxs-lookup"><span data-stu-id="28ab2-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="28ab2-141">[Serviço de Azure App](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="28ab2-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="28ab2-142">&dagger;O serviço de Azure App não dá suporte à Hospedagem de gRPC sobre HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="28ab2-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="28ab2-143">gRPC-Web é uma alternativa compatível.</span><span class="sxs-lookup"><span data-stu-id="28ab2-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="28ab2-144">O trabalho está em andamento para melhorar o suporte para gRPC com HTTP/2 no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="28ab2-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="28ab2-145">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="28ab2-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28ab2-146">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="28ab2-146">Additional resources</span></span>

* [<span data-ttu-id="28ab2-147">gRPC C# Core – biblioteca</span><span class="sxs-lookup"><span data-stu-id="28ab2-147">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
