---
title: Configure o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga
author: rick-anderson
description: Saiba mais sobre a configuração para aplicativos hospedados por trás de servidores proxy e balanceadores de carga, o que muitas vezes oculta informações de solicitação importantes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: b5c81e0cfa29cddeb1aeed1119a711fca4d91ae4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659379"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="40a07-103">Configure o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga</span><span class="sxs-lookup"><span data-stu-id="40a07-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="40a07-104">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="40a07-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40a07-105">Na configuração recomendada para o ASP.NET Core, o aplicativo é hospedado usando IIS/Módulo do ASP.NET Core, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="40a07-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="40a07-106">Servidores proxy, balanceadores de carga e outros dispositivos de rede geralmente ocultam informações sobre a solicitação antes de ela alcançar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="40a07-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="40a07-107">Quando solicitações HTTPS são passadas por proxy por HTTP, o esquema original (HTTPS) é perdido e deve ser encaminhado em um cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="40a07-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="40a07-108">Devido a um aplicativo receber uma solicitação do proxy e não de sua origem verdadeira na Internet ou rede corporativa, o endereço IP do cliente originador também deve ser encaminhado em um cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="40a07-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="40a07-109">Essas informações podem ser importantes no processamento de solicitações, por exemplo, em redirecionamentos, autenticação, geração de link, avaliação de política e localização geográfica do cliente.</span><span class="sxs-lookup"><span data-stu-id="40a07-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="40a07-110">Cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="40a07-110">Forwarded headers</span></span>

<span data-ttu-id="40a07-111">Por convenção, os proxies encaminham informações em cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="40a07-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="40a07-112">Cabeçalho</span><span class="sxs-lookup"><span data-stu-id="40a07-112">Header</span></span> | <span data-ttu-id="40a07-113">Descrição</span><span class="sxs-lookup"><span data-stu-id="40a07-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="40a07-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="40a07-114">X-Forwarded-For</span></span> | <span data-ttu-id="40a07-115">Contém informações sobre o cliente que iniciou a solicitação e os proxies subsequentes em uma cadeia de proxies.</span><span class="sxs-lookup"><span data-stu-id="40a07-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="40a07-116">Esse parâmetro pode conter endereços IP (e, opcionalmente, os números de porta).</span><span class="sxs-lookup"><span data-stu-id="40a07-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="40a07-117">Em uma cadeia de servidores proxy, o primeiro parâmetro indica o cliente em que a solicitação foi feita pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="40a07-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="40a07-118">Depois, vêm os identificadores de proxy subsequentes.</span><span class="sxs-lookup"><span data-stu-id="40a07-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="40a07-119">O último proxy na cadeia não está na lista de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="40a07-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="40a07-120">O endereço IP do último proxy (e opcionalmente um número da porta) estão disponíveis como o endereço IP remoto na camada de transporte.</span><span class="sxs-lookup"><span data-stu-id="40a07-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="40a07-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="40a07-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="40a07-122">O valor do esquema de origem (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="40a07-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="40a07-123">O valor também pode ser uma lista de esquemas se a solicitação percorreu vários proxies.</span><span class="sxs-lookup"><span data-stu-id="40a07-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="40a07-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="40a07-124">X-Forwarded-Host</span></span> | <span data-ttu-id="40a07-125">O valor original do campo de cabeçalho do host.</span><span class="sxs-lookup"><span data-stu-id="40a07-125">The original value of the Host header field.</span></span> <span data-ttu-id="40a07-126">Normalmente, os proxies não modificam o cabeçalho do host.</span><span class="sxs-lookup"><span data-stu-id="40a07-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="40a07-127">Veja [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para obter informações sobre uma vulnerabilidade de elevação de privilégios que afeta os sistemas em que o proxy não valida ou restringe cabeçalhos de Host a valores válidos conhecidos.</span><span class="sxs-lookup"><span data-stu-id="40a07-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="40a07-128">O middleware de cabeçalhos encaminhados, do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lê esses cabeçalhos e preenche os campos associados em <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="40a07-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="40a07-129">As atualizações de middleware:</span><span class="sxs-lookup"><span data-stu-id="40a07-129">The middleware updates:</span></span>

* <span data-ttu-id="40a07-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="40a07-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="40a07-131">Configurações adicionais influenciam o modo como o middleware define `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="40a07-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="40a07-132">Para obter detalhes, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="40a07-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="40a07-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="40a07-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="40a07-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="40a07-135">As [configurações padrão](#forwarded-headers-middleware-options) de middleware de cabeçalhos encaminhados podem ser definidas.</span><span class="sxs-lookup"><span data-stu-id="40a07-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="40a07-136">As configurações padrão são:</span><span class="sxs-lookup"><span data-stu-id="40a07-136">The default settings are:</span></span>

* <span data-ttu-id="40a07-137">Há apenas *um proxy* entre o aplicativo e a origem das solicitações.</span><span class="sxs-lookup"><span data-stu-id="40a07-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="40a07-138">Somente os endereços de loopback são configurados para proxies conhecidos e redes conhecidas.</span><span class="sxs-lookup"><span data-stu-id="40a07-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="40a07-139">Os cabeçalhos encaminhados são nomeados `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="40a07-140">Nem todos os dispositivos de rede adicionam os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="40a07-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="40a07-141">Consulte as diretrizes do fabricante do dispositivo se as solicitações de proxies não contiverem esses cabeçalhos quando atingirem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40a07-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="40a07-142">Se o dispositivo usar nomes de cabeçalho diferente de `X-Forwarded-For` e `X-Forwarded-Proto`, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo dispositivo.</span><span class="sxs-lookup"><span data-stu-id="40a07-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="40a07-143">Para obter mais informações, consulte [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) (Opções de middleware de cabeçalhos encaminhados) e [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names) (Configuração de um proxy que usa diferentes nomes de cabeçalho).</span><span class="sxs-lookup"><span data-stu-id="40a07-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="40a07-144">O IIS/IIS Express e o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40a07-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="40a07-145">O Middleware de Cabeçalhos Encaminhados é habilitado por padrão pelo [Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando o aplicativo é hospedado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) atrás do IIS e do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40a07-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="40a07-146">O middleware de cabeçalhos encaminhados é ativado para ser executado primeiro no pipeline de middleware, com uma configuração restrita específica para o Módulo do ASP.NET Core devido a questões de confiança com cabeçalhos encaminhados (por exemplo, [falsificação de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="40a07-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="40a07-147">O middleware está configurado para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` e é restrito a um proxy de localhost único.</span><span class="sxs-lookup"><span data-stu-id="40a07-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="40a07-148">Se configuração adicional for necessária, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="40a07-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="40a07-149">Outros cenários de servidor proxy e balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="40a07-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="40a07-150">Além de usar a [Integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando hospedar [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), o Middleware de cabeçalhos encaminhados não é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="40a07-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="40a07-151">O middleware de cabeçalhos encaminhados deve ser habilitado para um aplicativo para processar cabeçalhos encaminhados com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="40a07-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="40a07-152">Após a habilitação do middleware, se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, o [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) padrão será [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="40a07-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="40a07-153">Configure o middleware com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40a07-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="40a07-154">Invocar o método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> no `Startup.Configure` antes de chamar outro middleware:</span><span class="sxs-lookup"><span data-stu-id="40a07-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="40a07-155">Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado no `Startup.ConfigureServices` ou diretamente no método de extensão com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, os cabeçalhos padrão para encaminhar serão [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="40a07-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="40a07-156">A propriedade <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve ser configurada com os cabeçalhos para encaminhar.</span><span class="sxs-lookup"><span data-stu-id="40a07-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="40a07-157">Configuração de Nginx</span><span class="sxs-lookup"><span data-stu-id="40a07-157">Nginx configuration</span></span>

<span data-ttu-id="40a07-158">Para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="40a07-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="40a07-159">Para obter mais informações, veja [NGINX: usando o cabeçalho encaminhado](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="40a07-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="40a07-160">Configuração do Apache</span><span class="sxs-lookup"><span data-stu-id="40a07-160">Apache configuration</span></span>

<span data-ttu-id="40a07-161">`X-Forwarded-For` é adicionado automaticamente (veja [mod_proxy do módulo do Apache: cabeçalhos de solicitação de proxy reverso](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="40a07-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="40a07-162">Para obter informações sobre como encaminhar o cabeçalho `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="40a07-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="40a07-163">Opções de middleware de cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="40a07-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="40a07-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla o comportamento do middleware de cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="40a07-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="40a07-165">O seguinte exemplo altera os valores padrão:</span><span class="sxs-lookup"><span data-stu-id="40a07-165">The following example changes the default values:</span></span>

* <span data-ttu-id="40a07-166">Limite o número de entradas nos cabeçalhos encaminhados a `2`.</span><span class="sxs-lookup"><span data-stu-id="40a07-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="40a07-167">Adicione um endereço de proxy conhecido de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="40a07-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="40a07-168">Altere o nome do cabeçalho encaminhado do padrão `X-Forwarded-For` para `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="40a07-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="40a07-169">Opção</span><span class="sxs-lookup"><span data-stu-id="40a07-169">Option</span></span> | <span data-ttu-id="40a07-170">Descrição</span><span class="sxs-lookup"><span data-stu-id="40a07-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="40a07-171">Restringe os hosts com o cabeçalho `X-Forwarded-Host` para os valores fornecidos.</span><span class="sxs-lookup"><span data-stu-id="40a07-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="40a07-172">Os valores são comparados usando ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="40a07-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="40a07-173">Os número de porta devem ser excluídos.</span><span class="sxs-lookup"><span data-stu-id="40a07-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="40a07-174">Se a lista estiver vazia, todos os hosts serão permitidos.</span><span class="sxs-lookup"><span data-stu-id="40a07-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="40a07-175">Um curinga de nível superior `*` permite todos os hosts não vazios.</span><span class="sxs-lookup"><span data-stu-id="40a07-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="40a07-176">Curingas de subdomínio são permitidos, mas não correspondem ao domínio raiz.</span><span class="sxs-lookup"><span data-stu-id="40a07-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="40a07-177">Por exemplo, `*.contoso.com` corresponde o subdomínio `foo.contoso.com`, mas não ao domínio raiz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="40a07-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="40a07-178">Nomes do host Unicode são permitidos, mas são convertidos em [Punycode](https://tools.ietf.org/html/rfc3492) para correspondência.</span><span class="sxs-lookup"><span data-stu-id="40a07-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="40a07-179">[Endereços IPv6](https://tools.ietf.org/html/rfc4291) devem incluir colchetes delimitadores e estar no [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por exemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="40a07-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="40a07-180">Endereços IPv6 não têm caso especial para verificar se há igualdade lógica entre formatos diferentes, e nenhuma canonicalização é executada.</span><span class="sxs-lookup"><span data-stu-id="40a07-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="40a07-181">Falha ao restringir os hosts permitidos pode permitir que um atacante falsifique links gerados pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="40a07-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="40a07-182">O valor padrão é um `IList<string>` vazio.</span><span class="sxs-lookup"><span data-stu-id="40a07-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="40a07-183">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="40a07-184">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-For`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="40a07-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="40a07-185">O padrão é `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="40a07-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="40a07-186">Identifica quais encaminhadores devem ser processados.</span><span class="sxs-lookup"><span data-stu-id="40a07-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="40a07-187">Veja o [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para a lista de campos aplicáveis.</span><span class="sxs-lookup"><span data-stu-id="40a07-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="40a07-188">Os valores típicos atribuídos a essa propriedade são `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="40a07-189">O valor padrão é [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="40a07-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="40a07-190">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="40a07-191">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Host`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="40a07-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="40a07-192">O padrão é `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="40a07-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="40a07-193">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="40a07-194">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Proto`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="40a07-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="40a07-195">O padrão é `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="40a07-196">Limita o número de entradas nos cabeçalhos que são processados.</span><span class="sxs-lookup"><span data-stu-id="40a07-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="40a07-197">Defina para `null` para desabilitar o limite, mas isso só deve ser feito se `KnownProxies` ou `KnownNetworks` estão configurados.</span><span class="sxs-lookup"><span data-stu-id="40a07-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="40a07-198">A definição de um valor não `null` é feita por precaução (o que não implica em uma garantia), para proteção contra proxies mal configurados e solicitações mal-intencionadas que chegam de canais secundários na rede.</span><span class="sxs-lookup"><span data-stu-id="40a07-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="40a07-199">O middleware de cabeçalhos encaminhados processa cabeçalhos na ordem inversa, da direita para esquerda.</span><span class="sxs-lookup"><span data-stu-id="40a07-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="40a07-200">Se o valor padrão for usado (`1`), apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `ForwardLimit` seja aumentado.</span><span class="sxs-lookup"><span data-stu-id="40a07-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="40a07-201">O padrão é `1`.</span><span class="sxs-lookup"><span data-stu-id="40a07-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="40a07-202">Intervalos de endereços de redes conhecidas dos quais aceitar cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="40a07-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="40a07-203">Forneça os intervalos de IP usando notação de CIDR (Roteamento entre Domínios sem Classificação).</span><span class="sxs-lookup"><span data-stu-id="40a07-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="40a07-204">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="40a07-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="40a07-205">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="40a07-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="40a07-206">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="40a07-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="40a07-207">Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="40a07-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="40a07-208">O padrão é uma `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contém uma única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="40a07-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="40a07-209">Endereços de proxies conhecidos dos quais aceitar cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="40a07-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="40a07-210">Use `KnownProxies` especificar correspondências exatas de endereço IP.</span><span class="sxs-lookup"><span data-stu-id="40a07-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="40a07-211">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="40a07-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="40a07-212">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="40a07-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="40a07-213">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="40a07-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="40a07-214">Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="40a07-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="40a07-215">O padrão é uma `IList`\<<xref:System.Net.IPAddress>> que contém uma única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="40a07-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="40a07-216">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="40a07-217">O padrão é `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="40a07-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="40a07-218">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="40a07-219">O padrão é `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="40a07-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="40a07-220">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="40a07-221">O padrão é `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="40a07-222">Exigem o número de valores de cabeçalho a serem sincronizados entre os [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) sendo processados.</span><span class="sxs-lookup"><span data-stu-id="40a07-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="40a07-223">O padrão no ASP.NET Core 1.x é `true`.</span><span class="sxs-lookup"><span data-stu-id="40a07-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="40a07-224">O padrão no ASP.NET Core 2.0 ou posterior é `false`.</span><span class="sxs-lookup"><span data-stu-id="40a07-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="40a07-225">Cenários e casos de uso</span><span class="sxs-lookup"><span data-stu-id="40a07-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="40a07-226">Quando não é possível adicionar cabeçalhos encaminhados e todas as solicitações são seguras</span><span class="sxs-lookup"><span data-stu-id="40a07-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="40a07-227">Em alguns casos, pode não ser possível adicionar cabeçalhos encaminhados para as solicitações passadas por proxy ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40a07-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="40a07-228">Se o proxy está impondo que todas as solicitações externas públicas sejam HTTPS, o esquema pode ser definido manualmente em `Startup.Configure` antes de usar qualquer tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="40a07-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="40a07-229">Esse código pode ser desabilitado com uma variável de ambiente ou outra definição de configuração em um ambiente de preparo ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="40a07-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="40a07-230">Lidar com o caminho base e proxies que alteram o caminho da solicitação</span><span class="sxs-lookup"><span data-stu-id="40a07-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="40a07-231">Alguns proxies passam o caminho intacto, mas com um caminho base de aplicativo que deve ser removido para que o roteamento funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="40a07-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="40a07-232">O middleware de [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide o caminho em [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e o caminho base do aplicativo em [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="40a07-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="40a07-233">Se `/foo` é o caminho base do aplicativo para um caminho de proxy passado como `/foo/api/1`, o middleware define `Request.PathBase` para `/foo` e `Request.Path` para `/api/1` com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="40a07-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="40a07-234">O caminho original e o caminho base são reaplicados quando o middleware é chamado novamente na ordem inversa.</span><span class="sxs-lookup"><span data-stu-id="40a07-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="40a07-235">Para obter mais informações sobre o processamento de ordem de middleware, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="40a07-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="40a07-236">Se o proxy cortar o caminho (por exemplo, encaminhando `/foo/api/1` para `/api/1`), corrija redirecionamentos e links definindo a propriedade [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) da solicitação:</span><span class="sxs-lookup"><span data-stu-id="40a07-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="40a07-237">Se o proxy estiver adicionando dados de caminho, descarte a parte do caminho para corrigir os redirecionamentos e links usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> e atribuindo à propriedade <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="40a07-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="40a07-238">Configuração de um proxy que usa diferentes nomes de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="40a07-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="40a07-239">Se o proxy não usar cabeçalhos nomeados `X-Forwarded-For` e `X-Forwarded-Proto` para encaminhar a porta/endereço do proxy e as informações de origem do esquema, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo proxy:</span><span class="sxs-lookup"><span data-stu-id="40a07-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="40a07-240">Configuração de endereços IPv4 representados como endereços IPv6</span><span class="sxs-lookup"><span data-stu-id="40a07-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="40a07-241">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="40a07-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="40a07-242">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="40a07-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="40a07-243">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="40a07-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="40a07-244">No exemplo a seguir, um endereço de rede que fornece cabeçalhos encaminhados é adicionado à lista `KnownNetworks` no formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="40a07-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="40a07-245">Endereço IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="40a07-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="40a07-246">Conversão em endereço IPv6: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="40a07-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="40a07-247">Comprimento do prefixo convertido: 104</span><span class="sxs-lookup"><span data-stu-id="40a07-247">Converted prefix length: 104</span></span>

<span data-ttu-id="40a07-248">Você pode também fornecer o endereço no formato hexadecimal (`10.11.12.1`, representado no formato IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="40a07-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="40a07-249">Quando converter um endereço IPv4 em IPv6, adicione 96 ao comprimento do prefixo CIDR (`8` no exemplo) para levar em conta o prefixo IPv6 adicional `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="40a07-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="40a07-250">Encaminhar o esquema para proxies reversos não IIS e Linux</span><span class="sxs-lookup"><span data-stu-id="40a07-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="40a07-251">Os aplicativos que chamam <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> colocam um site em um loop infinito se implantado em um Serviço de Aplicativo do Linux do Azure, VM (máquina virtual) do Azure no Linux ou atrás de outro proxy reverso além do IIS.</span><span class="sxs-lookup"><span data-stu-id="40a07-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="40a07-252">O TLS é encerrado pelo proxy inverso e o Kestrel não é informado do esquema de solicitação correto.</span><span class="sxs-lookup"><span data-stu-id="40a07-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="40a07-253">OAuth e OIDC também falham nessa configuração, pois geram redirecionamentos incorretos.</span><span class="sxs-lookup"><span data-stu-id="40a07-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="40a07-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adiciona e configura o Middleware de Cabeçalhos Encaminhados quando executado atrás do IIS, mas não há nenhuma configuração automática correspondente para Linux (integração do Apache ou do Nginx).</span><span class="sxs-lookup"><span data-stu-id="40a07-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="40a07-255">Para encaminhar o esquema do proxy em cenários não de IIS, adicione e configure o Middleware de Cabeçalhos Encaminhados.</span><span class="sxs-lookup"><span data-stu-id="40a07-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="40a07-256">No `Startup.ConfigureServices`, use o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="40a07-256">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="40a07-257">Encaminhamento de certificado</span><span class="sxs-lookup"><span data-stu-id="40a07-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="40a07-258">Azure</span><span class="sxs-lookup"><span data-stu-id="40a07-258">Azure</span></span>

<span data-ttu-id="40a07-259">Para configurar o Azure App Service para encaminhamento de certificados, consulte [Configurar a autenticação mútua TLS para o Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="40a07-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="40a07-260">As seguintes orientações dizem respeito à configuração do aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40a07-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="40a07-261">Em `Startup.Configure`, adicione o seguinte `app.UseAuthentication();`código antes da chamada para:</span><span class="sxs-lookup"><span data-stu-id="40a07-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="40a07-262">Configure o Middleware de encaminhamento de certificados para especificar o nome de cabeçalho que o Azure usa.</span><span class="sxs-lookup"><span data-stu-id="40a07-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="40a07-263">Em `Startup.ConfigureServices`, adicione o seguinte código para configurar o cabeçalho a partir do qual o middleware constrói um certificado:</span><span class="sxs-lookup"><span data-stu-id="40a07-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="40a07-264">Outros proxies da web</span><span class="sxs-lookup"><span data-stu-id="40a07-264">Other web proxies</span></span>

<span data-ttu-id="40a07-265">Se for usado um proxy que não seja o ARR (Application Request Routing, roteamento de solicitação de solicitação do aplicativo do Azure App Service) do IIS ou do Azure App Service, configure o proxy para encaminhar o certificado recebido em um cabeçalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="40a07-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="40a07-266">Em `Startup.Configure`, adicione o seguinte `app.UseAuthentication();`código antes da chamada para:</span><span class="sxs-lookup"><span data-stu-id="40a07-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="40a07-267">Configure o Middleware de encaminhamento de certificados para especificar o nome do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="40a07-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="40a07-268">Em `Startup.ConfigureServices`, adicione o seguinte código para configurar o cabeçalho a partir do qual o middleware constrói um certificado:</span><span class="sxs-lookup"><span data-stu-id="40a07-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="40a07-269">Se o proxy não estiver codificando o certificado base64 (como é `HeaderConverter` o caso do Nginx), defina a opção.</span><span class="sxs-lookup"><span data-stu-id="40a07-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="40a07-270">Considere o exemplo a seguir em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="40a07-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="40a07-271">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="40a07-271">Troubleshoot</span></span>

<span data-ttu-id="40a07-272">Quando os cabeçalhos não são encaminhados conforme o esperado, habilite [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="40a07-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="40a07-273">Se os logs não fornecerem informações suficientes para solucionar o problema, enumere os cabeçalhos de solicitação recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="40a07-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="40a07-274">Use middleware embutido para gravar cabeçalhos de solicitação para uma resposta do aplicativo ou para log dos cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="40a07-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="40a07-275">Para gravar os cabeçalhos de resposta do aplicativo, coloque o seguinte middleware terminal embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="40a07-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="40a07-276">Você pode gravar em logs em vez de no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="40a07-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="40a07-277">A gravação em logs permite que o site funcione normalmente durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="40a07-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="40a07-278">Para gravar em logs em vez de no corpo da resposta:</span><span class="sxs-lookup"><span data-stu-id="40a07-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="40a07-279">Injete `ILogger<Startup>` na classe `Startup` conforme descrito em [Criar logs na inicialização](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="40a07-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="40a07-280">Coloque o seguinte middleware embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="40a07-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="40a07-281">Quando processado, os valores `X-Forwarded-{For|Proto|Host}` são movidos para `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="40a07-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="40a07-282">Quando houver vários valores em um determinado cabeçalho, o middleware de cabeçalhos encaminhados os processará na ordem inversa, da direita para esquerda.</span><span class="sxs-lookup"><span data-stu-id="40a07-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="40a07-283">O padrão `ForwardLimit` é `ForwardLimit` (um), portanto, apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `1` seja aumentado.</span><span class="sxs-lookup"><span data-stu-id="40a07-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="40a07-284">O IP de remoto original da solicitação precisa corresponder a uma entrada nas listas `KnownProxies` ou `KnownNetworks` antes dos cabeçalhos encaminhados serem processados.</span><span class="sxs-lookup"><span data-stu-id="40a07-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="40a07-285">Isso limita a falsificação de cabeçalho por não aceitar encaminhadores de proxies não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="40a07-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="40a07-286">Quando um proxy desconhecido é detectado, o registro em log indica o endereço dele:</span><span class="sxs-lookup"><span data-stu-id="40a07-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="40a07-287">No exemplo anterior, 10.0.0.100 é um servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="40a07-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="40a07-288">Se o servidor for um proxy confiável, adicione o endereço IP do servidor a `KnownProxies` (ou adicione uma rede confiável a `KnownNetworks`) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40a07-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="40a07-289">Para obter mais informações, consulte a seção [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="40a07-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="40a07-290">Permitir que somente proxies e redes confiáveis encaminhem os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="40a07-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="40a07-291">Caso contrário, podem ocorrer ataques de [falsificação de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="40a07-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40a07-292">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="40a07-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="40a07-293">Microsoft Security Advisory CVE-2018-0787: vulnerabilidade de elevação de privilégio do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40a07-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="40a07-294">Na configuração recomendada para o ASP.NET Core, o aplicativo é hospedado usando IIS/Módulo do ASP.NET Core, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="40a07-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="40a07-295">Servidores proxy, balanceadores de carga e outros dispositivos de rede geralmente ocultam informações sobre a solicitação antes de ela alcançar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="40a07-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="40a07-296">Quando solicitações HTTPS são passadas por proxy por HTTP, o esquema original (HTTPS) é perdido e deve ser encaminhado em um cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="40a07-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="40a07-297">Devido a um aplicativo receber uma solicitação do proxy e não de sua origem verdadeira na Internet ou rede corporativa, o endereço IP do cliente originador também deve ser encaminhado em um cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="40a07-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="40a07-298">Essas informações podem ser importantes no processamento de solicitações, por exemplo, em redirecionamentos, autenticação, geração de link, avaliação de política e localização geográfica do cliente.</span><span class="sxs-lookup"><span data-stu-id="40a07-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="40a07-299">Cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="40a07-299">Forwarded headers</span></span>

<span data-ttu-id="40a07-300">Por convenção, os proxies encaminham informações em cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="40a07-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="40a07-301">Cabeçalho</span><span class="sxs-lookup"><span data-stu-id="40a07-301">Header</span></span> | <span data-ttu-id="40a07-302">Descrição</span><span class="sxs-lookup"><span data-stu-id="40a07-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="40a07-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="40a07-303">X-Forwarded-For</span></span> | <span data-ttu-id="40a07-304">Contém informações sobre o cliente que iniciou a solicitação e os proxies subsequentes em uma cadeia de proxies.</span><span class="sxs-lookup"><span data-stu-id="40a07-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="40a07-305">Esse parâmetro pode conter endereços IP (e, opcionalmente, os números de porta).</span><span class="sxs-lookup"><span data-stu-id="40a07-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="40a07-306">Em uma cadeia de servidores proxy, o primeiro parâmetro indica o cliente em que a solicitação foi feita pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="40a07-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="40a07-307">Depois, vêm os identificadores de proxy subsequentes.</span><span class="sxs-lookup"><span data-stu-id="40a07-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="40a07-308">O último proxy na cadeia não está na lista de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="40a07-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="40a07-309">O endereço IP do último proxy (e opcionalmente um número da porta) estão disponíveis como o endereço IP remoto na camada de transporte.</span><span class="sxs-lookup"><span data-stu-id="40a07-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="40a07-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="40a07-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="40a07-311">O valor do esquema de origem (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="40a07-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="40a07-312">O valor também pode ser uma lista de esquemas se a solicitação percorreu vários proxies.</span><span class="sxs-lookup"><span data-stu-id="40a07-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="40a07-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="40a07-313">X-Forwarded-Host</span></span> | <span data-ttu-id="40a07-314">O valor original do campo de cabeçalho do host.</span><span class="sxs-lookup"><span data-stu-id="40a07-314">The original value of the Host header field.</span></span> <span data-ttu-id="40a07-315">Normalmente, os proxies não modificam o cabeçalho do host.</span><span class="sxs-lookup"><span data-stu-id="40a07-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="40a07-316">Veja [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para obter informações sobre uma vulnerabilidade de elevação de privilégios que afeta os sistemas em que o proxy não valida ou restringe cabeçalhos de Host a valores válidos conhecidos.</span><span class="sxs-lookup"><span data-stu-id="40a07-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="40a07-317">O middleware de cabeçalhos encaminhados, do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lê esses cabeçalhos e preenche os campos associados em <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="40a07-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="40a07-318">As atualizações de middleware:</span><span class="sxs-lookup"><span data-stu-id="40a07-318">The middleware updates:</span></span>

* <span data-ttu-id="40a07-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="40a07-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="40a07-320">Configurações adicionais influenciam o modo como o middleware define `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="40a07-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="40a07-321">Para obter detalhes, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="40a07-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="40a07-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="40a07-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="40a07-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="40a07-324">As [configurações padrão](#forwarded-headers-middleware-options) de middleware de cabeçalhos encaminhados podem ser definidas.</span><span class="sxs-lookup"><span data-stu-id="40a07-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="40a07-325">As configurações padrão são:</span><span class="sxs-lookup"><span data-stu-id="40a07-325">The default settings are:</span></span>

* <span data-ttu-id="40a07-326">Há apenas *um proxy* entre o aplicativo e a origem das solicitações.</span><span class="sxs-lookup"><span data-stu-id="40a07-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="40a07-327">Somente os endereços de loopback são configurados para proxies conhecidos e redes conhecidas.</span><span class="sxs-lookup"><span data-stu-id="40a07-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="40a07-328">Os cabeçalhos encaminhados são nomeados `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="40a07-329">Nem todos os dispositivos de rede adicionam os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="40a07-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="40a07-330">Consulte as diretrizes do fabricante do dispositivo se as solicitações de proxies não contiverem esses cabeçalhos quando atingirem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40a07-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="40a07-331">Se o dispositivo usar nomes de cabeçalho diferente de `X-Forwarded-For` e `X-Forwarded-Proto`, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo dispositivo.</span><span class="sxs-lookup"><span data-stu-id="40a07-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="40a07-332">Para obter mais informações, consulte [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) (Opções de middleware de cabeçalhos encaminhados) e [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names) (Configuração de um proxy que usa diferentes nomes de cabeçalho).</span><span class="sxs-lookup"><span data-stu-id="40a07-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="40a07-333">O IIS/IIS Express e o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40a07-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="40a07-334">O Middleware de Cabeçalhos Encaminhados é habilitado por padrão pelo [Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando o aplicativo é hospedado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) atrás do IIS e do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40a07-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="40a07-335">O middleware de cabeçalhos encaminhados é ativado para ser executado primeiro no pipeline de middleware, com uma configuração restrita específica para o Módulo do ASP.NET Core devido a questões de confiança com cabeçalhos encaminhados (por exemplo, [falsificação de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="40a07-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="40a07-336">O middleware está configurado para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` e é restrito a um proxy de localhost único.</span><span class="sxs-lookup"><span data-stu-id="40a07-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="40a07-337">Se configuração adicional for necessária, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="40a07-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="40a07-338">Outros cenários de servidor proxy e balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="40a07-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="40a07-339">Além de usar a [Integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando hospedar [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), o Middleware de cabeçalhos encaminhados não é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="40a07-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="40a07-340">O middleware de cabeçalhos encaminhados deve ser habilitado para um aplicativo para processar cabeçalhos encaminhados com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="40a07-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="40a07-341">Após a habilitação do middleware, se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, o [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) padrão será [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="40a07-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="40a07-342">Configure o middleware com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40a07-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="40a07-343">Invocar o método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> no `Startup.Configure` antes de chamar outro middleware:</span><span class="sxs-lookup"><span data-stu-id="40a07-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="40a07-344">Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado no `Startup.ConfigureServices` ou diretamente no método de extensão com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, os cabeçalhos padrão para encaminhar serão [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="40a07-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="40a07-345">A propriedade <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve ser configurada com os cabeçalhos para encaminhar.</span><span class="sxs-lookup"><span data-stu-id="40a07-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="40a07-346">Configuração de Nginx</span><span class="sxs-lookup"><span data-stu-id="40a07-346">Nginx configuration</span></span>

<span data-ttu-id="40a07-347">Para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="40a07-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="40a07-348">Para obter mais informações, veja [NGINX: usando o cabeçalho encaminhado](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="40a07-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="40a07-349">Configuração do Apache</span><span class="sxs-lookup"><span data-stu-id="40a07-349">Apache configuration</span></span>

<span data-ttu-id="40a07-350">`X-Forwarded-For` é adicionado automaticamente (veja [mod_proxy do módulo do Apache: cabeçalhos de solicitação de proxy reverso](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="40a07-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="40a07-351">Para obter informações sobre como encaminhar o cabeçalho `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="40a07-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="40a07-352">Opções de middleware de cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="40a07-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="40a07-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla o comportamento do middleware de cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="40a07-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="40a07-354">O seguinte exemplo altera os valores padrão:</span><span class="sxs-lookup"><span data-stu-id="40a07-354">The following example changes the default values:</span></span>

* <span data-ttu-id="40a07-355">Limite o número de entradas nos cabeçalhos encaminhados a `2`.</span><span class="sxs-lookup"><span data-stu-id="40a07-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="40a07-356">Adicione um endereço de proxy conhecido de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="40a07-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="40a07-357">Altere o nome do cabeçalho encaminhado do padrão `X-Forwarded-For` para `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="40a07-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="40a07-358">Opção</span><span class="sxs-lookup"><span data-stu-id="40a07-358">Option</span></span> | <span data-ttu-id="40a07-359">Descrição</span><span class="sxs-lookup"><span data-stu-id="40a07-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="40a07-360">Restringe os hosts com o cabeçalho `X-Forwarded-Host` para os valores fornecidos.</span><span class="sxs-lookup"><span data-stu-id="40a07-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="40a07-361">Os valores são comparados usando ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="40a07-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="40a07-362">Os número de porta devem ser excluídos.</span><span class="sxs-lookup"><span data-stu-id="40a07-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="40a07-363">Se a lista estiver vazia, todos os hosts serão permitidos.</span><span class="sxs-lookup"><span data-stu-id="40a07-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="40a07-364">Um curinga de nível superior `*` permite todos os hosts não vazios.</span><span class="sxs-lookup"><span data-stu-id="40a07-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="40a07-365">Curingas de subdomínio são permitidos, mas não correspondem ao domínio raiz.</span><span class="sxs-lookup"><span data-stu-id="40a07-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="40a07-366">Por exemplo, `*.contoso.com` corresponde o subdomínio `foo.contoso.com`, mas não ao domínio raiz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="40a07-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="40a07-367">Nomes do host Unicode são permitidos, mas são convertidos em [Punycode](https://tools.ietf.org/html/rfc3492) para correspondência.</span><span class="sxs-lookup"><span data-stu-id="40a07-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="40a07-368">[Endereços IPv6](https://tools.ietf.org/html/rfc4291) devem incluir colchetes delimitadores e estar no [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por exemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="40a07-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="40a07-369">Endereços IPv6 não têm caso especial para verificar se há igualdade lógica entre formatos diferentes, e nenhuma canonicalização é executada.</span><span class="sxs-lookup"><span data-stu-id="40a07-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="40a07-370">Falha ao restringir os hosts permitidos pode permitir que um atacante falsifique links gerados pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="40a07-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="40a07-371">O valor padrão é um `IList<string>` vazio.</span><span class="sxs-lookup"><span data-stu-id="40a07-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="40a07-372">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="40a07-373">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-For`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="40a07-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="40a07-374">O padrão é `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="40a07-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="40a07-375">Identifica quais encaminhadores devem ser processados.</span><span class="sxs-lookup"><span data-stu-id="40a07-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="40a07-376">Veja o [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para a lista de campos aplicáveis.</span><span class="sxs-lookup"><span data-stu-id="40a07-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="40a07-377">Os valores típicos atribuídos a essa propriedade são `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="40a07-378">O valor padrão é [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="40a07-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="40a07-379">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="40a07-380">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Host`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="40a07-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="40a07-381">O padrão é `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="40a07-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="40a07-382">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="40a07-383">Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Proto`, mas usa algum outro cabeçalho para encaminhar as informações.</span><span class="sxs-lookup"><span data-stu-id="40a07-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="40a07-384">O padrão é `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="40a07-385">Limita o número de entradas nos cabeçalhos que são processados.</span><span class="sxs-lookup"><span data-stu-id="40a07-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="40a07-386">Defina para `null` para desabilitar o limite, mas isso só deve ser feito se `KnownProxies` ou `KnownNetworks` estão configurados.</span><span class="sxs-lookup"><span data-stu-id="40a07-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="40a07-387">A definição de um valor não `null` é feita por precaução (o que não implica em uma garantia), para proteção contra proxies mal configurados e solicitações mal-intencionadas que chegam de canais secundários na rede.</span><span class="sxs-lookup"><span data-stu-id="40a07-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="40a07-388">O middleware de cabeçalhos encaminhados processa cabeçalhos na ordem inversa, da direita para esquerda.</span><span class="sxs-lookup"><span data-stu-id="40a07-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="40a07-389">Se o valor padrão for usado (`1`), apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `ForwardLimit` seja aumentado.</span><span class="sxs-lookup"><span data-stu-id="40a07-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="40a07-390">O padrão é `1`.</span><span class="sxs-lookup"><span data-stu-id="40a07-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="40a07-391">Intervalos de endereços de redes conhecidas dos quais aceitar cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="40a07-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="40a07-392">Forneça os intervalos de IP usando notação de CIDR (Roteamento entre Domínios sem Classificação).</span><span class="sxs-lookup"><span data-stu-id="40a07-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="40a07-393">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="40a07-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="40a07-394">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="40a07-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="40a07-395">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="40a07-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="40a07-396">Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="40a07-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="40a07-397">O padrão é uma `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contém uma única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="40a07-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="40a07-398">Endereços de proxies conhecidos dos quais aceitar cabeçalhos encaminhados.</span><span class="sxs-lookup"><span data-stu-id="40a07-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="40a07-399">Use `KnownProxies` especificar correspondências exatas de endereço IP.</span><span class="sxs-lookup"><span data-stu-id="40a07-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="40a07-400">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="40a07-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="40a07-401">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="40a07-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="40a07-402">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="40a07-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="40a07-403">Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="40a07-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="40a07-404">O padrão é uma `IList`\<<xref:System.Net.IPAddress>> que contém uma única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="40a07-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="40a07-405">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="40a07-406">O padrão é `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="40a07-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="40a07-407">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="40a07-408">O padrão é `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="40a07-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="40a07-409">Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="40a07-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="40a07-410">O padrão é `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="40a07-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="40a07-411">Exigem o número de valores de cabeçalho a serem sincronizados entre os [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) sendo processados.</span><span class="sxs-lookup"><span data-stu-id="40a07-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="40a07-412">O padrão no ASP.NET Core 1.x é `true`.</span><span class="sxs-lookup"><span data-stu-id="40a07-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="40a07-413">O padrão no ASP.NET Core 2.0 ou posterior é `false`.</span><span class="sxs-lookup"><span data-stu-id="40a07-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="40a07-414">Cenários e casos de uso</span><span class="sxs-lookup"><span data-stu-id="40a07-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="40a07-415">Quando não é possível adicionar cabeçalhos encaminhados e todas as solicitações são seguras</span><span class="sxs-lookup"><span data-stu-id="40a07-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="40a07-416">Em alguns casos, pode não ser possível adicionar cabeçalhos encaminhados para as solicitações passadas por proxy ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40a07-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="40a07-417">Se o proxy está impondo que todas as solicitações externas públicas sejam HTTPS, o esquema pode ser definido manualmente em `Startup.Configure` antes de usar qualquer tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="40a07-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="40a07-418">Esse código pode ser desabilitado com uma variável de ambiente ou outra definição de configuração em um ambiente de preparo ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="40a07-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="40a07-419">Lidar com o caminho base e proxies que alteram o caminho da solicitação</span><span class="sxs-lookup"><span data-stu-id="40a07-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="40a07-420">Alguns proxies passam o caminho intacto, mas com um caminho base de aplicativo que deve ser removido para que o roteamento funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="40a07-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="40a07-421">O middleware de [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide o caminho em [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e o caminho base do aplicativo em [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="40a07-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="40a07-422">Se `/foo` é o caminho base do aplicativo para um caminho de proxy passado como `/foo/api/1`, o middleware define `Request.PathBase` para `/foo` e `Request.Path` para `/api/1` com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="40a07-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="40a07-423">O caminho original e o caminho base são reaplicados quando o middleware é chamado novamente na ordem inversa.</span><span class="sxs-lookup"><span data-stu-id="40a07-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="40a07-424">Para obter mais informações sobre o processamento de ordem de middleware, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="40a07-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="40a07-425">Se o proxy cortar o caminho (por exemplo, encaminhando `/foo/api/1` para `/api/1`), corrija redirecionamentos e links definindo a propriedade [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) da solicitação:</span><span class="sxs-lookup"><span data-stu-id="40a07-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="40a07-426">Se o proxy estiver adicionando dados de caminho, descarte a parte do caminho para corrigir os redirecionamentos e links usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> e atribuindo à propriedade <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="40a07-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="40a07-427">Configuração de um proxy que usa diferentes nomes de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="40a07-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="40a07-428">Se o proxy não usar cabeçalhos nomeados `X-Forwarded-For` e `X-Forwarded-Proto` para encaminhar a porta/endereço do proxy e as informações de origem do esquema, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo proxy:</span><span class="sxs-lookup"><span data-stu-id="40a07-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="40a07-429">Configuração de endereços IPv4 representados como endereços IPv6</span><span class="sxs-lookup"><span data-stu-id="40a07-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="40a07-430">O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="40a07-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="40a07-431">Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="40a07-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="40a07-432">Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="40a07-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="40a07-433">No exemplo a seguir, um endereço de rede que fornece cabeçalhos encaminhados é adicionado à lista `KnownNetworks` no formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="40a07-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="40a07-434">Endereço IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="40a07-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="40a07-435">Conversão em endereço IPv6: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="40a07-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="40a07-436">Comprimento do prefixo convertido: 104</span><span class="sxs-lookup"><span data-stu-id="40a07-436">Converted prefix length: 104</span></span>

<span data-ttu-id="40a07-437">Você pode também fornecer o endereço no formato hexadecimal (`10.11.12.1`, representado no formato IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="40a07-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="40a07-438">Quando converter um endereço IPv4 em IPv6, adicione 96 ao comprimento do prefixo CIDR (`8` no exemplo) para levar em conta o prefixo IPv6 adicional `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="40a07-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="40a07-439">Encaminhar o esquema para proxies reversos não IIS e Linux</span><span class="sxs-lookup"><span data-stu-id="40a07-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="40a07-440">Os aplicativos que chamam <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> colocam um site em um loop infinito se implantado em um Serviço de Aplicativo do Linux do Azure, VM (máquina virtual) do Azure no Linux ou atrás de outro proxy reverso além do IIS.</span><span class="sxs-lookup"><span data-stu-id="40a07-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="40a07-441">O TLS é encerrado pelo proxy inverso e o Kestrel não é informado do esquema de solicitação correto.</span><span class="sxs-lookup"><span data-stu-id="40a07-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="40a07-442">OAuth e OIDC também falham nessa configuração, pois geram redirecionamentos incorretos.</span><span class="sxs-lookup"><span data-stu-id="40a07-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="40a07-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adiciona e configura o Middleware de Cabeçalhos Encaminhados quando executado atrás do IIS, mas não há nenhuma configuração automática correspondente para Linux (integração do Apache ou do Nginx).</span><span class="sxs-lookup"><span data-stu-id="40a07-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="40a07-444">Para encaminhar o esquema do proxy em cenários não de IIS, adicione e configure o Middleware de Cabeçalhos Encaminhados.</span><span class="sxs-lookup"><span data-stu-id="40a07-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="40a07-445">No `Startup.ConfigureServices`, use o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="40a07-445">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="40a07-446">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="40a07-446">Troubleshoot</span></span>

<span data-ttu-id="40a07-447">Quando os cabeçalhos não são encaminhados conforme o esperado, habilite [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="40a07-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="40a07-448">Se os logs não fornecerem informações suficientes para solucionar o problema, enumere os cabeçalhos de solicitação recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="40a07-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="40a07-449">Use middleware embutido para gravar cabeçalhos de solicitação para uma resposta do aplicativo ou para log dos cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="40a07-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="40a07-450">Para gravar os cabeçalhos de resposta do aplicativo, coloque o seguinte middleware terminal embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="40a07-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="40a07-451">Você pode gravar em logs em vez de no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="40a07-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="40a07-452">A gravação em logs permite que o site funcione normalmente durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="40a07-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="40a07-453">Para gravar em logs em vez de no corpo da resposta:</span><span class="sxs-lookup"><span data-stu-id="40a07-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="40a07-454">Injete `ILogger<Startup>` na classe `Startup` conforme descrito em [Criar logs na inicialização](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="40a07-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="40a07-455">Coloque o seguinte middleware embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="40a07-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="40a07-456">Quando processado, os valores `X-Forwarded-{For|Proto|Host}` são movidos para `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="40a07-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="40a07-457">Quando houver vários valores em um determinado cabeçalho, o middleware de cabeçalhos encaminhados os processará na ordem inversa, da direita para esquerda.</span><span class="sxs-lookup"><span data-stu-id="40a07-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="40a07-458">O padrão `ForwardLimit` é `ForwardLimit` (um), portanto, apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `1` seja aumentado.</span><span class="sxs-lookup"><span data-stu-id="40a07-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="40a07-459">O IP de remoto original da solicitação precisa corresponder a uma entrada nas listas `KnownProxies` ou `KnownNetworks` antes dos cabeçalhos encaminhados serem processados.</span><span class="sxs-lookup"><span data-stu-id="40a07-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="40a07-460">Isso limita a falsificação de cabeçalho por não aceitar encaminhadores de proxies não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="40a07-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="40a07-461">Quando um proxy desconhecido é detectado, o registro em log indica o endereço dele:</span><span class="sxs-lookup"><span data-stu-id="40a07-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="40a07-462">No exemplo anterior, 10.0.0.100 é um servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="40a07-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="40a07-463">Se o servidor for um proxy confiável, adicione o endereço IP do servidor a `KnownProxies` (ou adicione uma rede confiável a `KnownNetworks`) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40a07-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="40a07-464">Para obter mais informações, consulte a seção [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="40a07-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="40a07-465">Permitir que somente proxies e redes confiáveis encaminhem os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="40a07-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="40a07-466">Caso contrário, podem ocorrer ataques de [falsificação de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="40a07-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40a07-467">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="40a07-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="40a07-468">Microsoft Security Advisory CVE-2018-0787: vulnerabilidade de elevação de privilégio do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40a07-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
