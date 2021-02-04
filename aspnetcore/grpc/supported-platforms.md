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
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530158"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="73fa9-103">gRPC em plataformas com suporte do .NET</span><span class="sxs-lookup"><span data-stu-id="73fa9-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="73fa9-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="73fa9-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="73fa9-105">Este artigo aborda os requisitos e as plataformas com suporte para o uso do gRPC com o .NET.</span><span class="sxs-lookup"><span data-stu-id="73fa9-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="73fa9-106">o gRPC aproveita os recursos avançados disponíveis no HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="73fa9-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="73fa9-107">O HTTP/2 não tem suporte em todos os lugares, mas um segundo formato de conexão usando HTTP/1.1 está disponível para gRPC:</span><span class="sxs-lookup"><span data-stu-id="73fa9-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="73fa9-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sobre HTTP/2 é como o gRPC normalmente é usado.</span><span class="sxs-lookup"><span data-stu-id="73fa9-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="73fa9-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifica o protocolo gRPC para que seja compatível com HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="73fa9-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="73fa9-110">o gRPC-Web pode ser usado em mais lugares, notadamente que é possível chamá-lo por aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="73fa9-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="73fa9-111">Dois recursos avançados do gRPC não têm mais suporte: streaming de cliente e streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="73fa9-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="73fa9-112">o gRPC no .NET dá suporte a formatos de fios.</span><span class="sxs-lookup"><span data-stu-id="73fa9-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="73fa9-113">o gRPC sobre HTTP/2 é usado por padrão.</span><span class="sxs-lookup"><span data-stu-id="73fa9-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="73fa9-114">Para obter informações sobre como configurar o gRPC-Web, consulte <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="73fa9-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="73fa9-115">Requisitos do dispositivo</span><span class="sxs-lookup"><span data-stu-id="73fa9-115">Device requirements</span></span>

<span data-ttu-id="73fa9-116">o gRPC no .NET dá suporte a qualquer dispositivo compatível com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="73fa9-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="73fa9-117">Windows</span><span class="sxs-lookup"><span data-stu-id="73fa9-117">Windows</span></span>
> * <span data-ttu-id="73fa9-118">Linux</span><span class="sxs-lookup"><span data-stu-id="73fa9-118">Linux</span></span>
> * <span data-ttu-id="73fa9-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="73fa9-119">macOS&dagger;</span></span>
> * <span data-ttu-id="73fa9-120">Navegadores&Dagger;</span><span class="sxs-lookup"><span data-stu-id="73fa9-120">Browsers&Dagger;</span></span>

<span data-ttu-id="73fa9-121">&dagger;o [MacOS não dá suporte à Hospedagem de aplicativos ASP.NET Core com HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="73fa9-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="73fa9-122">Os clientes do gRPC no macOS podem chamar serviços remotos que usam HTTPS.</span><span class="sxs-lookup"><span data-stu-id="73fa9-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="73fa9-123">&Dagger;Blazor WebAssembly os aplicativos podem chamar serviços gRPCs com gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="73fa9-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="73fa9-124">Requisitos do ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="73fa9-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="73fa9-125">os serviços gRPCs podem ser hospedados em todos os servidores de ASP.NET Core internos.</span><span class="sxs-lookup"><span data-stu-id="73fa9-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="73fa9-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="73fa9-126">Kestrel</span></span>
> * <span data-ttu-id="73fa9-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="73fa9-127">TestServer</span></span>
> * <span data-ttu-id="73fa9-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="73fa9-128">IIS&dagger;</span></span>
> * <span data-ttu-id="73fa9-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="73fa9-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="73fa9-130">&dagger;O IIS requer o .NET 5 e o Windows 10 Build 20241 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="73fa9-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="73fa9-131">&Dagger;HTTP.sys requer o .NET 5 e o Windows 10 Build 19529 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="73fa9-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="73fa9-132">Para obter informações sobre como configurar servidores ASP.NET Core para executar o gRPC, consulte <xref:grpc/aspnetcore#server-options> .</span><span class="sxs-lookup"><span data-stu-id="73fa9-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="73fa9-133">Requisitos de versão do .NET</span><span class="sxs-lookup"><span data-stu-id="73fa9-133">.NET version requirements</span></span>

<span data-ttu-id="73fa9-134">o gRPC no .NET dá suporte ao .NET Core 3 e ao .NET 5 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="73fa9-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="73fa9-135">.NET 5 ou posterior</span><span class="sxs-lookup"><span data-stu-id="73fa9-135">.NET 5 or later</span></span>
> * <span data-ttu-id="73fa9-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="73fa9-136">.NET Core 3</span></span>

<span data-ttu-id="73fa9-137">o gRPC no .NET não dá suporte à execução em .NET Framework e Xamarin.</span><span class="sxs-lookup"><span data-stu-id="73fa9-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="73fa9-138">[GRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) é uma biblioteca de terceiros que dá suporte a .NET Framework e Xamarin.</span><span class="sxs-lookup"><span data-stu-id="73fa9-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="73fa9-139">o gRPC C-Core não tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="73fa9-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="73fa9-140">Serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="73fa9-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="73fa9-141">AKS (Serviço de Kubernetes do Azure)</span><span class="sxs-lookup"><span data-stu-id="73fa9-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="73fa9-142">[Serviço de Azure App](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="73fa9-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="73fa9-143">&dagger;O serviço de Azure App não dá suporte à Hospedagem de gRPC sobre HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="73fa9-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="73fa9-144">gRPC-Web é uma alternativa compatível.</span><span class="sxs-lookup"><span data-stu-id="73fa9-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="73fa9-145">O trabalho está em andamento para melhorar o suporte para gRPC com HTTP/2 no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="73fa9-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="73fa9-146">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="73fa9-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73fa9-147">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="73fa9-147">Additional resources</span></span>

* [<span data-ttu-id="73fa9-148">gRPC C# Core – biblioteca</span><span class="sxs-lookup"><span data-stu-id="73fa9-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
