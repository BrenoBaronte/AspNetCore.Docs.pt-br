---
title: Usar HTTP/2 com o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba como usar o HTTP/2 com o Kestrel, o servidor Web multiplataforma para ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253915"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="39a93-103">Usar HTTP/2 com o servidor Web ASP.NET Core Kestrel</span><span class="sxs-lookup"><span data-stu-id="39a93-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="39a93-104">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="39a93-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="39a93-105">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="39a93-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="39a93-106">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="39a93-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="39a93-107">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="39a93-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="39a93-108">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="39a93-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="39a93-109">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="39a93-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="39a93-110">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="39a93-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="39a93-111">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="39a93-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="39a93-112">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="39a93-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="39a93-113">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="39a93-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="39a93-114">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="39a93-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="39a93-115">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="39a93-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="39a93-116">A partir do .NET Core 3,0, o HTTP/2 é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="39a93-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="39a93-117">Para obter mais informações sobre a configuração, consulte as seções [limites de Kestrel http/2](xref:fundamentals/servers/kestrel/options#http2-limits) e [ListenerOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="39a93-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="39a93-118">Recursos avançados de HTTP/2</span><span class="sxs-lookup"><span data-stu-id="39a93-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="39a93-119">Recursos adicionais de HTTP/2 no Kestrel dão suporte a gRPC, incluindo suporte para trailers de resposta e envio de quadros de redefinição.</span><span class="sxs-lookup"><span data-stu-id="39a93-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="39a93-120">Trailers</span><span class="sxs-lookup"><span data-stu-id="39a93-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="39a93-121">Redefinir</span><span class="sxs-lookup"><span data-stu-id="39a93-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
