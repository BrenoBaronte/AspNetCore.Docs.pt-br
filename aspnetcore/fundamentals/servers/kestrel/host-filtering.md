---
title: Filtragem de host com o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba como usar a filtragem de host com o Kestrel, o servidor Web de plataforma cruzada para ASP.NET Core.
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
uid: fundamentals/servers/kestrel/host-filtering
ms.openlocfilehash: d55c211f05a77f6acabedef2ff62a621d9a1844e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253918"
---
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="75ea5-103">Filtragem de host com o servidor Web ASP.NET Core Kestrel</span><span class="sxs-lookup"><span data-stu-id="75ea5-103">Host filtering with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="75ea5-104">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="75ea5-104">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="75ea5-105">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="75ea5-105">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="75ea5-106">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="75ea5-106">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="75ea5-107">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="75ea5-107">`Host` headers aren't validated.</span></span>

<span data-ttu-id="75ea5-108">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="75ea5-108">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="75ea5-109">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que é fornecido implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75ea5-109">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="75ea5-110">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>:</span><span class="sxs-lookup"><span data-stu-id="75ea5-110">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>:</span></span>

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="75ea5-111">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="75ea5-111">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="75ea5-112">Para habilitar o middleware, defina uma `AllowedHosts` chave em *appsettings.json* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="75ea5-112">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="75ea5-113">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="75ea5-113">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="75ea5-114">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="75ea5-114">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="75ea5-115">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="75ea5-115">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="75ea5-116">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="75ea5-116">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="75ea5-117">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="75ea5-117">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="75ea5-118">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="75ea5-118">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="75ea5-119">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="75ea5-119">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>
