---
title: Solicitação de descarga com o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba mais sobre a solicitação de descarga com o Kestrel, o servidor Web multiplataforma para ASP.NET Core.
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253913"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="6d817-103">Solicitação de descarga com o servidor Web ASP.NET Core Kestrel</span><span class="sxs-lookup"><span data-stu-id="6d817-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="6d817-104">A abertura de conexões HTTP é demorada.</span><span class="sxs-lookup"><span data-stu-id="6d817-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="6d817-105">Para HTTPS, ele também consome muitos recursos.</span><span class="sxs-lookup"><span data-stu-id="6d817-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="6d817-106">Portanto, o Kestrel tenta reutilizar conexões de acordo com o protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6d817-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="6d817-107">Um corpo de solicitação deve ser totalmente consumido para permitir que a conexão seja reutilizada.</span><span class="sxs-lookup"><span data-stu-id="6d817-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="6d817-108">O aplicativo nem sempre consome o corpo da solicitação, como solicitações HTTP POST, em que o servidor retorna uma resposta redirecionada ou 404.</span><span class="sxs-lookup"><span data-stu-id="6d817-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="6d817-109">No caso de redirecionamento HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="6d817-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="6d817-110">O cliente pode já ter enviado parte dos dados de POSTAgem.</span><span class="sxs-lookup"><span data-stu-id="6d817-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="6d817-111">O servidor grava a resposta 301.</span><span class="sxs-lookup"><span data-stu-id="6d817-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="6d817-112">A conexão não pode ser usada para uma nova solicitação até que os dados de POSTAgem do corpo da solicitação anterior tenham sido totalmente lidos.</span><span class="sxs-lookup"><span data-stu-id="6d817-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="6d817-113">Kestrel tenta drenar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="6d817-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="6d817-114">Drenar o corpo da solicitação significa ler e descartar os dados sem processá-los.</span><span class="sxs-lookup"><span data-stu-id="6d817-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="6d817-115">O processo de descarga faz uma compensação entre permitir que a conexão seja reutilizada e o tempo necessário para drenar os dados restantes:</span><span class="sxs-lookup"><span data-stu-id="6d817-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="6d817-116">O descarregamento tem um tempo limite de cinco segundos, o que não é configurável.</span><span class="sxs-lookup"><span data-stu-id="6d817-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="6d817-117">Se todos os dados especificados pelo `Content-Length` `Transfer-Encoding` cabeçalho ou não tiverem sido lidos antes do tempo limite, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6d817-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="6d817-118">Às vezes, você pode querer encerrar a solicitação imediatamente, antes ou depois de gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="6d817-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="6d817-119">Por exemplo, os clientes podem ter limites de dados restritivos.</span><span class="sxs-lookup"><span data-stu-id="6d817-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="6d817-120">Limitar os dados carregados pode ser uma prioridade.</span><span class="sxs-lookup"><span data-stu-id="6d817-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="6d817-121">Nesses casos, para encerrar uma solicitação, chame [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) de um controlador, Razor página ou middleware.</span><span class="sxs-lookup"><span data-stu-id="6d817-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="6d817-122">Há limitações para chamar `Abort` :</span><span class="sxs-lookup"><span data-stu-id="6d817-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="6d817-123">A criação de novas conexões pode ser lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="6d817-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="6d817-124">Não há nenhuma garantia de que o cliente leu a resposta antes que a conexão seja fechada.</span><span class="sxs-lookup"><span data-stu-id="6d817-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="6d817-125">`Abort`A chamada deve ser rara e reservada para casos de erro graves, não erros comuns.</span><span class="sxs-lookup"><span data-stu-id="6d817-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="6d817-126">Só chame `Abort` quando um problema específico precisar ser resolvido.</span><span class="sxs-lookup"><span data-stu-id="6d817-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="6d817-127">Por exemplo, chame `Abort` se clientes mal-intencionados estão tentando postar dados ou quando há um bug no código de cliente que causa grandes ou várias solicitações.</span><span class="sxs-lookup"><span data-stu-id="6d817-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="6d817-128">Não chame `Abort` situações de erro comuns, como HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="6d817-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="6d817-129">Chamar [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes `Abort` de chamar garante que o servidor concluiu a gravação da resposta.</span><span class="sxs-lookup"><span data-stu-id="6d817-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="6d817-130">No entanto, o comportamento do cliente não é previsível e pode não ler a resposta antes que a conexão seja anulada.</span><span class="sxs-lookup"><span data-stu-id="6d817-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="6d817-131">Esse processo é diferente para HTTP/2 porque o protocolo dá suporte à anulação de fluxos de solicitação individuais sem fechar a conexão.</span><span class="sxs-lookup"><span data-stu-id="6d817-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="6d817-132">O tempo limite de descarga de cinco segundos não se aplica.</span><span class="sxs-lookup"><span data-stu-id="6d817-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="6d817-133">Se houver algum dado de corpo de solicitação não lido depois de concluir uma resposta, o servidor enviará um quadro RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6d817-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="6d817-134">Quadros de dados de corpo de solicitação adicionais são ignorados.</span><span class="sxs-lookup"><span data-stu-id="6d817-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="6d817-135">Se possível, é melhor para os clientes usarem o cabeçalho de solicitação [esperado: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e aguarde até que o servidor responda antes de começar a enviar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="6d817-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="6d817-136">Isso dá ao cliente uma oportunidade de examinar a resposta e anular antes de enviar dados desnecessários.</span><span class="sxs-lookup"><span data-stu-id="6d817-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
