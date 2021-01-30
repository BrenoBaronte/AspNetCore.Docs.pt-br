---
title: gRPC para plataformas com suporte do .NET
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
ms.openlocfilehash: 92ca38875c6618c8630a66af16548d32bc469a62
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057708"
---
# <a name="grpc-for-net-supported-platforms"></a><span data-ttu-id="5e57f-103">gRPC para plataformas com suporte do .NET</span><span class="sxs-lookup"><span data-stu-id="5e57f-103">gRPC for .NET supported platforms</span></span>

<span data-ttu-id="5e57f-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5e57f-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5e57f-105">Este artigo aborda os requisitos e as plataformas com suporte para o uso do gRPC com o .NET.</span><span class="sxs-lookup"><span data-stu-id="5e57f-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="5e57f-106">o gRPC foi projetado para usar HTTP/2 para alguns de seus recursos mais avançados.</span><span class="sxs-lookup"><span data-stu-id="5e57f-106">gRPC is designed to use HTTP/2 for some of its more advanced features.</span></span> <span data-ttu-id="5e57f-107">Não há suporte para HTTP/2 em todos os lugares que podem impedir o uso de gRPC.</span><span class="sxs-lookup"><span data-stu-id="5e57f-107">HTTP/2 isn't supported everywhere which can prevent using gRPC.</span></span> <span data-ttu-id="5e57f-108">Por causa disso, há um segundo formato de transmissão compatível com HTTP/1.1 para enviar chamadas gRPC entre clientes e servidores:</span><span class="sxs-lookup"><span data-stu-id="5e57f-108">Because of this there is second wire-format that is compatible with HTTP/1.1 for sending gRPC calls between clients and servers:</span></span>

* <span data-ttu-id="5e57f-109">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sobre HTTP/2 é como o gRPC normalmente é usado.</span><span class="sxs-lookup"><span data-stu-id="5e57f-109">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="5e57f-110">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifica o protocolo gRPC para que seja compatível com HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="5e57f-110">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="5e57f-111">o gRPC-Web pode ser usado em mais lugares, notadamente que é possível chamá-lo por aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="5e57f-111">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="5e57f-112">Dois recursos avançados do gRPC não têm mais suporte: streaming de cliente e streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="5e57f-112">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="5e57f-113">o gRPC para .NET dá suporte a formatos de fios.</span><span class="sxs-lookup"><span data-stu-id="5e57f-113">gRPC for .NET supports both wire-formats.</span></span> <span data-ttu-id="5e57f-114">Para obter informações sobre como configurar o gRPC-Web, consulte <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="5e57f-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="5e57f-115">Requisitos do dispositivo</span><span class="sxs-lookup"><span data-stu-id="5e57f-115">Device requirements</span></span>

<span data-ttu-id="5e57f-116">o gRPC para .NET dá suporte a qualquer dispositivo compatível com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e57f-116">gRPC for .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="5e57f-117">Windows</span><span class="sxs-lookup"><span data-stu-id="5e57f-117">Windows</span></span>
> * <span data-ttu-id="5e57f-118">Linux</span><span class="sxs-lookup"><span data-stu-id="5e57f-118">Linux</span></span>
> * <span data-ttu-id="5e57f-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="5e57f-119">macOS&dagger;</span></span>
> * <span data-ttu-id="5e57f-120">Navegadores&Dagger;</span><span class="sxs-lookup"><span data-stu-id="5e57f-120">Browsers&Dagger;</span></span>

<span data-ttu-id="5e57f-121">&dagger;ASP.NET Core aplicativos hospedados no macOS não dão suporte a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5e57f-121">&dagger;ASP.NET Core apps hosted on macOS don't support HTTPS.</span></span> <span data-ttu-id="5e57f-122">Os clientes do gRPC no macOS ainda podem usar HTTPS ao chamar serviços remotos.</span><span class="sxs-lookup"><span data-stu-id="5e57f-122">gRPC clients on macOS can still use HTTPS when calling remote services.</span></span>

<span data-ttu-id="5e57f-123">&Dagger;Blazor WebAssembly os aplicativos podem chamar serviços gRPCs com gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="5e57f-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="5e57f-124">Requisitos do ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="5e57f-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="5e57f-125">os serviços gRPCs podem ser hospedados em todos os servidores de ASP.NET Core internos.</span><span class="sxs-lookup"><span data-stu-id="5e57f-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="5e57f-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="5e57f-126">Kestrel</span></span>
> * <span data-ttu-id="5e57f-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="5e57f-127">TestServer</span></span>
> * <span data-ttu-id="5e57f-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="5e57f-128">IIS&dagger;</span></span>
> * <span data-ttu-id="5e57f-129">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="5e57f-129">HTTP.sys&dagger;</span></span>

<span data-ttu-id="5e57f-130">&dagger;O IIS e o HTTP.sys exigem o .NET 5 e o Windows 10 Build 20241 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="5e57f-130">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="5e57f-131">Para obter mais informações, consulte <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="5e57f-131">For more information, see <xref:grpc/aspnetcore>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="5e57f-132">Requisitos de versão do .NET</span><span class="sxs-lookup"><span data-stu-id="5e57f-132">.NET version requirements</span></span>

<span data-ttu-id="5e57f-133">o gRPC para .NET dá suporte ao .NET Core 3 e ao .NET 5 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="5e57f-133">gRPC for .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="5e57f-134">.NET 5 ou posterior</span><span class="sxs-lookup"><span data-stu-id="5e57f-134">.NET 5 or later</span></span>
> * <span data-ttu-id="5e57f-135">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="5e57f-135">.NET Core 3</span></span>

<span data-ttu-id="5e57f-136">o gRPC para .NET não dá suporte à execução em .NET Framework e Xamarin.</span><span class="sxs-lookup"><span data-stu-id="5e57f-136">gRPC for .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="5e57f-137">[GRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) é uma biblioteca de terceiros que dá suporte a .NET Framework e Xamarin.</span><span class="sxs-lookup"><span data-stu-id="5e57f-137">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="5e57f-138">o gRPC C-Core não tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5e57f-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="5e57f-139">Serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="5e57f-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="5e57f-140">AKS (Serviço de Kubernetes do Azure)</span><span class="sxs-lookup"><span data-stu-id="5e57f-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="5e57f-141">[Serviço de Azure App](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="5e57f-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="5e57f-142">&dagger;O serviço de Azure App não dá suporte à Hospedagem de gRPC sobre HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="5e57f-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="5e57f-143">gRPC-Web é uma alternativa compatível.</span><span class="sxs-lookup"><span data-stu-id="5e57f-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="5e57f-144">O trabalho está em andamento para melhorar o suporte para gRPC com HTTP/2 no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="5e57f-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="5e57f-145">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="5e57f-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e57f-146">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5e57f-146">Additional resources</span></span>

* [<span data-ttu-id="5e57f-147">gRPC C# Core – biblioteca</span><span class="sxs-lookup"><span data-stu-id="5e57f-147">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
