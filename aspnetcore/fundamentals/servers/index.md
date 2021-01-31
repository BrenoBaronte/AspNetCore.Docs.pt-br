---
title: Implementações de servidor Web em ASP.NET Core
author: rick-anderson
description: Descubra os servidores Web Kestrel e HTTP.sys para ASP.NET Core. Saiba como escolher um servidor e quando usar um servidor proxy reverso.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 2acddd212639ac0a82b3c46f2225ff66d0999dd0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217551"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="bbd9d-104">Implementações de servidor Web em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbd9d-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="bbd9d-105">Por [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) e [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="bbd9d-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="bbd9d-106">Um aplicativo ASP.NET Core é executado com uma implementação do servidor HTTP em processo.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="bbd9d-107">A implementação do servidor escuta solicitações HTTP e apresenta-as para o aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="bbd9d-108">Windows</span><span class="sxs-lookup"><span data-stu-id="bbd9d-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="bbd9d-109">O ASP.NET Core vem com os seguintes itens:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="bbd9d-110">O [servidor Kestrel](xref:fundamentals/servers/kestrel) é a implementação padrão do servidor HTTP multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="bbd9d-111">O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados do HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="bbd9d-112">Para obter mais informações, consulte [Kestrel vs. HTTP.sys](#korh) neste documento.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-112">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>
* <span data-ttu-id="bbd9d-113">O servidor HTTP do IIS é um [servidor em processo](#hosting-models) do IIS.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-113">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="bbd9d-114">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor HTTP somente do Windows com base no [driver do kernel HTTP.sys e na API do servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="bbd9d-115">Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-115">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="bbd9d-116">No mesmo processo que o processo de trabalho do IIS (o [modelo de hospedagem em processo](#hosting-models)) com o servidor http IIS.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-116">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="bbd9d-117">*Em processo* é a configuração recomendada.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-117">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="bbd9d-118">Em um processo separado do processo de trabalho do IIS (o [modelo de hospedagem fora do processo](#hosting-models)) com o [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-118">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="bbd9d-119">O [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) é um módulo nativo do IIS que manipula as solicitações nativas do IIS entre o IIS e o servidor HTTP do IIS em processo ou o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-119">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="bbd9d-120">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-120">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a><span data-ttu-id="bbd9d-121">Kestrel versus HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="bbd9d-121">Kestrel vs. HTTP.sys</span></span>

<span data-ttu-id="bbd9d-122">O Kestrel tem as seguintes vantagens em relação ao HTTP.sys:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-122">Kestrel has the following advantages over HTTP.sys:</span></span>

  * <span data-ttu-id="bbd9d-123">Melhor utilização de memória e desempenho.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-123">Better performance and memory utilization.</span></span>
  * <span data-ttu-id="bbd9d-124">Plataforma cruzada</span><span class="sxs-lookup"><span data-stu-id="bbd9d-124">Cross platform</span></span>
  * <span data-ttu-id="bbd9d-125">Agilidade, ela é desenvolvida e corrigida de forma independente do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-125">Agility, it's developed and patched independent of the OS.</span></span>
  * <span data-ttu-id="bbd9d-126">Configuração de TLS e porta programática</span><span class="sxs-lookup"><span data-stu-id="bbd9d-126">Programmatic port and TLS configuration</span></span>
  * <span data-ttu-id="bbd9d-127">Extensibilidade que permite protocolos como [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) e transportes alternativos.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-127">Extensibility that allows for protocols like [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) and alternate transports.</span></span>

<span data-ttu-id="bbd9d-128">Http.Sys funciona como um componente de modo kernel compartilhado com os seguintes recursos que o Kestrel não tem:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-128">Http.Sys operates as a shared kernel mode component with the following features that kestrel does not have:</span></span>

  * <span data-ttu-id="bbd9d-129">Compartilhamento de porta</span><span class="sxs-lookup"><span data-stu-id="bbd9d-129">Port sharing</span></span>
  * <span data-ttu-id="bbd9d-130">Autenticação do Windows no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-130">Kernel mode windows authentication.</span></span> <span data-ttu-id="bbd9d-131">O [Kestrel dá suporte apenas à autenticação de modo de usuário](xref:security/authentication/windowsauth#kestrel).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-131">[Kestrel supports only user-mode authentication](xref:security/authentication/windowsauth#kestrel).</span></span>
  * <span data-ttu-id="bbd9d-132">Proxy rápido por meio de transferências de fila</span><span class="sxs-lookup"><span data-stu-id="bbd9d-132">Fast proxying via queue transfers</span></span>
  * <span data-ttu-id="bbd9d-133">Transmissão direta de arquivo</span><span class="sxs-lookup"><span data-stu-id="bbd9d-133">Direct file transmission</span></span>
  * <span data-ttu-id="bbd9d-134">Cache de resposta</span><span class="sxs-lookup"><span data-stu-id="bbd9d-134">Response caching</span></span>

## <a name="hosting-models"></a><span data-ttu-id="bbd9d-135">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="bbd9d-135">Hosting models</span></span>

<span data-ttu-id="bbd9d-136">Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-136">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="bbd9d-137">A hospedagem em processo oferece desempenho melhor em hospedagem fora do processo porque as solicitações não são transmitidas por proxy pelo adaptador de loopback, um adaptador de rede que retorna o tráfego de rede de saída para o mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-137">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="bbd9d-138">O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-138">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="bbd9d-139">Usando a hospedagem fora do processo, os aplicativos do ASP.NET Core são executados em um processo separado do processo de trabalho do IIS e o módulo cuida do gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-139">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="bbd9d-140">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-140">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="bbd9d-141">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-141">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="bbd9d-142">Para obter mais informações e orientação sobre a configuração, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-142">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="bbd9d-143">macOS</span><span class="sxs-lookup"><span data-stu-id="bbd9d-143">macOS</span></span>](#tab/macos)

<span data-ttu-id="bbd9d-144">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-144">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="bbd9d-145">Linux</span><span class="sxs-lookup"><span data-stu-id="bbd9d-145">Linux</span></span>](#tab/linux)

<span data-ttu-id="bbd9d-146">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-146">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="bbd9d-147">Kestrel</span><span class="sxs-lookup"><span data-stu-id="bbd9d-147">Kestrel</span></span>

 <span data-ttu-id="bbd9d-148">O [servidor Kestrel](xref:fundamentals/servers/kestrel) é a implementação padrão do servidor HTTP multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-148">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="bbd9d-149">O Kestrel fornece o melhor desempenho e utilização de memória, mas não tem alguns dos recursos avançados do HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-149">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="bbd9d-150">Para obter mais informações, consulte [Kestrel vs. HTTP.sys](#korh) neste documento.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-150">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>

<span data-ttu-id="bbd9d-151">Use o Kestrel:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-151">Use Kestrel:</span></span>

* <span data-ttu-id="bbd9d-152">Sozinho, como um servidor de borda que processa solicitações diretamente de uma rede, incluindo a Internet.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-152">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="bbd9d-154">Com um *servidor proxy reverso* como [IIS (Serviços de Informações da Internet)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-154">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="bbd9d-155">Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-155">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="bbd9d-157">&mdash;Há suporte para hospedar a configuração com ou sem um servidor proxy reverso &mdash; .</span><span class="sxs-lookup"><span data-stu-id="bbd9d-157">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="bbd9d-158">Para obter diretrizes de configuração do Kestrel e informações sobre quando usar o Kestrel em uma configuração de proxy reverso, confira <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-158">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="bbd9d-159">Windows</span><span class="sxs-lookup"><span data-stu-id="bbd9d-159">Windows</span></span>](#tab/windows)

<span data-ttu-id="bbd9d-160">O ASP.NET Core vem com os seguintes itens:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-160">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="bbd9d-161">O [servidor Kestrel](xref:fundamentals/servers/kestrel) é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-161">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="bbd9d-162">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor HTTP somente do Windows com base no [driver do kernel HTTP.sys e na API do servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-162">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="bbd9d-163">Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado em um processo separado do processo de trabalho do IIS (*fora do processo*) com o [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-163">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="bbd9d-164">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-164">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="bbd9d-165">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-165">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="bbd9d-166">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-166">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="bbd9d-167">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-167">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="bbd9d-169">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-169">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="bbd9d-170">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-170">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="bbd9d-171">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-171">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="bbd9d-172">O módulo Especifica a porta por meio de uma variável de ambiente na inicialização, e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor a ser escutado `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="bbd9d-172">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="bbd9d-173">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-173">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="bbd9d-174">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-174">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="bbd9d-175">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-175">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="bbd9d-176">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-176">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="bbd9d-177">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-177">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="bbd9d-178">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-178">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="bbd9d-179">Para obter as diretrizes de configuração do IIS e do módulo do ASP.NET Core, confira os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-179">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="bbd9d-180">macOS</span><span class="sxs-lookup"><span data-stu-id="bbd9d-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="bbd9d-181">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-181">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="bbd9d-182">Linux</span><span class="sxs-lookup"><span data-stu-id="bbd9d-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="bbd9d-183">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="bbd9d-184">Nginx com Kestrel</span><span class="sxs-lookup"><span data-stu-id="bbd9d-184">Nginx with Kestrel</span></span>

<span data-ttu-id="bbd9d-185">Para obter informações sobre como usar Nginx no Linux como um servidor proxy reverso para Kestrel, confira <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-185">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="bbd9d-186">Apache com Kestrel</span><span class="sxs-lookup"><span data-stu-id="bbd9d-186">Apache with Kestrel</span></span>

<span data-ttu-id="bbd9d-187">Para obter informações sobre como usar Apache no Linux como um servidor proxy reverso para Kestrel, confira <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-187">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="bbd9d-188">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="bbd9d-188">HTTP.sys</span></span>

<span data-ttu-id="bbd9d-189">Se os aplicativos ASP.NET Core forem executados no Windows, o HTTP.sys será uma alternativa ao Kestrel.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-189">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="bbd9d-190">O Kestrel é recomendado em HTTP.sys, a menos que o aplicativo exija recursos não disponíveis no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-190">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="bbd9d-191">Para obter mais informações, consulte <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-191">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![O HTTP.sys se comunica diretamente com a Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="bbd9d-193">O HTTP.sys também pode ser usado para aplicativos que são expostos somente a uma rede interna.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-193">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![O HTTP.sys se comunica diretamente com a rede interna](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="bbd9d-195">Para obter as diretrizes de configuração do HTTP.sys, confira <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-195">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="bbd9d-196">Infraestrutura de servidor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbd9d-196">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="bbd9d-197">O <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponível no método `Startup.Configure` expõe a propriedade <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> do tipo <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-197">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="bbd9d-198">O Kestrel e o HTTP.sys expõem apenas um único recurso cada, o <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, mas diferentes implementações de servidor podem expor funcionalidades adicionais.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-198">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="bbd9d-199">`IServerAddressesFeature` pode ser usado para descobrir a qual porta a implementação do servidor se acoplou durante o runtime.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-199">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="bbd9d-200">Servidores personalizados</span><span class="sxs-lookup"><span data-stu-id="bbd9d-200">Custom servers</span></span>

<span data-ttu-id="bbd9d-201">Se os servidores internos não atenderem aos requisitos do aplicativo, um servidor personalizado poderá ser criado.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-201">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="bbd9d-202">O [Guia de OWIN (Open Web Interface para .NET)](xref:fundamentals/owin) demonstra como gravar uma implementação [com base em ](https://github.com/Bobris/Nowin)Nowin<xref:Microsoft.AspNetCore.Hosting.Server.IServer>.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-202">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="bbd9d-203">Somente as interfaces de recurso que o aplicativo usa exigem implementação, embora no mínimo <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> e <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> devam ser compatíveis.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-203">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="bbd9d-204">Inicialização do servidor</span><span class="sxs-lookup"><span data-stu-id="bbd9d-204">Server startup</span></span>

<span data-ttu-id="bbd9d-205">O servidor é iniciado quando o IDE (Ambiente de Desenvolvimento Integrado) ou o editor inicia o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-205">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="bbd9d-206">[Visual Studio](https://visualstudio.microsoft.com): os perfis de inicialização podem ser usados para iniciar o aplicativo e o servidor com o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core módulo](xref:host-and-deploy/aspnet-core-module) ou o console do.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-206">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="bbd9d-207">[Visual Studio Code](https://code.visualstudio.com/): o aplicativo e o servidor são iniciados pelo [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), que ativa o depurador CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-207">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="bbd9d-208">[Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/): o aplicativo e o servidor são iniciados pelo [depurador de Soft-Mode mono](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="bbd9d-209">Ao iniciar o aplicativo usando um prompt de comando na pasta do projeto, o [dotnet run](/dotnet/core/tools/dotnet-run) inicia o aplicativo e o servidor (apenas Kestrel e HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-209">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="bbd9d-210">A configuração é especificada pela opção `-c|--configuration`, que é definida como `Debug` (padrão) ou `Release`.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-210">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="bbd9d-211">Um *launchSettings.jsno* arquivo fornece configuração ao iniciar um aplicativo com `dotnet run` o ou com um depurador interno de ferramentas, como o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-211">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="bbd9d-212">Se os perfis de inicialização estiverem presentes em um *launchSettings.jsno* arquivo, use a `--launch-profile {PROFILE NAME}` opção com o `dotnet run` comando ou selecione o perfil no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-212">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="bbd9d-213">Para obter mais informações, confira [dotnet run](/dotnet/core/tools/dotnet-run) e [pacote de distribuição do .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="bbd9d-213">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="bbd9d-214">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="bbd9d-214">HTTP/2 support</span></span>

<span data-ttu-id="bbd9d-215">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação:</span><span class="sxs-lookup"><span data-stu-id="bbd9d-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="bbd9d-216">Kestrel</span><span class="sxs-lookup"><span data-stu-id="bbd9d-216">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="bbd9d-217">Sistema operacional</span><span class="sxs-lookup"><span data-stu-id="bbd9d-217">Operating system</span></span>
    * <span data-ttu-id="bbd9d-218">Windows Server 2016/Windows 10 ou posterior&dagger;</span><span class="sxs-lookup"><span data-stu-id="bbd9d-218">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="bbd9d-219">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="bbd9d-219">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="bbd9d-220">O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-220">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="bbd9d-221">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-221">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="bbd9d-222">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="bbd9d-222">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="bbd9d-223">Windows Server 2016/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-223">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="bbd9d-224">Estrutura de destino: não aplicável a implantações de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-224">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="bbd9d-225">IIS (em processo)</span><span class="sxs-lookup"><span data-stu-id="bbd9d-225">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="bbd9d-226">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-226">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="bbd9d-227">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-227">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="bbd9d-228">IIS (fora do processo)</span><span class="sxs-lookup"><span data-stu-id="bbd9d-228">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="bbd9d-229">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="bbd9d-230">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-230">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="bbd9d-231">Estrutura de destino: não aplicável a implantações IIS fora de processo.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-231">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="bbd9d-232">&dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-232">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="bbd9d-233">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-233">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="bbd9d-234">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-234">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="bbd9d-235">Kestrel</span><span class="sxs-lookup"><span data-stu-id="bbd9d-235">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="bbd9d-236">Sistema operacional</span><span class="sxs-lookup"><span data-stu-id="bbd9d-236">Operating system</span></span>
    * <span data-ttu-id="bbd9d-237">Windows Server 2016/Windows 10 ou posterior&dagger;</span><span class="sxs-lookup"><span data-stu-id="bbd9d-237">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="bbd9d-238">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="bbd9d-238">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="bbd9d-239">O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-239">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="bbd9d-240">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-240">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="bbd9d-241">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="bbd9d-241">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="bbd9d-242">Windows Server 2016/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-242">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="bbd9d-243">Estrutura de destino: não aplicável a implantações de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-243">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="bbd9d-244">IIS (em processo)</span><span class="sxs-lookup"><span data-stu-id="bbd9d-244">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="bbd9d-245">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-245">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="bbd9d-246">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="bbd9d-247">IIS (fora do processo)</span><span class="sxs-lookup"><span data-stu-id="bbd9d-247">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="bbd9d-248">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-248">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="bbd9d-249">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-249">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="bbd9d-250">Estrutura de destino: não aplicável a implantações IIS fora de processo.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-250">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="bbd9d-251">&dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-251">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="bbd9d-252">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-252">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="bbd9d-253">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-253">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="bbd9d-254">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="bbd9d-254">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="bbd9d-255">Windows Server 2016/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-255">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="bbd9d-256">Estrutura de destino: não aplicável a implantações de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-256">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="bbd9d-257">IIS (fora do processo)</span><span class="sxs-lookup"><span data-stu-id="bbd9d-257">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="bbd9d-258">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbd9d-258">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="bbd9d-259">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-259">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="bbd9d-260">Estrutura de destino: não aplicável a implantações IIS fora de processo.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-260">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="bbd9d-261">Uma conexão HTTP/2 precisa usar [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) e TLS 1.2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-261">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="bbd9d-262">Para obter mais informações, consulte os tópicos referentes aos seus cenários de implantação do servidor.</span><span class="sxs-lookup"><span data-stu-id="bbd9d-262">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bbd9d-263">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bbd9d-263">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
