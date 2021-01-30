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
ms.openlocfilehash: 57edfa31079cb3fca6e9e8d0fa55bcbb8bbfefca
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057468"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="ae411-103">Serviços do gRPC com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae411-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="ae411-104">Este documento mostra como começar a usar os serviços gRPCs usando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae411-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="ae411-105">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ae411-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ae411-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae411-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ae411-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ae411-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ae411-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ae411-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="ae411-109">Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae411-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="ae411-110">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ae411-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ae411-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae411-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae411-112">Consulte Introdução [aos serviços gRPCs](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.</span><span class="sxs-lookup"><span data-stu-id="ae411-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ae411-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ae411-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ae411-114">Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="ae411-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="ae411-115">Adicionar serviços gRPCs a um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae411-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="ae411-116">gRPC requer o pacote [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="ae411-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="ae411-117">Configurar o gRPC</span><span class="sxs-lookup"><span data-stu-id="ae411-117">Configure gRPC</span></span>

<span data-ttu-id="ae411-118">Em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae411-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="ae411-119">gRPC é habilitado com o `AddGrpc` método.</span><span class="sxs-lookup"><span data-stu-id="ae411-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="ae411-120">Cada serviço gRPC é adicionado ao pipeline de roteamento por meio do `MapGrpcService` método.</span><span class="sxs-lookup"><span data-stu-id="ae411-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ae411-121">ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais.</span><span class="sxs-lookup"><span data-stu-id="ae411-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="ae411-122">Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.</span><span class="sxs-lookup"><span data-stu-id="ae411-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="ae411-123">Opções de servidor</span><span class="sxs-lookup"><span data-stu-id="ae411-123">Server options</span></span>

<span data-ttu-id="ae411-124">os serviços gRPCs podem ser hospedados por todos os servidores de ASP.NET Core internos.</span><span class="sxs-lookup"><span data-stu-id="ae411-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="ae411-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ae411-125">Kestrel</span></span>
> * <span data-ttu-id="ae411-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="ae411-126">TestServer</span></span>
> * <span data-ttu-id="ae411-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="ae411-127">IIS&dagger;</span></span>
> * <span data-ttu-id="ae411-128">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="ae411-128">HTTP.sys&dagger;</span></span>

<span data-ttu-id="ae411-129">&dagger;O IIS e o HTTP.sys exigem o .NET 5 e o Windows 10 Build 20241 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="ae411-129">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="ae411-130">Para obter mais informações sobre como escolher o servidor certo para um aplicativo ASP.NET Core, consulte <xref:fundamentals/servers/index> .</span><span class="sxs-lookup"><span data-stu-id="ae411-130">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="ae411-131">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ae411-131">Kestrel</span></span>

<span data-ttu-id="ae411-132">[Kestrel](xref:fundamentals/servers/kestrel) é um servidor Web de plataforma cruzada para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae411-132">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="ae411-133">O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados em HTTP.sys como o compartilhamento de porta.</span><span class="sxs-lookup"><span data-stu-id="ae411-133">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="ae411-134">Kestrel pontos de extremidade gRPC:</span><span class="sxs-lookup"><span data-stu-id="ae411-134">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="ae411-135">Exigir HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ae411-135">Require HTTP/2.</span></span>
* <span data-ttu-id="ae411-136">Deve ser protegido com [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="ae411-136">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="ae411-137">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ae411-137">HTTP/2</span></span>

<span data-ttu-id="ae411-138">gRPC requer HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ae411-138">gRPC requires HTTP/2.</span></span> <span data-ttu-id="ae411-139">gRPC para ASP.NET Core valida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) é `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="ae411-139">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="ae411-140">O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel/http2) na maioria dos sistemas operacionais modernos.</span><span class="sxs-lookup"><span data-stu-id="ae411-140">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="ae411-141">Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.</span><span class="sxs-lookup"><span data-stu-id="ae411-141">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="ae411-142">TLS</span><span class="sxs-lookup"><span data-stu-id="ae411-142">TLS</span></span>

<span data-ttu-id="ae411-143">Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com TLS.</span><span class="sxs-lookup"><span data-stu-id="ae411-143">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="ae411-144">No desenvolvimento, um ponto de extremidade protegido com TLS é criado automaticamente `https://localhost:5001` quando o certificado de desenvolvimento de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="ae411-144">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="ae411-145">Nenhuma configuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="ae411-145">No configuration is required.</span></span> <span data-ttu-id="ae411-146">Um `https` prefixo verifica se o ponto de extremidade Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="ae411-146">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="ae411-147">Em produção, o TLS deve ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="ae411-147">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="ae411-148">No exemplo a seguir *appsettings.json* , um ponto de extremidade http/2 protegido com TLS é fornecido:</span><span class="sxs-lookup"><span data-stu-id="ae411-148">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="ae411-149">Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae411-149">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="ae411-150">Negociação de protocolo</span><span class="sxs-lookup"><span data-stu-id="ae411-150">Protocol negotiation</span></span>

<span data-ttu-id="ae411-151">O TLS é usado para mais do que proteger a comunicação.</span><span class="sxs-lookup"><span data-stu-id="ae411-151">TLS is used for more than securing communication.</span></span> <span data-ttu-id="ae411-152">O handshake [ALPN (negociação do protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto de extremidade dá suporte a vários protocolos.</span><span class="sxs-lookup"><span data-stu-id="ae411-152">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="ae411-153">Essa negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ae411-153">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="ae411-154">Se um ponto de extremidade HTTP/2 estiver configurado sem TLS, os [protocolos de escuta](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) do ponto de extremidade deverão ser definidos como `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="ae411-154">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="ae411-155">Um ponto de extremidade com vários protocolos (por exemplo, `HttpProtocols.Http1AndHttp2` ) não pode ser usado sem TLS porque não há negociação.</span><span class="sxs-lookup"><span data-stu-id="ae411-155">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="ae411-156">Todas as conexões com o ponto de extremidade não seguro padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="ae411-156">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="ae411-157">Para obter mais informações sobre como habilitar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="ae411-157">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="ae411-158">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="ae411-158">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="ae411-159">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="ae411-159">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="ae411-160">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="ae411-160">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="ae411-161">IIS</span><span class="sxs-lookup"><span data-stu-id="ae411-161">IIS</span></span>

<span data-ttu-id="ae411-162">O [serviços de informações da Internet (IIS)](xref:host-and-deploy/iis/index) é um servidor Web flexível, seguro e gerenciável para hospedar aplicativos Web, incluindo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae411-162">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="ae411-163">O .NET 5 e o Windows 10 Build 20241 ou posterior são necessários para hospedar serviços gRPC com o IIS.</span><span class="sxs-lookup"><span data-stu-id="ae411-163">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="ae411-164">O IIS deve ser configurado para usar TLS e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ae411-164">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="ae411-165">Para obter mais informações, consulte <xref:host-and-deploy/iis/protocols>.</span><span class="sxs-lookup"><span data-stu-id="ae411-165">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="ae411-166">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ae411-166">HTTP.sys</span></span>

<span data-ttu-id="ae411-167">O [HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor Web para ASP.NET Core executado apenas no Windows.</span><span class="sxs-lookup"><span data-stu-id="ae411-167">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="ae411-168">O .NET 5 e o Windows 10 Build 20241 ou posterior são necessários para hospedar serviços gRPCs com HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="ae411-168">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="ae411-169">HTTP.sys deve ser configurado para usar TLS e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ae411-169">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="ae411-170">Para obter mais informações, consulte  [ suporte ao servidor Web doHTTP.sys http/2](xref:fundamentals/servers/httpsys#http2-support).</span><span class="sxs-lookup"><span data-stu-id="ae411-170">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="ae411-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ae411-171">Kestrel</span></span>

<span data-ttu-id="ae411-172">[Kestrel](xref:fundamentals/servers/kestrel) é um servidor Web de plataforma cruzada para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae411-172">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="ae411-173">O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados em HTTP.sys como o compartilhamento de porta.</span><span class="sxs-lookup"><span data-stu-id="ae411-173">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="ae411-174">Kestrel pontos de extremidade gRPC:</span><span class="sxs-lookup"><span data-stu-id="ae411-174">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="ae411-175">Exigir HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ae411-175">Require HTTP/2.</span></span>
* <span data-ttu-id="ae411-176">Deve ser protegido com [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="ae411-176">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="ae411-177">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ae411-177">HTTP/2</span></span>

<span data-ttu-id="ae411-178">gRPC requer HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ae411-178">gRPC requires HTTP/2.</span></span> <span data-ttu-id="ae411-179">gRPC para ASP.NET Core valida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) é `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="ae411-179">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="ae411-180">O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos.</span><span class="sxs-lookup"><span data-stu-id="ae411-180">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="ae411-181">Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.</span><span class="sxs-lookup"><span data-stu-id="ae411-181">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="ae411-182">TLS</span><span class="sxs-lookup"><span data-stu-id="ae411-182">TLS</span></span>

<span data-ttu-id="ae411-183">Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com TLS.</span><span class="sxs-lookup"><span data-stu-id="ae411-183">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="ae411-184">No desenvolvimento, um ponto de extremidade protegido com TLS é criado automaticamente `https://localhost:5001` quando o certificado de desenvolvimento de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="ae411-184">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="ae411-185">Nenhuma configuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="ae411-185">No configuration is required.</span></span> <span data-ttu-id="ae411-186">Um `https` prefixo verifica se o ponto de extremidade Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="ae411-186">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="ae411-187">Em produção, o TLS deve ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="ae411-187">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="ae411-188">No exemplo a seguir *appsettings.json* , um ponto de extremidade http/2 protegido com TLS é fornecido:</span><span class="sxs-lookup"><span data-stu-id="ae411-188">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="ae411-189">Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae411-189">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="ae411-190">Negociação de protocolo</span><span class="sxs-lookup"><span data-stu-id="ae411-190">Protocol negotiation</span></span>

<span data-ttu-id="ae411-191">O TLS é usado para mais do que proteger a comunicação.</span><span class="sxs-lookup"><span data-stu-id="ae411-191">TLS is used for more than securing communication.</span></span> <span data-ttu-id="ae411-192">O handshake [ALPN (negociação do protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto de extremidade dá suporte a vários protocolos.</span><span class="sxs-lookup"><span data-stu-id="ae411-192">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="ae411-193">Essa negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ae411-193">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="ae411-194">Se um ponto de extremidade HTTP/2 estiver configurado sem TLS, os [protocolos de escuta](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deverão ser definidos como `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="ae411-194">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="ae411-195">Um ponto de extremidade com vários protocolos (por exemplo, `HttpProtocols.Http1AndHttp2` ) não pode ser usado sem TLS porque não há negociação.</span><span class="sxs-lookup"><span data-stu-id="ae411-195">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="ae411-196">Todas as conexões com o ponto de extremidade não seguro padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="ae411-196">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="ae411-197">Para obter mais informações sobre como habilitar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="ae411-197">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="ae411-198">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="ae411-198">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="ae411-199">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="ae411-199">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="ae411-200">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="ae411-200">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="ae411-201">Integração com APIs de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae411-201">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="ae411-202">os serviços gRPCs têm acesso completo aos recursos de ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (di) e [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="ae411-202">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="ae411-203">Por exemplo, a implementação do serviço pode resolver um serviço de agente do contêiner DI por meio do Construtor:</span><span class="sxs-lookup"><span data-stu-id="ae411-203">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="ae411-204">Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (singleton, com escopo ou transitório).</span><span class="sxs-lookup"><span data-stu-id="ae411-204">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="ae411-205">Resolver HttpContext em métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="ae411-205">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="ae411-206">A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, o host, o cabeçalho e os trailers.</span><span class="sxs-lookup"><span data-stu-id="ae411-206">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="ae411-207">O acesso é por meio do `ServerCallContext` argumento passado para cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="ae411-207">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="ae411-208">`ServerCallContext` não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.net.</span><span class="sxs-lookup"><span data-stu-id="ae411-208">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="ae411-209">O `GetHttpContext` método de extensão fornece acesso completo à `HttpContext` representação da mensagem http/2 subjacente em APIs ASP.net:</span><span class="sxs-lookup"><span data-stu-id="ae411-209">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="ae411-210">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ae411-210">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
