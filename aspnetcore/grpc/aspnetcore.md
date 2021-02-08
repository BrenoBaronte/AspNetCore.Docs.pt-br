---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 1a5510364ee46165e275d07073ab087d79d65313
ms.sourcegitcommit: 50d3e939a90c5480df480f651dda032901468dd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819036"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="8118a-103">Serviços do gRPC com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8118a-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="8118a-104">Este documento mostra como começar a usar os serviços gRPCs usando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8118a-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="8118a-105">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="8118a-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8118a-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8118a-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8118a-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8118a-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8118a-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8118a-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="8118a-109">Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8118a-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="8118a-110">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8118a-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8118a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8118a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8118a-112">Consulte Introdução [aos serviços gRPCs](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.</span><span class="sxs-lookup"><span data-stu-id="8118a-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8118a-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8118a-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8118a-114">Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="8118a-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="8118a-115">Adicionar serviços gRPCs a um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8118a-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="8118a-116">gRPC requer o pacote [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="8118a-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="8118a-117">Configurar o gRPC</span><span class="sxs-lookup"><span data-stu-id="8118a-117">Configure gRPC</span></span>

<span data-ttu-id="8118a-118">Em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8118a-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="8118a-119">gRPC é habilitado com o `AddGrpc` método.</span><span class="sxs-lookup"><span data-stu-id="8118a-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="8118a-120">Cada serviço gRPC é adicionado ao pipeline de roteamento por meio do `MapGrpcService` método.</span><span class="sxs-lookup"><span data-stu-id="8118a-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8118a-121">ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais.</span><span class="sxs-lookup"><span data-stu-id="8118a-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="8118a-122">Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.</span><span class="sxs-lookup"><span data-stu-id="8118a-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="8118a-123">Opções de servidor</span><span class="sxs-lookup"><span data-stu-id="8118a-123">Server options</span></span>

<span data-ttu-id="8118a-124">os serviços gRPCs podem ser hospedados por todos os servidores de ASP.NET Core internos.</span><span class="sxs-lookup"><span data-stu-id="8118a-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="8118a-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8118a-125">Kestrel</span></span>
> * <span data-ttu-id="8118a-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="8118a-126">TestServer</span></span>
> * <span data-ttu-id="8118a-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="8118a-127">IIS&dagger;</span></span>
> * <span data-ttu-id="8118a-128">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="8118a-128">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="8118a-129">&dagger;O IIS requer o .NET 5 e o Windows 10 Build 20241 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="8118a-129">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="8118a-130">&Dagger;HTTP.sys requer o .NET 5 e o Windows 10 Build 19529 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="8118a-130">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="8118a-131">Para obter mais informações sobre como escolher o servidor certo para um aplicativo ASP.NET Core, consulte <xref:fundamentals/servers/index> .</span><span class="sxs-lookup"><span data-stu-id="8118a-131">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="8118a-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8118a-132">Kestrel</span></span>

<span data-ttu-id="8118a-133">[Kestrel](xref:fundamentals/servers/kestrel) é um servidor Web de plataforma cruzada para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8118a-133">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="8118a-134">O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados em HTTP.sys como o compartilhamento de porta.</span><span class="sxs-lookup"><span data-stu-id="8118a-134">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="8118a-135">Kestrel pontos de extremidade gRPC:</span><span class="sxs-lookup"><span data-stu-id="8118a-135">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="8118a-136">Exigir HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8118a-136">Require HTTP/2.</span></span>
* <span data-ttu-id="8118a-137">Deve ser protegido com [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="8118a-137">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="8118a-138">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8118a-138">HTTP/2</span></span>

<span data-ttu-id="8118a-139">gRPC requer HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8118a-139">gRPC requires HTTP/2.</span></span> <span data-ttu-id="8118a-140">gRPC para ASP.NET Core valida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) é `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="8118a-140">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="8118a-141">O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel/http2) na maioria dos sistemas operacionais modernos.</span><span class="sxs-lookup"><span data-stu-id="8118a-141">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="8118a-142">Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.</span><span class="sxs-lookup"><span data-stu-id="8118a-142">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="8118a-143">TLS</span><span class="sxs-lookup"><span data-stu-id="8118a-143">TLS</span></span>

<span data-ttu-id="8118a-144">Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com TLS.</span><span class="sxs-lookup"><span data-stu-id="8118a-144">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="8118a-145">No desenvolvimento, um ponto de extremidade protegido com TLS é criado automaticamente `https://localhost:5001` quando o certificado de desenvolvimento de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="8118a-145">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="8118a-146">Nenhuma configuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="8118a-146">No configuration is required.</span></span> <span data-ttu-id="8118a-147">Um `https` prefixo verifica se o ponto de extremidade Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="8118a-147">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="8118a-148">Em produção, o TLS deve ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="8118a-148">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="8118a-149">No exemplo a seguir *appsettings.json* , um ponto de extremidade http/2 protegido com TLS é fornecido:</span><span class="sxs-lookup"><span data-stu-id="8118a-149">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="8118a-150">Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8118a-150">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="8118a-151">Negociação de protocolo</span><span class="sxs-lookup"><span data-stu-id="8118a-151">Protocol negotiation</span></span>

<span data-ttu-id="8118a-152">O TLS é usado para mais do que proteger a comunicação.</span><span class="sxs-lookup"><span data-stu-id="8118a-152">TLS is used for more than securing communication.</span></span> <span data-ttu-id="8118a-153">O handshake [ALPN (negociação do protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto de extremidade dá suporte a vários protocolos.</span><span class="sxs-lookup"><span data-stu-id="8118a-153">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="8118a-154">Essa negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8118a-154">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="8118a-155">Se um ponto de extremidade HTTP/2 estiver configurado sem TLS, os [protocolos de escuta](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) do ponto de extremidade deverão ser definidos como `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="8118a-155">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="8118a-156">Um ponto de extremidade com vários protocolos (por exemplo, `HttpProtocols.Http1AndHttp2` ) não pode ser usado sem TLS porque não há negociação.</span><span class="sxs-lookup"><span data-stu-id="8118a-156">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="8118a-157">Todas as conexões com o ponto de extremidade não seguro padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="8118a-157">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="8118a-158">Para obter mais informações sobre como habilitar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="8118a-158">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="8118a-159">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="8118a-159">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="8118a-160">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="8118a-160">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="8118a-161">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="8118a-161">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="8118a-162">IIS</span><span class="sxs-lookup"><span data-stu-id="8118a-162">IIS</span></span>

<span data-ttu-id="8118a-163">O [serviços de informações da Internet (IIS)](xref:host-and-deploy/iis/index) é um servidor Web flexível, seguro e gerenciável para hospedar aplicativos Web, incluindo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8118a-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="8118a-164">O .NET 5 e o Windows 10 Build 20241 ou posterior são necessários para hospedar serviços gRPC com o IIS.</span><span class="sxs-lookup"><span data-stu-id="8118a-164">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="8118a-165">O IIS deve ser configurado para usar TLS e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8118a-165">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="8118a-166">Para obter mais informações, consulte <xref:host-and-deploy/iis/protocols>.</span><span class="sxs-lookup"><span data-stu-id="8118a-166">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="8118a-167">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="8118a-167">HTTP.sys</span></span>

<span data-ttu-id="8118a-168">O [HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor Web para ASP.NET Core executado apenas no Windows.</span><span class="sxs-lookup"><span data-stu-id="8118a-168">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="8118a-169">O .NET 5 e o Windows 10 Build 19529 ou posterior são necessários para hospedar serviços gRPCs com HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="8118a-169">.NET 5 and Windows 10 Build 19529 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="8118a-170">HTTP.sys deve ser configurado para usar TLS e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8118a-170">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="8118a-171">Para obter mais informações, consulte  [ suporte ao servidor Web doHTTP.sys http/2](xref:fundamentals/servers/httpsys#http2-support).</span><span class="sxs-lookup"><span data-stu-id="8118a-171">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="8118a-172">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8118a-172">Kestrel</span></span>

<span data-ttu-id="8118a-173">[Kestrel](xref:fundamentals/servers/kestrel) é um servidor Web de plataforma cruzada para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8118a-173">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="8118a-174">O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados em HTTP.sys como o compartilhamento de porta.</span><span class="sxs-lookup"><span data-stu-id="8118a-174">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="8118a-175">Kestrel pontos de extremidade gRPC:</span><span class="sxs-lookup"><span data-stu-id="8118a-175">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="8118a-176">Exigir HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8118a-176">Require HTTP/2.</span></span>
* <span data-ttu-id="8118a-177">Deve ser protegido com [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="8118a-177">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="8118a-178">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8118a-178">HTTP/2</span></span>

<span data-ttu-id="8118a-179">gRPC requer HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8118a-179">gRPC requires HTTP/2.</span></span> <span data-ttu-id="8118a-180">gRPC para ASP.NET Core valida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) é `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="8118a-180">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="8118a-181">O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos.</span><span class="sxs-lookup"><span data-stu-id="8118a-181">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="8118a-182">Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.</span><span class="sxs-lookup"><span data-stu-id="8118a-182">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="8118a-183">TLS</span><span class="sxs-lookup"><span data-stu-id="8118a-183">TLS</span></span>

<span data-ttu-id="8118a-184">Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com TLS.</span><span class="sxs-lookup"><span data-stu-id="8118a-184">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="8118a-185">No desenvolvimento, um ponto de extremidade protegido com TLS é criado automaticamente `https://localhost:5001` quando o certificado de desenvolvimento de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="8118a-185">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="8118a-186">Nenhuma configuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="8118a-186">No configuration is required.</span></span> <span data-ttu-id="8118a-187">Um `https` prefixo verifica se o ponto de extremidade Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="8118a-187">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="8118a-188">Em produção, o TLS deve ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="8118a-188">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="8118a-189">No exemplo a seguir *appsettings.json* , um ponto de extremidade http/2 protegido com TLS é fornecido:</span><span class="sxs-lookup"><span data-stu-id="8118a-189">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="8118a-190">Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8118a-190">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="8118a-191">Negociação de protocolo</span><span class="sxs-lookup"><span data-stu-id="8118a-191">Protocol negotiation</span></span>

<span data-ttu-id="8118a-192">O TLS é usado para mais do que proteger a comunicação.</span><span class="sxs-lookup"><span data-stu-id="8118a-192">TLS is used for more than securing communication.</span></span> <span data-ttu-id="8118a-193">O handshake [ALPN (negociação do protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto de extremidade dá suporte a vários protocolos.</span><span class="sxs-lookup"><span data-stu-id="8118a-193">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="8118a-194">Essa negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8118a-194">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="8118a-195">Se um ponto de extremidade HTTP/2 estiver configurado sem TLS, os [protocolos de escuta](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deverão ser definidos como `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="8118a-195">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="8118a-196">Um ponto de extremidade com vários protocolos (por exemplo, `HttpProtocols.Http1AndHttp2` ) não pode ser usado sem TLS porque não há negociação.</span><span class="sxs-lookup"><span data-stu-id="8118a-196">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="8118a-197">Todas as conexões com o ponto de extremidade não seguro padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="8118a-197">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="8118a-198">Para obter mais informações sobre como habilitar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="8118a-198">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="8118a-199">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="8118a-199">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="8118a-200">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="8118a-200">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="8118a-201">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="8118a-201">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="8118a-202">Integração com APIs de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8118a-202">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="8118a-203">os serviços gRPCs têm acesso completo aos recursos de ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (di) e [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8118a-203">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8118a-204">Por exemplo, a implementação do serviço pode resolver um serviço de agente do contêiner DI por meio do Construtor:</span><span class="sxs-lookup"><span data-stu-id="8118a-204">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="8118a-205">Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (singleton, com escopo ou transitório).</span><span class="sxs-lookup"><span data-stu-id="8118a-205">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="8118a-206">Resolver HttpContext em métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="8118a-206">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="8118a-207">A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, o host, o cabeçalho e os trailers.</span><span class="sxs-lookup"><span data-stu-id="8118a-207">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="8118a-208">O acesso é por meio do `ServerCallContext` argumento passado para cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="8118a-208">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="8118a-209">`ServerCallContext` não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.net.</span><span class="sxs-lookup"><span data-stu-id="8118a-209">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="8118a-210">O `GetHttpContext` método de extensão fornece acesso completo à `HttpContext` representação da mensagem http/2 subjacente em APIs ASP.net:</span><span class="sxs-lookup"><span data-stu-id="8118a-210">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="8118a-211">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8118a-211">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
