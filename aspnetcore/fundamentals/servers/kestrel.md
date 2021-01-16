---
title: Implementação do servidor Web Kestrel no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o Kestrel, o servidor Web multiplataforma do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 268a6e71d3bd290ed614e70d963d653924cdcc43
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252728"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="78bf0-103">Implementação do servidor Web Kestrel no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78bf0-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="78bf0-104">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="78bf0-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="78bf0-105">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="78bf0-106">Kestrel é o servidor Web que está incluído e habilitado por padrão em ASP.NET Core modelos de projeto.</span><span class="sxs-lookup"><span data-stu-id="78bf0-106">Kestrel is the web server that's included and enabled by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="78bf0-107">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="78bf0-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="78bf0-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="78bf0-108">HTTPS</span></span>
* <span data-ttu-id="78bf0-109">[Http/2](xref:fundamentals/servers/kestrel/http2) (exceto no MacOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="78bf0-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (except on macOS&dagger;)</span></span>
* <span data-ttu-id="78bf0-110">Atualização do Opaque usado para habilitar o [WebSockets](xref:fundamentals/websockets)</span><span class="sxs-lookup"><span data-stu-id="78bf0-110">Opaque upgrade used to enable [WebSockets](xref:fundamentals/websockets)</span></span>
* <span data-ttu-id="78bf0-111">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="78bf0-111">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="78bf0-112">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="78bf0-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="78bf0-113">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bf0-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="78bf0-114">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78bf0-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started"></a><span data-ttu-id="78bf0-115">Introdução</span><span class="sxs-lookup"><span data-stu-id="78bf0-115">Get started</span></span>

<span data-ttu-id="78bf0-116">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-116">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="78bf0-117">No *Program.cs*, o <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> método chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="78bf0-117">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="78bf0-118">Para obter mais informações sobre como criar o host, consulte as seções *configurar um host* e *as configurações do construtor padrão* de <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="78bf0-118">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

## <a name="additional-resournces"></a><span data-ttu-id="78bf0-119">Técnicos adicionais</span><span class="sxs-lookup"><span data-stu-id="78bf0-119">Additional resournces</span></span>

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="78bf0-120">RFC 7230: Sintaxe e roteamento da mensagem (Seção 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="78bf0-120">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* <span data-ttu-id="78bf0-121">Ao usar soquetes UNIX no Linux, o soquete não é excluído automaticamente no aplicativo desligado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-121">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="78bf0-122">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="78bf0-122">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>

> [!NOTE]
> <span data-ttu-id="78bf0-123">A partir de ASP.NET Core 5,0, o transporte de libuv do Kestrel é obsoleto.</span><span class="sxs-lookup"><span data-stu-id="78bf0-123">As of ASP.NET Core 5.0, Kestrel's libuv transport is obsolete.</span></span> <span data-ttu-id="78bf0-124">O transporte libuv não recebe atualizações para dar suporte a novas plataformas de sistema operacional, como o Windows ARM64, e será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="78bf0-124">The libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="78bf0-125">Remova todas as chamadas para o <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> método obsoleto e use o transporte de soquete padrão do Kestrel em vez disso.</span><span class="sxs-lookup"><span data-stu-id="78bf0-125">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="78bf0-126">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-126">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="78bf0-127">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bf0-127">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="78bf0-128">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="78bf0-128">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="78bf0-129">HTTPS</span><span class="sxs-lookup"><span data-stu-id="78bf0-129">HTTPS</span></span>
* <span data-ttu-id="78bf0-130">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="78bf0-130">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="78bf0-131">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="78bf0-131">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="78bf0-132">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="78bf0-132">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="78bf0-133">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="78bf0-133">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="78bf0-134">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bf0-134">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="78bf0-135">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78bf0-135">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="78bf0-136">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="78bf0-136">HTTP/2 support</span></span>

<span data-ttu-id="78bf0-137">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="78bf0-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="78bf0-138">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="78bf0-138">Operating system&dagger;</span></span>
  * <span data-ttu-id="78bf0-139">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="78bf0-139">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="78bf0-140">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="78bf0-140">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="78bf0-141">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="78bf0-141">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="78bf0-142">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="78bf0-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="78bf0-143">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="78bf0-143">TLS 1.2 or later connection</span></span>

<span data-ttu-id="78bf0-144">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="78bf0-144">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="78bf0-145">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="78bf0-145">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="78bf0-146">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-146">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="78bf0-147">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-147">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="78bf0-148">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-148">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="78bf0-149">A partir do .NET Core 3,0, o HTTP/2 é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-149">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="78bf0-150">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="78bf0-150">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="78bf0-151">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="78bf0-151">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="78bf0-152">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="78bf0-152">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="78bf0-153">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-153">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="78bf0-154">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="78bf0-154">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="78bf0-156">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="78bf0-156">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="78bf0-158">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="78bf0-158">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="78bf0-159">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-159">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="78bf0-160">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="78bf0-160">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="78bf0-161">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-161">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="78bf0-162">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-162">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="78bf0-163">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="78bf0-163">A reverse proxy:</span></span>

* <span data-ttu-id="78bf0-164">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="78bf0-164">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="78bf0-165">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-165">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="78bf0-166">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-166">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="78bf0-167">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="78bf0-167">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="78bf0-168">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="78bf0-168">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="78bf0-169">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="78bf0-169">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="78bf0-170">KESTREL em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78bf0-170">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="78bf0-171">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-171">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="78bf0-172">No *Program.cs*, o <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> método chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="78bf0-172">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="78bf0-173">Para obter mais informações sobre como criar o host, consulte as seções *configurar um host* e *as configurações do construtor padrão* de <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="78bf0-173">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="78bf0-174">Para fornecer configuração adicional após chamar `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-174">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="78bf0-175">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="78bf0-175">Kestrel options</span></span>

<span data-ttu-id="78bf0-176">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="78bf0-176">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="78bf0-177">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-177">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="78bf0-178">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-178">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="78bf0-179">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-179">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="78bf0-180">Nos exemplos mostrados posteriormente neste artigo, as opções de Kestrel são configuradas no código C#.</span><span class="sxs-lookup"><span data-stu-id="78bf0-180">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="78bf0-181">As opções de Kestrel também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-181">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="78bf0-182">Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider) pode carregar a configuração Kestrel de um *appsettings.json* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="78bf0-182">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="78bf0-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> e a [configuração do ponto de extremidade](#endpoint-configuration) são configuráveis de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="78bf0-184">A configuração restante do Kestrel deve ser configurada no código C#.</span><span class="sxs-lookup"><span data-stu-id="78bf0-184">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="78bf0-185">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="78bf0-185">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="78bf0-186">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78bf0-186">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="78bf0-187">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="78bf0-187">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="78bf0-188">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-188">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="78bf0-189">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="78bf0-189">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="78bf0-190">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="78bf0-190">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="78bf0-191">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="78bf0-191">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="78bf0-192">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-192">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="78bf0-193">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="78bf0-193">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="78bf0-194">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="78bf0-194">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="78bf0-195">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-195">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="78bf0-196">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="78bf0-196">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="78bf0-197">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="78bf0-197">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="78bf0-198">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="78bf0-198">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="78bf0-199">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-199">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="78bf0-200">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-200">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="78bf0-201">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-201">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="78bf0-202">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="78bf0-202">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="78bf0-203">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="78bf0-203">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="78bf0-204">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="78bf0-204">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="78bf0-205">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="78bf0-205">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="78bf0-206">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-206">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="78bf0-207">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="78bf0-207">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="78bf0-208">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="78bf0-208">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="78bf0-209">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-209">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="78bf0-210">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="78bf0-210">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="78bf0-211">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="78bf0-211">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="78bf0-212">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="78bf0-212">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="78bf0-213">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-213">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="78bf0-214">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-214">A minimum rate also applies to the response.</span></span> <span data-ttu-id="78bf0-215">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="78bf0-215">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="78bf0-216">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="78bf0-216">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="78bf0-217">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="78bf0-217">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="78bf0-218">O <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenciado no exemplo anterior não está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é geralmente compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="78bf0-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="78bf0-219">No entanto, o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ainda está presente em `HttpContext.Features` para solicitações HTTP/2, pois o limite de taxa de leitura ainda pode ser *desabilitado totalmente* em cada solicitação, definindo `IHttpMinRequestBodyDataRateFeature.MinDataRate` para `null` mesmo em uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-219">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="78bf0-220">Tentar ler `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou tentar defini-lo como um valor diferente de `null` resultará na geração de um `NotSupportedException` devido a uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-220">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="78bf0-221">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-221">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="78bf0-222">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-222">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="78bf0-223">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-223">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="78bf0-224">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-224">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="78bf0-225">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="78bf0-225">Maximum streams per connection</span></span>

<span data-ttu-id="78bf0-226">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-226">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="78bf0-227">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-227">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="78bf0-228">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="78bf0-228">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="78bf0-229">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="78bf0-229">Header table size</span></span>

<span data-ttu-id="78bf0-230">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-230">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="78bf0-231">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="78bf0-231">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="78bf0-232">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="78bf0-232">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="78bf0-233">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="78bf0-233">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="78bf0-234">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="78bf0-234">Maximum frame size</span></span>

<span data-ttu-id="78bf0-235">`Http2.MaxFrameSize` indica o tamanho máximo permitido de um conteúdo de quadro de conexão HTTP/2 recebido ou enviado pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="78bf0-235">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="78bf0-236">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="78bf0-236">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="78bf0-237">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="78bf0-237">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="78bf0-238">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-238">Maximum request header size</span></span>

<span data-ttu-id="78bf0-239">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-239">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="78bf0-240">Esse limite se aplica tanto ao nome quanto ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-240">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="78bf0-241">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="78bf0-241">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="78bf0-242">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="78bf0-242">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="78bf0-243">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="78bf0-243">Initial connection window size</span></span>

<span data-ttu-id="78bf0-244">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-244">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="78bf0-245">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-245">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="78bf0-246">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="78bf0-246">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="78bf0-247">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="78bf0-247">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="78bf0-248">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="78bf0-248">Initial stream window size</span></span>

<span data-ttu-id="78bf0-249">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="78bf0-249">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="78bf0-250">As solicitações também são limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-250">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="78bf0-251">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="78bf0-251">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="78bf0-252">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="78bf0-252">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="78bf0-253">Trailers</span><span class="sxs-lookup"><span data-stu-id="78bf0-253">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="78bf0-254">Redefinir</span><span class="sxs-lookup"><span data-stu-id="78bf0-254">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="78bf0-255">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="78bf0-255">Synchronous I/O</span></span>

<span data-ttu-id="78bf0-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="78bf0-257">O valor padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-257">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="78bf0-258">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-258">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="78bf0-259">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="78bf0-259">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="78bf0-260">O exemplo a seguir habilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="78bf0-260">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="78bf0-261">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="78bf0-261">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="78bf0-262">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="78bf0-262">Endpoint configuration</span></span>

<span data-ttu-id="78bf0-263">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="78bf0-263">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="78bf0-264">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="78bf0-264">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="78bf0-265">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="78bf0-265">Specify URLs using the:</span></span>

* <span data-ttu-id="78bf0-266">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-266">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="78bf0-267">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-267">`--urls` command-line argument.</span></span>
* <span data-ttu-id="78bf0-268">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-268">`urls` host configuration key.</span></span>
* <span data-ttu-id="78bf0-269">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-269">`UseUrls` extension method.</span></span>

<span data-ttu-id="78bf0-270">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="78bf0-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="78bf0-271">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="78bf0-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="78bf0-272">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="78bf0-272">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="78bf0-273">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-273">A development certificate is created:</span></span>

* <span data-ttu-id="78bf0-274">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-274">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="78bf0-275">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-275">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="78bf0-276">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="78bf0-276">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="78bf0-277">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="78bf0-277">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="78bf0-278">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-278">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="78bf0-279">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="78bf0-279">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="78bf0-280">`KestrelServerOptions` configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-280">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="78bf0-281">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="78bf0-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="78bf0-282">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-282">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="78bf0-283">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-283">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="78bf0-284">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="78bf0-285">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="78bf0-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="78bf0-286">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-286">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="78bf0-287">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-287">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="78bf0-288">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-288">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="78bf0-289">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="78bf0-289">Configure(IConfiguration)</span></span>

<span data-ttu-id="78bf0-290">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-290">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="78bf0-291">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-291">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="78bf0-292">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="78bf0-292">ListenOptions.UseHttps</span></span>

<span data-ttu-id="78bf0-293">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-293">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="78bf0-294">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-294">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="78bf0-295">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-295">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="78bf0-296">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-296">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="78bf0-297">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-297">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="78bf0-298">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="78bf0-298">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="78bf0-299">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="78bf0-299">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="78bf0-300">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-300">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="78bf0-301">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-301">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="78bf0-302">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-302">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="78bf0-303">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-303">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="78bf0-304">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-304">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="78bf0-305">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-305">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="78bf0-306">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="78bf0-306">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="78bf0-307">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-307">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="78bf0-308">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="78bf0-308">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="78bf0-309">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="78bf0-309">Supported configurations described next:</span></span>

* <span data-ttu-id="78bf0-310">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-310">No configuration</span></span>
* <span data-ttu-id="78bf0-311">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-311">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="78bf0-312">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="78bf0-312">Change the defaults in code</span></span>

<span data-ttu-id="78bf0-313">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="78bf0-313">*No configuration*</span></span>

<span data-ttu-id="78bf0-314">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="78bf0-314">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="78bf0-315">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="78bf0-315">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="78bf0-316">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-316">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="78bf0-317">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-317">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="78bf0-318">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-318">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="78bf0-319">No exemplo a seguir *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="78bf0-319">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="78bf0-320">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="78bf0-320">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="78bf0-321">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="78bf0-321">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="78bf0-322">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-322">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="78bf0-323">Por exemplo, o   >  certificado **padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="78bf0-323">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="78bf0-324">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="78bf0-324">Schema notes:</span></span>

* <span data-ttu-id="78bf0-325">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-325">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="78bf0-326">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-326">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="78bf0-327">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-327">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="78bf0-328">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="78bf0-328">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="78bf0-329">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="78bf0-329">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="78bf0-330">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-330">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="78bf0-331">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="78bf0-331">The `Certificate` section is optional.</span></span> <span data-ttu-id="78bf0-332">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-332">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="78bf0-333">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-333">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="78bf0-334">A `Certificate` seção dá suporte a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="78bf0-334">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="78bf0-335">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-335">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="78bf0-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="78bf0-337">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="78bf0-337">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="78bf0-338">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-338">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="78bf0-339">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-339">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="78bf0-340">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="78bf0-340">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="78bf0-341">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="78bf0-341">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="78bf0-342">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="78bf0-342">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="78bf0-343">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-343">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="78bf0-344">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="78bf0-344">*Change the defaults in code*</span></span>

<span data-ttu-id="78bf0-345">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="78bf0-345">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="78bf0-346">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-346">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="78bf0-347">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="78bf0-347">*Kestrel support for SNI*</span></span>

<span data-ttu-id="78bf0-348">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-348">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="78bf0-349">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="78bf0-349">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="78bf0-350">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-350">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="78bf0-351">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-351">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="78bf0-352">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-352">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="78bf0-353">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="78bf0-353">SNI support requires:</span></span>

* <span data-ttu-id="78bf0-354">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="78bf0-354">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="78bf0-355">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="78bf0-355">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="78bf0-356">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-356">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="78bf0-357">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-357">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="78bf0-358">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="78bf0-358">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="78bf0-359">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="78bf0-359">Connection logging</span></span>

<span data-ttu-id="78bf0-360">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-360">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="78bf0-361">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="78bf0-361">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="78bf0-362">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-362">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="78bf0-363">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-363">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="78bf0-364">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="78bf0-364">Bind to a TCP socket</span></span>

<span data-ttu-id="78bf0-365">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="78bf0-365">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="78bf0-366">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-366">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="78bf0-367">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-367">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="78bf0-368">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="78bf0-368">Bind to a Unix socket</span></span>

<span data-ttu-id="78bf0-369">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="78bf0-369">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="78bf0-370">No arquivo de configuração Nginx, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="78bf0-370">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="78bf0-371">`{KESTREL SOCKET}` é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="78bf0-371">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="78bf0-372">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="78bf0-372">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="78bf0-373">Porta 0</span><span class="sxs-lookup"><span data-stu-id="78bf0-373">Port 0</span></span>

<span data-ttu-id="78bf0-374">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="78bf0-374">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="78bf0-375">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="78bf0-375">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="78bf0-376">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-376">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="78bf0-377">Limitações</span><span class="sxs-lookup"><span data-stu-id="78bf0-377">Limitations</span></span>

<span data-ttu-id="78bf0-378">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="78bf0-378">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="78bf0-379">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="78bf0-379">`--urls` command-line argument</span></span>
* <span data-ttu-id="78bf0-380">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="78bf0-380">`urls` host configuration key</span></span>
* <span data-ttu-id="78bf0-381">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="78bf0-381">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="78bf0-382">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-382">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="78bf0-383">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="78bf0-383">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="78bf0-384">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="78bf0-384">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="78bf0-385">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-385">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="78bf0-386">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="78bf0-386">IIS endpoint configuration</span></span>

<span data-ttu-id="78bf0-387">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-387">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="78bf0-388">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="78bf0-388">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="78bf0-389">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="78bf0-389">ListenOptions.Protocols</span></span>

<span data-ttu-id="78bf0-390">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="78bf0-390">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="78bf0-391">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-391">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="78bf0-392">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="78bf0-392">`HttpProtocols` enum value</span></span> | <span data-ttu-id="78bf0-393">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="78bf0-393">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="78bf0-394">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-394">HTTP/1.1 only.</span></span> <span data-ttu-id="78bf0-395">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-395">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="78bf0-396">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-396">HTTP/2 only.</span></span> <span data-ttu-id="78bf0-397">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="78bf0-397">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="78bf0-398">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-398">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="78bf0-399">O HTTP/2 requer que o cliente selecione HTTP/2 no handshake de [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78bf0-399">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="78bf0-400">O `ListenOptions.Protocols` valor padrão para qualquer ponto de extremidade é `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="78bf0-400">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="78bf0-401">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="78bf0-401">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="78bf0-402">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="78bf0-402">TLS version 1.2 or later</span></span>
* <span data-ttu-id="78bf0-403">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="78bf0-403">Renegotiation disabled</span></span>
* <span data-ttu-id="78bf0-404">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="78bf0-404">Compression disabled</span></span>
* <span data-ttu-id="78bf0-405">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="78bf0-405">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="78bf0-406">Diffie-Hellman de curva elíptica (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="78bf0-406">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="78bf0-407">Diffie-Hellman de campo finito (DHE) &lbrack; `TLS12` &rbrack; : mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="78bf0-407">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="78bf0-408">Conjunto de codificação não proibido.</span><span class="sxs-lookup"><span data-stu-id="78bf0-408">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="78bf0-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 tem &lbrack; `FIPS186` &rbrack; suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="78bf0-410">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="78bf0-410">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="78bf0-411">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="78bf0-411">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="78bf0-412">Use o middleware de conexão para filtrar os Handshakes TLS em uma base por conexão para codificações específicas, se necessário.</span><span class="sxs-lookup"><span data-stu-id="78bf0-412">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="78bf0-413">O exemplo a seguir gera <xref:System.NotSupportedException> um algoritmo de codificação para o qual o aplicativo não dá suporte.</span><span class="sxs-lookup"><span data-stu-id="78bf0-413">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="78bf0-414">Como alternativa, defina e compare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) com uma lista de pacotes de codificação aceitáveis.</span><span class="sxs-lookup"><span data-stu-id="78bf0-414">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="78bf0-415">Nenhuma criptografia é usada com um algoritmo de codificação [CipherAlgorithmType. NULL](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="78bf0-415">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="78bf0-416">A filtragem de conexão também pode ser configurada por meio de um <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="78bf0-416">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="78bf0-417">No Linux, <xref:System.Net.Security.CipherSuitesPolicy> o pode ser usado para filtrar os Handshakes de TLS em uma base por conexão:</span><span class="sxs-lookup"><span data-stu-id="78bf0-417">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="78bf0-418">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="78bf0-418">*Set the protocol from configuration*</span></span>

<span data-ttu-id="78bf0-419">`CreateDefaultBuilder` chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-419">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="78bf0-420">O exemplo a seguir *appsettings.json* estabelece http/1.1 como o protocolo de conexão padrão para todos os pontos de extremidade:</span><span class="sxs-lookup"><span data-stu-id="78bf0-420">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="78bf0-421">O exemplo a seguir *appsettings.json* estabelece o protocolo de conexão HTTP/1.1 para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="78bf0-421">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="78bf0-422">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-422">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="78bf0-423">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="78bf0-423">URL prefixes</span></span>

<span data-ttu-id="78bf0-424">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="78bf0-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="78bf0-425">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="78bf0-426">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="78bf0-427">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="78bf0-428">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="78bf0-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="78bf0-429">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="78bf0-430">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="78bf0-431">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="78bf0-432">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="78bf0-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="78bf0-433">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="78bf0-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="78bf0-434">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="78bf0-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="78bf0-435">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="78bf0-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="78bf0-436">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="78bf0-437">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="78bf0-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="78bf0-438">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="78bf0-439">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="78bf0-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="78bf0-440">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="78bf0-440">Host filtering</span></span>

<span data-ttu-id="78bf0-441">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="78bf0-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="78bf0-442">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="78bf0-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="78bf0-443">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="78bf0-444">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="78bf0-445">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="78bf0-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="78bf0-446">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que é fornecido implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bf0-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="78bf0-447">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="78bf0-448">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="78bf0-449">Para habilitar o middleware, defina uma `AllowedHosts` chave em *appsettings.json* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="78bf0-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="78bf0-450">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="78bf0-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="78bf0-451">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="78bf0-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="78bf0-452">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="78bf0-453">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="78bf0-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="78bf0-454">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="78bf0-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="78bf0-455">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="78bf0-456">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="78bf0-457">Configuração de transporte Libuv</span><span class="sxs-lookup"><span data-stu-id="78bf0-457">Libuv transport configuration</span></span>

<span data-ttu-id="78bf0-458">Para projetos que exigem o uso de Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> ):</span><span class="sxs-lookup"><span data-stu-id="78bf0-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="78bf0-459">Adicione uma dependência para o [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="78bf0-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="78bf0-460">Ligue para <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> o `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="78bf0-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

## <a name="http11-request-draining"></a><span data-ttu-id="78bf0-461">Descarga de solicitação HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="78bf0-461">HTTP/1.1 request draining</span></span>

<span data-ttu-id="78bf0-462">A abertura de conexões HTTP é demorada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-462">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="78bf0-463">Para HTTPS, ele também consome muitos recursos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-463">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="78bf0-464">Portanto, o Kestrel tenta reutilizar conexões de acordo com o protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78bf0-464">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="78bf0-465">Um corpo de solicitação deve ser totalmente consumido para permitir que a conexão seja reutilizada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-465">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="78bf0-466">O aplicativo nem sempre consome o corpo da solicitação, como `POST` solicitações em que o servidor retorna uma resposta de redirecionamento ou 404.</span><span class="sxs-lookup"><span data-stu-id="78bf0-466">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="78bf0-467">No `POST` – caso de redirecionamento:</span><span class="sxs-lookup"><span data-stu-id="78bf0-467">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="78bf0-468">O cliente pode já ter enviado parte dos `POST` dados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-468">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="78bf0-469">O servidor grava a resposta 301.</span><span class="sxs-lookup"><span data-stu-id="78bf0-469">The server writes the 301 response.</span></span>
* <span data-ttu-id="78bf0-470">A conexão não pode ser usada para uma nova solicitação até que os `POST` dados do corpo da solicitação anterior tenham sido totalmente lidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-470">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="78bf0-471">Kestrel tenta drenar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-471">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="78bf0-472">Drenar o corpo da solicitação significa ler e descartar os dados sem processá-los.</span><span class="sxs-lookup"><span data-stu-id="78bf0-472">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="78bf0-473">O processo de descarga faz um tradoff entre permitir que a conexão seja reutilizada e o tempo necessário para drenar os dados restantes:</span><span class="sxs-lookup"><span data-stu-id="78bf0-473">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="78bf0-474">O descarregamento tem um tempo limite de cinco segundos, o que não é configurável.</span><span class="sxs-lookup"><span data-stu-id="78bf0-474">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="78bf0-475">Se todos os dados especificados pelo `Content-Length` `Transfer-Encoding` cabeçalho ou não tiverem sido lidos antes do tempo limite, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-475">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="78bf0-476">Às vezes, você pode querer encerrar a solicitação imediatamente, antes ou depois de gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-476">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="78bf0-477">Por exemplo, os clientes podem ter limites de dados restritivos, portanto, limitar os dados carregados pode ser uma prioridade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-477">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="78bf0-478">Nesses casos, para encerrar uma solicitação, chame [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) de um controlador, Razor página ou middleware.</span><span class="sxs-lookup"><span data-stu-id="78bf0-478">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="78bf0-479">Há limitações para chamar `Abort` :</span><span class="sxs-lookup"><span data-stu-id="78bf0-479">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="78bf0-480">A criação de novas conexões pode ser lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="78bf0-480">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="78bf0-481">Não há nenhuma garantia de que o cliente leu a resposta antes que a conexão seja fechada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-481">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="78bf0-482">`Abort`A chamada deve ser rara e reservada para casos de erro graves, não erros comuns.</span><span class="sxs-lookup"><span data-stu-id="78bf0-482">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="78bf0-483">Só chame `Abort` quando um problema específico precisar ser resolvido.</span><span class="sxs-lookup"><span data-stu-id="78bf0-483">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="78bf0-484">Por exemplo, chame `Abort` se clientes mal-intencionados estão tentando `POST` dados ou quando há um bug no código do cliente que causa grandes ou inúmeras solicitações.</span><span class="sxs-lookup"><span data-stu-id="78bf0-484">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="78bf0-485">Não chame `Abort` situações de erro comuns, como HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="78bf0-485">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="78bf0-486">Chamar [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes `Abort` de chamar garante que o servidor concluiu a gravação da resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-486">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="78bf0-487">No entanto, o comportamento do cliente não é previsível e pode não ler a resposta antes que a conexão seja anulada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-487">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="78bf0-488">Esse processo é diferente para HTTP/2 porque o protocolo dá suporte à anulação de fluxos de solicitação individuais sem fechar a conexão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-488">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="78bf0-489">O tempo limite de esgotamento de cinco segundos não se aplica.</span><span class="sxs-lookup"><span data-stu-id="78bf0-489">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="78bf0-490">Se houver algum dado de corpo de solicitação não lido depois de concluir uma resposta, o servidor enviará um quadro RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-490">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="78bf0-491">Quadros de dados de corpo de solicitação adicionais são ignorados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-491">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="78bf0-492">Se possível, é melhor para os clientes utilizarem o cabeçalho de solicitação [esperado: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e aguarde até que o servidor responda antes de começar a enviar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-492">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="78bf0-493">Isso dá ao cliente uma oportunidade de examinar a resposta e anular antes de enviar dados desnecessários.</span><span class="sxs-lookup"><span data-stu-id="78bf0-493">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78bf0-494">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="78bf0-494">Additional resources</span></span>

* <span data-ttu-id="78bf0-495">Ao usar soquetes UNIX no Linux, o soquete não é excluído automaticamente no aplicativo desligado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-495">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="78bf0-496">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="78bf0-496">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="78bf0-497">RFC 7230: Sintaxe e roteamento da mensagem (Seção 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="78bf0-497">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="78bf0-498">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-498">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="78bf0-499">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bf0-499">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="78bf0-500">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="78bf0-500">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="78bf0-501">HTTPS</span><span class="sxs-lookup"><span data-stu-id="78bf0-501">HTTPS</span></span>
* <span data-ttu-id="78bf0-502">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="78bf0-502">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="78bf0-503">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="78bf0-503">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="78bf0-504">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="78bf0-504">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="78bf0-505">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="78bf0-505">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="78bf0-506">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bf0-506">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="78bf0-507">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78bf0-507">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="78bf0-508">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="78bf0-508">HTTP/2 support</span></span>

<span data-ttu-id="78bf0-509">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="78bf0-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="78bf0-510">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="78bf0-510">Operating system&dagger;</span></span>
  * <span data-ttu-id="78bf0-511">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="78bf0-511">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="78bf0-512">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="78bf0-512">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="78bf0-513">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="78bf0-513">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="78bf0-514">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="78bf0-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="78bf0-515">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="78bf0-515">TLS 1.2 or later connection</span></span>

<span data-ttu-id="78bf0-516">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="78bf0-516">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="78bf0-517">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="78bf0-517">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="78bf0-518">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-518">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="78bf0-519">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-519">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="78bf0-520">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-520">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="78bf0-521">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-521">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="78bf0-522">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="78bf0-522">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="78bf0-523">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="78bf0-523">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="78bf0-524">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="78bf0-524">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="78bf0-525">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-525">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="78bf0-526">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="78bf0-526">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="78bf0-528">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="78bf0-528">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="78bf0-530">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="78bf0-530">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="78bf0-531">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-531">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="78bf0-532">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="78bf0-532">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="78bf0-533">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-533">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="78bf0-534">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-534">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="78bf0-535">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="78bf0-535">A reverse proxy:</span></span>

* <span data-ttu-id="78bf0-536">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="78bf0-536">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="78bf0-537">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-537">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="78bf0-538">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-538">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="78bf0-539">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="78bf0-539">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="78bf0-540">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="78bf0-540">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="78bf0-541">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="78bf0-541">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="78bf0-542">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78bf0-542">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="78bf0-543">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="78bf0-543">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="78bf0-544">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-544">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="78bf0-545">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="78bf0-545">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="78bf0-546">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="78bf0-546">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="78bf0-547">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-547">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="78bf0-548">Se o aplicativo não chamar `CreateDefaultBuilder` para configurar o host, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **antes** de chamar `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-548">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="78bf0-549">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="78bf0-549">Kestrel options</span></span>

<span data-ttu-id="78bf0-550">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="78bf0-550">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="78bf0-551">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-551">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="78bf0-552">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-552">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="78bf0-553">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-553">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="78bf0-554">As opções de Kestrel, que são configuradas no código C# nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-554">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="78bf0-555">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de um *appsettings.json* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="78bf0-555">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="78bf0-556">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="78bf0-556">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="78bf0-557">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78bf0-557">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="78bf0-558">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="78bf0-558">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="78bf0-559">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-559">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="78bf0-560">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="78bf0-560">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="78bf0-561">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="78bf0-561">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="78bf0-562">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="78bf0-562">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="78bf0-563">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-563">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="78bf0-564">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="78bf0-564">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="78bf0-565">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="78bf0-565">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="78bf0-566">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-566">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="78bf0-567">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="78bf0-567">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="78bf0-568">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="78bf0-568">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="78bf0-569">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="78bf0-569">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="78bf0-570">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-570">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="78bf0-571">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-571">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="78bf0-572">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-572">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="78bf0-573">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="78bf0-573">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="78bf0-574">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="78bf0-574">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="78bf0-575">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="78bf0-575">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="78bf0-576">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="78bf0-576">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="78bf0-577">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-577">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="78bf0-578">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="78bf0-578">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="78bf0-579">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="78bf0-579">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="78bf0-580">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-580">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="78bf0-581">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="78bf0-581">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="78bf0-582">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="78bf0-582">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="78bf0-583">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="78bf0-583">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="78bf0-584">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-584">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="78bf0-585">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-585">A minimum rate also applies to the response.</span></span> <span data-ttu-id="78bf0-586">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="78bf0-586">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="78bf0-587">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="78bf0-587">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="78bf0-588">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="78bf0-588">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="78bf0-589">Nenhum desses recursos de taxa referenciados no exemplo anterior está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="78bf0-589">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="78bf0-590">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-590">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="78bf0-591">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-591">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="78bf0-592">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-592">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="78bf0-593">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-593">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="78bf0-594">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="78bf0-594">Maximum streams per connection</span></span>

<span data-ttu-id="78bf0-595">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-595">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="78bf0-596">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-596">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="78bf0-597">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="78bf0-597">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="78bf0-598">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="78bf0-598">Header table size</span></span>

<span data-ttu-id="78bf0-599">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-599">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="78bf0-600">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="78bf0-600">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="78bf0-601">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="78bf0-601">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="78bf0-602">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="78bf0-602">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="78bf0-603">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="78bf0-603">Maximum frame size</span></span>

<span data-ttu-id="78bf0-604">O `Http2.MaxFrameSize` indica o tamanho máximo do payload do quadro da conexão HTTP/2 a ser recebido.</span><span class="sxs-lookup"><span data-stu-id="78bf0-604">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="78bf0-605">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="78bf0-605">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="78bf0-606">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="78bf0-606">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="78bf0-607">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-607">Maximum request header size</span></span>

<span data-ttu-id="78bf0-608">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-608">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="78bf0-609">Esse limite se aplica ao nome e ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-609">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="78bf0-610">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="78bf0-610">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="78bf0-611">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="78bf0-611">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="78bf0-612">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="78bf0-612">Initial connection window size</span></span>

<span data-ttu-id="78bf0-613">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-613">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="78bf0-614">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-614">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="78bf0-615">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="78bf0-615">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="78bf0-616">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="78bf0-616">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="78bf0-617">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="78bf0-617">Initial stream window size</span></span>

<span data-ttu-id="78bf0-618">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="78bf0-618">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="78bf0-619">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-619">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="78bf0-620">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="78bf0-620">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="78bf0-621">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="78bf0-621">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="78bf0-622">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="78bf0-622">Synchronous I/O</span></span>

<span data-ttu-id="78bf0-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="78bf0-624">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-624">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="78bf0-625">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-625">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="78bf0-626">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="78bf0-626">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="78bf0-627">O exemplo a seguir habilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="78bf0-627">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="78bf0-628">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="78bf0-628">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="78bf0-629">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="78bf0-629">Endpoint configuration</span></span>

<span data-ttu-id="78bf0-630">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="78bf0-630">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="78bf0-631">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="78bf0-631">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="78bf0-632">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="78bf0-632">Specify URLs using the:</span></span>

* <span data-ttu-id="78bf0-633">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-633">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="78bf0-634">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-634">`--urls` command-line argument.</span></span>
* <span data-ttu-id="78bf0-635">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-635">`urls` host configuration key.</span></span>
* <span data-ttu-id="78bf0-636">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-636">`UseUrls` extension method.</span></span>

<span data-ttu-id="78bf0-637">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="78bf0-637">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="78bf0-638">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="78bf0-638">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="78bf0-639">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="78bf0-639">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="78bf0-640">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-640">A development certificate is created:</span></span>

* <span data-ttu-id="78bf0-641">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-641">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="78bf0-642">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-642">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="78bf0-643">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="78bf0-643">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="78bf0-644">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="78bf0-644">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="78bf0-645">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-645">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="78bf0-646">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="78bf0-646">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="78bf0-647">`KestrelServerOptions` configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-647">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="78bf0-648">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="78bf0-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="78bf0-649">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-649">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="78bf0-650">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-650">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="78bf0-651">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-651">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="78bf0-652">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="78bf0-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="78bf0-653">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-653">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="78bf0-654">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-654">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="78bf0-655">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-655">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="78bf0-656">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="78bf0-656">Configure(IConfiguration)</span></span>

<span data-ttu-id="78bf0-657">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-657">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="78bf0-658">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-658">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="78bf0-659">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="78bf0-659">ListenOptions.UseHttps</span></span>

<span data-ttu-id="78bf0-660">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-660">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="78bf0-661">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-661">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="78bf0-662">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-662">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="78bf0-663">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-663">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="78bf0-664">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-664">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="78bf0-665">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="78bf0-665">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="78bf0-666">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="78bf0-666">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="78bf0-667">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-667">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="78bf0-668">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-668">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="78bf0-669">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-669">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="78bf0-670">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-670">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="78bf0-671">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-671">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="78bf0-672">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-672">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="78bf0-673">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="78bf0-673">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="78bf0-674">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-674">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="78bf0-675">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="78bf0-675">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="78bf0-676">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="78bf0-676">Supported configurations described next:</span></span>

* <span data-ttu-id="78bf0-677">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-677">No configuration</span></span>
* <span data-ttu-id="78bf0-678">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-678">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="78bf0-679">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="78bf0-679">Change the defaults in code</span></span>

<span data-ttu-id="78bf0-680">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="78bf0-680">*No configuration*</span></span>

<span data-ttu-id="78bf0-681">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="78bf0-681">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="78bf0-682">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="78bf0-682">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="78bf0-683">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-683">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="78bf0-684">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-684">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="78bf0-685">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-685">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="78bf0-686">No exemplo a seguir *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="78bf0-686">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="78bf0-687">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="78bf0-687">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="78bf0-688">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="78bf0-688">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="78bf0-689">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-689">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="78bf0-690">Por exemplo, o   >  certificado **padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="78bf0-690">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="78bf0-691">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="78bf0-691">Schema notes:</span></span>

* <span data-ttu-id="78bf0-692">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-692">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="78bf0-693">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-693">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="78bf0-694">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-694">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="78bf0-695">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="78bf0-695">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="78bf0-696">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="78bf0-696">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="78bf0-697">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-697">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="78bf0-698">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="78bf0-698">The `Certificate` section is optional.</span></span> <span data-ttu-id="78bf0-699">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-699">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="78bf0-700">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-700">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="78bf0-701">A `Certificate` seção dá suporte a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="78bf0-701">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="78bf0-702">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-702">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="78bf0-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="78bf0-704">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="78bf0-704">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="78bf0-705">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-705">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="78bf0-706">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-706">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="78bf0-707">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="78bf0-707">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="78bf0-708">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="78bf0-708">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="78bf0-709">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="78bf0-709">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="78bf0-710">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-710">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="78bf0-711">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="78bf0-711">*Change the defaults in code*</span></span>

<span data-ttu-id="78bf0-712">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="78bf0-712">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="78bf0-713">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-713">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="78bf0-714">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="78bf0-714">*Kestrel support for SNI*</span></span>

<span data-ttu-id="78bf0-715">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-715">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="78bf0-716">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="78bf0-716">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="78bf0-717">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-717">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="78bf0-718">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-718">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="78bf0-719">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-719">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="78bf0-720">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="78bf0-720">SNI support requires:</span></span>

* <span data-ttu-id="78bf0-721">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="78bf0-721">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="78bf0-722">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="78bf0-722">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="78bf0-723">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-723">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="78bf0-724">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-724">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="78bf0-725">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="78bf0-725">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="78bf0-726">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="78bf0-726">Connection logging</span></span>

<span data-ttu-id="78bf0-727">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-727">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="78bf0-728">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="78bf0-728">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="78bf0-729">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-729">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="78bf0-730">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-730">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="78bf0-731">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="78bf0-731">Bind to a TCP socket</span></span>

<span data-ttu-id="78bf0-732">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="78bf0-732">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="78bf0-733">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-733">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="78bf0-734">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-734">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="78bf0-735">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="78bf0-735">Bind to a Unix socket</span></span>

<span data-ttu-id="78bf0-736">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="78bf0-736">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="78bf0-737">No arquivo Nginx confiuguration, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="78bf0-737">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="78bf0-738">`{KESTREL SOCKET}` é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="78bf0-738">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="78bf0-739">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="78bf0-739">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="78bf0-740">Porta 0</span><span class="sxs-lookup"><span data-stu-id="78bf0-740">Port 0</span></span>

<span data-ttu-id="78bf0-741">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="78bf0-741">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="78bf0-742">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="78bf0-742">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="78bf0-743">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-743">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="78bf0-744">Limitações</span><span class="sxs-lookup"><span data-stu-id="78bf0-744">Limitations</span></span>

<span data-ttu-id="78bf0-745">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="78bf0-745">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="78bf0-746">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="78bf0-746">`--urls` command-line argument</span></span>
* <span data-ttu-id="78bf0-747">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="78bf0-747">`urls` host configuration key</span></span>
* <span data-ttu-id="78bf0-748">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="78bf0-748">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="78bf0-749">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-749">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="78bf0-750">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="78bf0-750">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="78bf0-751">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="78bf0-751">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="78bf0-752">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-752">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="78bf0-753">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="78bf0-753">IIS endpoint configuration</span></span>

<span data-ttu-id="78bf0-754">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-754">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="78bf0-755">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="78bf0-755">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="78bf0-756">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="78bf0-756">ListenOptions.Protocols</span></span>

<span data-ttu-id="78bf0-757">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="78bf0-757">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="78bf0-758">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-758">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="78bf0-759">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="78bf0-759">`HttpProtocols` enum value</span></span> | <span data-ttu-id="78bf0-760">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="78bf0-760">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="78bf0-761">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-761">HTTP/1.1 only.</span></span> <span data-ttu-id="78bf0-762">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-762">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="78bf0-763">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-763">HTTP/2 only.</span></span> <span data-ttu-id="78bf0-764">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="78bf0-764">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="78bf0-765">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-765">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="78bf0-766">O HTTP/2 requer uma conexão TLS e [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) ; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78bf0-766">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="78bf0-767">O protocolo padrão é HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78bf0-767">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="78bf0-768">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="78bf0-768">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="78bf0-769">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="78bf0-769">TLS version 1.2 or later</span></span>
* <span data-ttu-id="78bf0-770">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="78bf0-770">Renegotiation disabled</span></span>
* <span data-ttu-id="78bf0-771">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="78bf0-771">Compression disabled</span></span>
* <span data-ttu-id="78bf0-772">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="78bf0-772">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="78bf0-773">Diffie-Hellman de curva elíptica (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="78bf0-773">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="78bf0-774">Diffie-Hellman de campo finito (DHE) &lbrack; `TLS12` &rbrack; : mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="78bf0-774">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="78bf0-775">Conjunto de codificação não bloqueado</span><span class="sxs-lookup"><span data-stu-id="78bf0-775">Cipher suite not blocked</span></span>

<span data-ttu-id="78bf0-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 tem &lbrack; `FIPS186` &rbrack; suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="78bf0-777">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="78bf0-777">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="78bf0-778">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="78bf0-778">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="78bf0-779">Crie opcionalmente uma implementação do `IConnectionAdapter` para filtrar os handshakes TLS por conexão para codificações específicas:</span><span class="sxs-lookup"><span data-stu-id="78bf0-779">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="78bf0-780">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="78bf0-780">*Set the protocol from configuration*</span></span>

<span data-ttu-id="78bf0-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="78bf0-782">No exemplo a seguir *appsettings.json* , um protocolo de conexão padrão (http/1.1 e http/2) é estabelecido para todos os pontos de extremidade do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="78bf0-782">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="78bf0-783">O arquivo de configuração de exemplo a seguir estabelece um protocolo de conexão para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="78bf0-783">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="78bf0-784">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-784">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="78bf0-785">Configuração de transporte Libuv</span><span class="sxs-lookup"><span data-stu-id="78bf0-785">Libuv transport configuration</span></span>

<span data-ttu-id="78bf0-786">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-786">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="78bf0-787">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="78bf0-787">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="78bf0-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="78bf0-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="78bf0-789">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="78bf0-789">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="78bf0-790">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="78bf0-790">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="78bf0-791">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="78bf0-791">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="78bf0-792">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-792">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="78bf0-793">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="78bf0-793">URL prefixes</span></span>

<span data-ttu-id="78bf0-794">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="78bf0-794">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="78bf0-795">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-795">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="78bf0-796">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-796">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="78bf0-797">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-797">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="78bf0-798">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="78bf0-798">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="78bf0-799">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-799">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="78bf0-800">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-800">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="78bf0-801">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-801">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="78bf0-802">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="78bf0-802">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="78bf0-803">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="78bf0-803">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="78bf0-804">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="78bf0-804">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="78bf0-805">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="78bf0-805">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="78bf0-806">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-806">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="78bf0-807">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="78bf0-807">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="78bf0-808">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-808">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="78bf0-809">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="78bf0-809">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="78bf0-810">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="78bf0-810">Host filtering</span></span>

<span data-ttu-id="78bf0-811">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="78bf0-811">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="78bf0-812">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="78bf0-812">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="78bf0-813">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-813">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="78bf0-814">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-814">`Host` headers aren't validated.</span></span>

<span data-ttu-id="78bf0-815">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="78bf0-815">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="78bf0-816">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="78bf0-816">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="78bf0-817">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-817">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="78bf0-818">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-818">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="78bf0-819">Para habilitar o middleware, defina uma `AllowedHosts` chave em *appsettings.json* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="78bf0-819">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="78bf0-820">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="78bf0-820">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="78bf0-821">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="78bf0-821">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="78bf0-822">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-822">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="78bf0-823">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="78bf0-823">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="78bf0-824">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="78bf0-824">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="78bf0-825">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-825">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="78bf0-826">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-826">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="78bf0-827">Descarga de solicitação HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="78bf0-827">HTTP/1.1 request draining</span></span>

<span data-ttu-id="78bf0-828">A abertura de conexões HTTP é demorada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-828">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="78bf0-829">Para HTTPS, ele também consome muitos recursos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-829">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="78bf0-830">Portanto, o Kestrel tenta reutilizar conexões de acordo com o protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78bf0-830">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="78bf0-831">Um corpo de solicitação deve ser totalmente consumido para permitir que a conexão seja reutilizada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-831">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="78bf0-832">O aplicativo nem sempre consome o corpo da solicitação, como `POST` solicitações em que o servidor retorna uma resposta de redirecionamento ou 404.</span><span class="sxs-lookup"><span data-stu-id="78bf0-832">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="78bf0-833">No `POST` – caso de redirecionamento:</span><span class="sxs-lookup"><span data-stu-id="78bf0-833">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="78bf0-834">O cliente pode já ter enviado parte dos `POST` dados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-834">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="78bf0-835">O servidor grava a resposta 301.</span><span class="sxs-lookup"><span data-stu-id="78bf0-835">The server writes the 301 response.</span></span>
* <span data-ttu-id="78bf0-836">A conexão não pode ser usada para uma nova solicitação até que os `POST` dados do corpo da solicitação anterior tenham sido totalmente lidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-836">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="78bf0-837">Kestrel tenta drenar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-837">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="78bf0-838">Drenar o corpo da solicitação significa ler e descartar os dados sem processá-los.</span><span class="sxs-lookup"><span data-stu-id="78bf0-838">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="78bf0-839">O processo de descarga faz um tradoff entre permitir que a conexão seja reutilizada e o tempo necessário para drenar os dados restantes:</span><span class="sxs-lookup"><span data-stu-id="78bf0-839">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="78bf0-840">O descarregamento tem um tempo limite de cinco segundos, o que não é configurável.</span><span class="sxs-lookup"><span data-stu-id="78bf0-840">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="78bf0-841">Se todos os dados especificados pelo `Content-Length` `Transfer-Encoding` cabeçalho ou não tiverem sido lidos antes do tempo limite, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-841">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="78bf0-842">Às vezes, você pode querer encerrar a solicitação imediatamente, antes ou depois de gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-842">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="78bf0-843">Por exemplo, os clientes podem ter limites de dados restritivos, portanto, limitar os dados carregados pode ser uma prioridade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-843">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="78bf0-844">Nesses casos, para encerrar uma solicitação, chame [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) de um controlador, Razor página ou middleware.</span><span class="sxs-lookup"><span data-stu-id="78bf0-844">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="78bf0-845">Há limitações para chamar `Abort` :</span><span class="sxs-lookup"><span data-stu-id="78bf0-845">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="78bf0-846">A criação de novas conexões pode ser lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="78bf0-846">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="78bf0-847">Não há nenhuma garantia de que o cliente leu a resposta antes que a conexão seja fechada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-847">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="78bf0-848">`Abort`A chamada deve ser rara e reservada para casos de erro graves, não erros comuns.</span><span class="sxs-lookup"><span data-stu-id="78bf0-848">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="78bf0-849">Só chame `Abort` quando um problema específico precisar ser resolvido.</span><span class="sxs-lookup"><span data-stu-id="78bf0-849">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="78bf0-850">Por exemplo, chame `Abort` se clientes mal-intencionados estão tentando `POST` dados ou quando há um bug no código do cliente que causa grandes ou inúmeras solicitações.</span><span class="sxs-lookup"><span data-stu-id="78bf0-850">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="78bf0-851">Não chame `Abort` situações de erro comuns, como HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="78bf0-851">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="78bf0-852">Chamar [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes `Abort` de chamar garante que o servidor concluiu a gravação da resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-852">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="78bf0-853">No entanto, o comportamento do cliente não é previsível e pode não ler a resposta antes que a conexão seja anulada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-853">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="78bf0-854">Esse processo é diferente para HTTP/2 porque o protocolo dá suporte à anulação de fluxos de solicitação individuais sem fechar a conexão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-854">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="78bf0-855">O tempo limite de esgotamento de cinco segundos não se aplica.</span><span class="sxs-lookup"><span data-stu-id="78bf0-855">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="78bf0-856">Se houver algum dado de corpo de solicitação não lido depois de concluir uma resposta, o servidor enviará um quadro RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-856">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="78bf0-857">Quadros de dados de corpo de solicitação adicionais são ignorados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-857">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="78bf0-858">Se possível, é melhor para os clientes utilizarem o cabeçalho de solicitação [esperado: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e aguarde até que o servidor responda antes de começar a enviar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-858">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="78bf0-859">Isso dá ao cliente uma oportunidade de examinar a resposta e anular antes de enviar dados desnecessários.</span><span class="sxs-lookup"><span data-stu-id="78bf0-859">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78bf0-860">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="78bf0-860">Additional resources</span></span>

* <span data-ttu-id="78bf0-861">Ao usar soquetes UNIX no Linux, o soquete não é excluído automaticamente no aplicativo desligado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-861">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="78bf0-862">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="78bf0-862">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="78bf0-863">RFC 7230: Sintaxe e roteamento da mensagem (Seção 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="78bf0-863">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="78bf0-864">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-864">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="78bf0-865">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bf0-865">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="78bf0-866">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="78bf0-866">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="78bf0-867">HTTPS</span><span class="sxs-lookup"><span data-stu-id="78bf0-867">HTTPS</span></span>
* <span data-ttu-id="78bf0-868">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="78bf0-868">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="78bf0-869">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="78bf0-869">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="78bf0-870">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bf0-870">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="78bf0-871">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78bf0-871">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="78bf0-872">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="78bf0-872">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="78bf0-873">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="78bf0-873">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="78bf0-874">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-874">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="78bf0-875">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="78bf0-875">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="78bf0-877">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="78bf0-877">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="78bf0-879">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="78bf0-879">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="78bf0-880">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-880">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="78bf0-881">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="78bf0-881">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="78bf0-882">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-882">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="78bf0-883">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-883">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="78bf0-884">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="78bf0-884">A reverse proxy:</span></span>

* <span data-ttu-id="78bf0-885">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="78bf0-885">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="78bf0-886">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-886">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="78bf0-887">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-887">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="78bf0-888">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="78bf0-888">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="78bf0-889">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="78bf0-889">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="78bf0-890">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="78bf0-890">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="78bf0-891">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78bf0-891">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="78bf0-892">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="78bf0-892">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="78bf0-893">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-893">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="78bf0-894">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="78bf0-894">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="78bf0-895">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-895">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="78bf0-896">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="78bf0-896">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="78bf0-897">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="78bf0-897">Kestrel options</span></span>

<span data-ttu-id="78bf0-898">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="78bf0-898">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="78bf0-899">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-899">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="78bf0-900">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-900">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="78bf0-901">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-901">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="78bf0-902">As opções de Kestrel, que são configuradas no código C# nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-902">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="78bf0-903">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de um *appsettings.json* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="78bf0-903">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="78bf0-904">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="78bf0-904">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="78bf0-905">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="78bf0-905">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="78bf0-906">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="78bf0-906">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="78bf0-907">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-907">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="78bf0-908">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="78bf0-908">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="78bf0-909">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="78bf0-909">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="78bf0-910">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="78bf0-910">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="78bf0-911">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="78bf0-911">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="78bf0-912">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="78bf0-912">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="78bf0-913">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="78bf0-913">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="78bf0-914">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-914">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="78bf0-915">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="78bf0-915">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="78bf0-916">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="78bf0-916">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="78bf0-917">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="78bf0-917">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="78bf0-918">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-918">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="78bf0-919">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-919">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="78bf0-920">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-920">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="78bf0-921">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="78bf0-921">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="78bf0-922">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="78bf0-922">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="78bf0-923">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="78bf0-923">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="78bf0-924">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="78bf0-924">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="78bf0-925">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-925">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="78bf0-926">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="78bf0-926">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="78bf0-927">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="78bf0-927">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="78bf0-928">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-928">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="78bf0-929">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="78bf0-929">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="78bf0-930">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="78bf0-930">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="78bf0-931">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="78bf0-931">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="78bf0-932">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-932">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="78bf0-933">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-933">A minimum rate also applies to the response.</span></span> <span data-ttu-id="78bf0-934">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="78bf0-934">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="78bf0-935">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="78bf0-935">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="78bf0-936">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="78bf0-936">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="78bf0-937">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-937">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="78bf0-938">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-938">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="78bf0-939">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="78bf0-939">Synchronous I/O</span></span>

<span data-ttu-id="78bf0-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="78bf0-941">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-941">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="78bf0-942">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-942">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="78bf0-943">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="78bf0-943">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="78bf0-944">O exemplo a seguir desabilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="78bf0-944">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="78bf0-945">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="78bf0-945">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="78bf0-946">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="78bf0-946">Endpoint configuration</span></span>

<span data-ttu-id="78bf0-947">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="78bf0-947">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="78bf0-948">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="78bf0-948">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="78bf0-949">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="78bf0-949">Specify URLs using the:</span></span>

* <span data-ttu-id="78bf0-950">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-950">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="78bf0-951">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-951">`--urls` command-line argument.</span></span>
* <span data-ttu-id="78bf0-952">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-952">`urls` host configuration key.</span></span>
* <span data-ttu-id="78bf0-953">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-953">`UseUrls` extension method.</span></span>

<span data-ttu-id="78bf0-954">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="78bf0-954">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="78bf0-955">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="78bf0-955">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="78bf0-956">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="78bf0-956">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="78bf0-957">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-957">A development certificate is created:</span></span>

* <span data-ttu-id="78bf0-958">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-958">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="78bf0-959">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-959">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="78bf0-960">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="78bf0-960">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="78bf0-961">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="78bf0-961">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="78bf0-962">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-962">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="78bf0-963">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="78bf0-963">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="78bf0-964">`KestrelServerOptions` configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-964">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="78bf0-965">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="78bf0-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="78bf0-966">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-966">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="78bf0-967">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-967">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="78bf0-968">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-968">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="78bf0-969">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="78bf0-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="78bf0-970">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-970">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="78bf0-971">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-971">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="78bf0-972">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-972">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="78bf0-973">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="78bf0-973">Configure(IConfiguration)</span></span>

<span data-ttu-id="78bf0-974">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-974">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="78bf0-975">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-975">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="78bf0-976">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="78bf0-976">ListenOptions.UseHttps</span></span>

<span data-ttu-id="78bf0-977">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-977">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="78bf0-978">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-978">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="78bf0-979">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-979">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="78bf0-980">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-980">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="78bf0-981">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="78bf0-981">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="78bf0-982">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="78bf0-982">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="78bf0-983">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="78bf0-983">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="78bf0-984">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-984">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="78bf0-985">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-985">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="78bf0-986">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-986">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="78bf0-987">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-987">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="78bf0-988">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-988">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="78bf0-989">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-989">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="78bf0-990">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="78bf0-990">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="78bf0-991">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-991">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="78bf0-992">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="78bf0-992">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="78bf0-993">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="78bf0-993">Supported configurations described next:</span></span>

* <span data-ttu-id="78bf0-994">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-994">No configuration</span></span>
* <span data-ttu-id="78bf0-995">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="78bf0-995">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="78bf0-996">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="78bf0-996">Change the defaults in code</span></span>

<span data-ttu-id="78bf0-997">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="78bf0-997">*No configuration*</span></span>

<span data-ttu-id="78bf0-998">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="78bf0-998">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="78bf0-999">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="78bf0-999">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="78bf0-1000">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1000">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="78bf0-1001">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1001">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="78bf0-1002">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1002">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="78bf0-1003">No exemplo a seguir *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="78bf0-1003">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="78bf0-1004">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1004">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="78bf0-1005">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1005">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="78bf0-1006">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1006">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="78bf0-1007">Por exemplo, o   >  certificado **padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1007">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="78bf0-1008">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1008">Schema notes:</span></span>

* <span data-ttu-id="78bf0-1009">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1009">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="78bf0-1010">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1010">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="78bf0-1011">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1011">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="78bf0-1012">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1012">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="78bf0-1013">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1013">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="78bf0-1014">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1014">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="78bf0-1015">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1015">The `Certificate` section is optional.</span></span> <span data-ttu-id="78bf0-1016">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1016">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="78bf0-1017">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1017">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="78bf0-1018">A `Certificate` seção dá suporte a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="78bf0-1018">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="78bf0-1019">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1019">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="78bf0-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="78bf0-1021">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="78bf0-1021">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="78bf0-1022">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1022">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="78bf0-1023">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1023">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="78bf0-1024">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1024">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="78bf0-1025">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1025">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="78bf0-1026">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1026">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="78bf0-1027">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1027">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="78bf0-1028">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="78bf0-1028">*Change the defaults in code*</span></span>

<span data-ttu-id="78bf0-1029">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1029">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="78bf0-1030">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1030">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="78bf0-1031">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="78bf0-1031">*Kestrel support for SNI*</span></span>

<span data-ttu-id="78bf0-1032">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1032">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="78bf0-1033">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1033">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="78bf0-1034">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1034">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="78bf0-1035">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1035">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="78bf0-1036">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1036">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="78bf0-1037">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1037">SNI support requires:</span></span>

* <span data-ttu-id="78bf0-1038">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1038">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="78bf0-1039">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="78bf0-1039">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="78bf0-1040">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1040">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="78bf0-1041">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1041">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="78bf0-1042">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1042">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="78bf0-1043">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="78bf0-1043">Connection logging</span></span>

<span data-ttu-id="78bf0-1044">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1044">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="78bf0-1045">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1045">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="78bf0-1046">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1046">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="78bf0-1047">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1047">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="78bf0-1048">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="78bf0-1048">Bind to a TCP socket</span></span>

<span data-ttu-id="78bf0-1049">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1049">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="78bf0-1050">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1050">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="78bf0-1051">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1051">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="78bf0-1052">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="78bf0-1052">Bind to a Unix socket</span></span>

<span data-ttu-id="78bf0-1053">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1053">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="78bf0-1054">No arquivo Nginx confiuguration, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="78bf0-1054">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="78bf0-1055">`{KESTREL SOCKET}` é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1055">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="78bf0-1056">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1056">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="78bf0-1057">Porta 0</span><span class="sxs-lookup"><span data-stu-id="78bf0-1057">Port 0</span></span>

<span data-ttu-id="78bf0-1058">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1058">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="78bf0-1059">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1059">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="78bf0-1060">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1060">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="78bf0-1061">Limitações</span><span class="sxs-lookup"><span data-stu-id="78bf0-1061">Limitations</span></span>

<span data-ttu-id="78bf0-1062">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1062">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="78bf0-1063">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="78bf0-1063">`--urls` command-line argument</span></span>
* <span data-ttu-id="78bf0-1064">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="78bf0-1064">`urls` host configuration key</span></span>
* <span data-ttu-id="78bf0-1065">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="78bf0-1065">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="78bf0-1066">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1066">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="78bf0-1067">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1067">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="78bf0-1068">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1068">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="78bf0-1069">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1069">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="78bf0-1070">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="78bf0-1070">IIS endpoint configuration</span></span>

<span data-ttu-id="78bf0-1071">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1071">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="78bf0-1072">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1072">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="78bf0-1073">Configuração de transporte Libuv</span><span class="sxs-lookup"><span data-stu-id="78bf0-1073">Libuv transport configuration</span></span>

<span data-ttu-id="78bf0-1074">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1074">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="78bf0-1075">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1075">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="78bf0-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="78bf0-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="78bf0-1077">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="78bf0-1077">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="78bf0-1078">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1078">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="78bf0-1079">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1079">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="78bf0-1080">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1080">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="78bf0-1081">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="78bf0-1081">URL prefixes</span></span>

<span data-ttu-id="78bf0-1082">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1082">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="78bf0-1083">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1083">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="78bf0-1084">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1084">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="78bf0-1085">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-1085">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="78bf0-1086">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1086">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="78bf0-1087">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-1087">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="78bf0-1088">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1088">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="78bf0-1089">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-1089">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="78bf0-1090">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1090">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="78bf0-1091">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1091">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="78bf0-1092">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1092">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="78bf0-1093">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1093">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="78bf0-1094">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="78bf0-1094">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="78bf0-1095">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1095">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="78bf0-1096">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1096">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="78bf0-1097">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1097">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="78bf0-1098">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="78bf0-1098">Host filtering</span></span>

<span data-ttu-id="78bf0-1099">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1099">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="78bf0-1100">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1100">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="78bf0-1101">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1101">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="78bf0-1102">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1102">`Host` headers aren't validated.</span></span>

<span data-ttu-id="78bf0-1103">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1103">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="78bf0-1104">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1104">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="78bf0-1105">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1105">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="78bf0-1106">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1106">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="78bf0-1107">Para habilitar o middleware, defina uma `AllowedHosts` chave em *appsettings.json* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1107">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="78bf0-1108">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1108">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="78bf0-1109">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1109">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="78bf0-1110">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1110">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="78bf0-1111">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1111">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="78bf0-1112">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1112">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="78bf0-1113">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1113">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="78bf0-1114">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1114">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="78bf0-1115">Descarga de solicitação HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="78bf0-1115">HTTP/1.1 request draining</span></span>

<span data-ttu-id="78bf0-1116">A abertura de conexões HTTP é demorada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1116">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="78bf0-1117">Para HTTPS, ele também consome muitos recursos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1117">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="78bf0-1118">Portanto, o Kestrel tenta reutilizar conexões de acordo com o protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1118">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="78bf0-1119">Um corpo de solicitação deve ser totalmente consumido para permitir que a conexão seja reutilizada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1119">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="78bf0-1120">O aplicativo nem sempre consome o corpo da solicitação, como `POST` solicitações em que o servidor retorna uma resposta de redirecionamento ou 404.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1120">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="78bf0-1121">No `POST` – caso de redirecionamento:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1121">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="78bf0-1122">O cliente pode já ter enviado parte dos `POST` dados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1122">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="78bf0-1123">O servidor grava a resposta 301.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1123">The server writes the 301 response.</span></span>
* <span data-ttu-id="78bf0-1124">A conexão não pode ser usada para uma nova solicitação até que os `POST` dados do corpo da solicitação anterior tenham sido totalmente lidos.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1124">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="78bf0-1125">Kestrel tenta drenar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1125">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="78bf0-1126">Drenar o corpo da solicitação significa ler e descartar os dados sem processá-los.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1126">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="78bf0-1127">O processo de descarga faz um tradoff entre permitir que a conexão seja reutilizada e o tempo necessário para drenar os dados restantes:</span><span class="sxs-lookup"><span data-stu-id="78bf0-1127">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="78bf0-1128">O descarregamento tem um tempo limite de cinco segundos, o que não é configurável.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1128">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="78bf0-1129">Se todos os dados especificados pelo `Content-Length` `Transfer-Encoding` cabeçalho ou não tiverem sido lidos antes do tempo limite, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1129">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="78bf0-1130">Às vezes, você pode querer encerrar a solicitação imediatamente, antes ou depois de gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1130">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="78bf0-1131">Por exemplo, os clientes podem ter limites de dados restritivos, portanto, limitar os dados carregados pode ser uma prioridade.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1131">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="78bf0-1132">Nesses casos, para encerrar uma solicitação, chame [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) de um controlador, Razor página ou middleware.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1132">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="78bf0-1133">Há limitações para chamar `Abort` :</span><span class="sxs-lookup"><span data-stu-id="78bf0-1133">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="78bf0-1134">A criação de novas conexões pode ser lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1134">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="78bf0-1135">Não há nenhuma garantia de que o cliente leu a resposta antes que a conexão seja fechada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1135">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="78bf0-1136">`Abort`A chamada deve ser rara e reservada para casos de erro graves, não erros comuns.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1136">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="78bf0-1137">Só chame `Abort` quando um problema específico precisar ser resolvido.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1137">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="78bf0-1138">Por exemplo, chame `Abort` se clientes mal-intencionados estão tentando `POST` dados ou quando há um bug no código do cliente que causa grandes ou inúmeras solicitações.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1138">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="78bf0-1139">Não chame `Abort` situações de erro comuns, como HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1139">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="78bf0-1140">Chamar [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes `Abort` de chamar garante que o servidor concluiu a gravação da resposta.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1140">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="78bf0-1141">No entanto, o comportamento do cliente não é previsível e pode não ler a resposta antes que a conexão seja anulada.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1141">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="78bf0-1142">Esse processo é diferente para HTTP/2 porque o protocolo dá suporte à anulação de fluxos de solicitação individuais sem fechar a conexão.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1142">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="78bf0-1143">O tempo limite de esgotamento de cinco segundos não se aplica.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1143">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="78bf0-1144">Se houver algum dado de corpo de solicitação não lido depois de concluir uma resposta, o servidor enviará um quadro RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1144">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="78bf0-1145">Quadros de dados de corpo de solicitação adicionais são ignorados.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1145">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="78bf0-1146">Se possível, é melhor para os clientes utilizarem o cabeçalho de solicitação [esperado: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e aguarde até que o servidor responda antes de começar a enviar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1146">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="78bf0-1147">Isso dá ao cliente uma oportunidade de examinar a resposta e anular antes de enviar dados desnecessários.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1147">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78bf0-1148">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="78bf0-1148">Additional resources</span></span>

* <span data-ttu-id="78bf0-1149">Ao usar soquetes UNIX no Linux, o soquete não é excluído automaticamente no aplicativo desligado.</span><span class="sxs-lookup"><span data-stu-id="78bf0-1149">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="78bf0-1150">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="78bf0-1150">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="78bf0-1151">RFC 7230: Sintaxe e roteamento da mensagem (Seção 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="78bf0-1151">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end