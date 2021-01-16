---
title: Quando usar um proxy reverso com o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba mais sobre quando usar um proxy reverso na frente do Kestrel, o servidor Web de plataforma cruzada para ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253909"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="cd8b7-103">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="cd8b7-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="cd8b7-104">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="cd8b7-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="cd8b7-105">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="cd8b7-106">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="cd8b7-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](_static/kestrel-to-internet2.png)

<span data-ttu-id="cd8b7-108">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="cd8b7-108">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](_static/kestrel-to-internet.png)

<span data-ttu-id="cd8b7-110">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="cd8b7-111">Quando Kestrel é usado como um servidor de borda sem um servidor proxy reverso, não há suporte para o compartilhamento do mesmo endereço IP e porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="cd8b7-112">Quando Kestrel é configurado para escutar em uma porta, o Kestrel manipula todo o tráfego para essa porta independentemente dos `Host` cabeçalhos de solicitações.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="cd8b7-113">Um proxy reverso que pode compartilhar portas pode encaminhar solicitações para Kestrel em um IP e uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="cd8b7-114">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="cd8b7-115">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="cd8b7-115">A reverse proxy:</span></span>

* <span data-ttu-id="cd8b7-116">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="cd8b7-117">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="cd8b7-118">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="cd8b7-119">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="cd8b7-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="cd8b7-120">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="cd8b7-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="cd8b7-121">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="cd8b7-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd8b7-122">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cd8b7-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

