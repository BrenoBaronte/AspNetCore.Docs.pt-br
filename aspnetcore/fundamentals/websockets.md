---
title: Suporte ao WebSockets no ASP.NET Core
author: rick-anderson
description: Saiba como começar a usar o WebSockets no ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 6edf2017cc889321cfb484e643b75711fd66004d
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058344"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="3177c-103">Suporte ao WebSockets no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3177c-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="3177c-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="3177c-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="3177c-105">Este artigo explica como começar a usar o WebSockets no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3177c-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="3177c-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) é um protocolo que permite canais de comunicação persistentes bidirecionais em conexões TCP.</span><span class="sxs-lookup"><span data-stu-id="3177c-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="3177c-107">Ele é usado em aplicativos que se beneficiam de comunicação rápida e em tempo real, como chat, painel e aplicativos de jogos.</span><span class="sxs-lookup"><span data-stu-id="3177c-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="3177c-108">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3177c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="3177c-109">[Como executar](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="3177c-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="3177c-110">[ASP.NET Core SignalR ](xref:signalr/introduction) é uma biblioteca que simplifica a adição de funcionalidades da Web em tempo real a aplicativos.</span><span class="sxs-lookup"><span data-stu-id="3177c-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="3177c-111">Ele usa WebSockets sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="3177c-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="3177c-112">Para a maioria dos aplicativos, recomendamos SignalR sobre WebSockets brutos.</span><span class="sxs-lookup"><span data-stu-id="3177c-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="3177c-113">SignalR fornece fallback de transporte para ambientes em que o WebSockets não está disponível.</span><span class="sxs-lookup"><span data-stu-id="3177c-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="3177c-114">Ele também fornece um modelo básico de aplicativo de chamada de procedimento remoto.</span><span class="sxs-lookup"><span data-stu-id="3177c-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="3177c-115">E na maioria dos cenários, SignalR não há uma desvantagem significativa no desempenho em comparação com o uso de WebSockets brutos.</span><span class="sxs-lookup"><span data-stu-id="3177c-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="3177c-116">Para alguns aplicativos, o [gRPC no .net](xref:grpc/index) fornece uma alternativa para WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3177c-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3177c-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="3177c-117">Prerequisites</span></span>

* <span data-ttu-id="3177c-118">Qualquer sistema operacional compatível com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3177c-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="3177c-119">Windows 7/Windows Server 2008 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3177c-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="3177c-120">Linux</span><span class="sxs-lookup"><span data-stu-id="3177c-120">Linux</span></span>
  * <span data-ttu-id="3177c-121">macOS</span><span class="sxs-lookup"><span data-stu-id="3177c-121">macOS</span></span>  
* <span data-ttu-id="3177c-122">Se o aplicativo é executado no Windows com o IIS:</span><span class="sxs-lookup"><span data-stu-id="3177c-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="3177c-123">Windows 8/Windows Server 2012 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3177c-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="3177c-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="3177c-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="3177c-125">Os WebSockets devem ser habilitados.</span><span class="sxs-lookup"><span data-stu-id="3177c-125">WebSockets must be enabled.</span></span> <span data-ttu-id="3177c-126">Consulte a seção [suporte do IIS/IIS Express](#iisiis-express-support) .</span><span class="sxs-lookup"><span data-stu-id="3177c-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="3177c-127">Se o aplicativo é executado no [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="3177c-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="3177c-128">Windows 8/Windows Server 2012 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3177c-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="3177c-129">Para saber quais são os navegadores compatíveis, confira https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="3177c-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="3177c-130">Configurar o middleware</span><span class="sxs-lookup"><span data-stu-id="3177c-130">Configure the middleware</span></span>

<span data-ttu-id="3177c-131">Adicione o middleware do WebSockets no método `Configure` da classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="3177c-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3177c-132">As seguintes configurações podem ser definidas:</span><span class="sxs-lookup"><span data-stu-id="3177c-132">The following settings can be configured:</span></span>

* <span data-ttu-id="3177c-133">`KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="3177c-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="3177c-134">O padrão é dois minutos.</span><span class="sxs-lookup"><span data-stu-id="3177c-134">The default is two minutes.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3177c-135">As seguintes configurações podem ser definidas:</span><span class="sxs-lookup"><span data-stu-id="3177c-135">The following settings can be configured:</span></span>

* <span data-ttu-id="3177c-136">`KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="3177c-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="3177c-137">O padrão é dois minutos.</span><span class="sxs-lookup"><span data-stu-id="3177c-137">The default is two minutes.</span></span>
* <span data-ttu-id="3177c-138">`AllowedOrigins` – Uma lista de valores de cabeçalho de origem permitidos para solicitações do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3177c-138">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="3177c-139">Por padrão, todas as origens são permitidas.</span><span class="sxs-lookup"><span data-stu-id="3177c-139">By default, all origins are allowed.</span></span> <span data-ttu-id="3177c-140">Consulte "Restrição de origem do WebSocket" abaixo para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="3177c-140">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="3177c-141">Aceitar solicitações do WebSocket</span><span class="sxs-lookup"><span data-stu-id="3177c-141">Accept WebSocket requests</span></span>

<span data-ttu-id="3177c-142">Futuramente no ciclo de vida da solicitação (mais tarde no método `Configure` ou em um método de ação, por exemplo), verifique se é uma solicitação do WebSocket e aceite-a.</span><span class="sxs-lookup"><span data-stu-id="3177c-142">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="3177c-143">O exemplo a seguir é de uma fase posterior do método `Configure`:</span><span class="sxs-lookup"><span data-stu-id="3177c-143">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="3177c-144">Uma solicitação do WebSocket pode entrar em qualquer URL, mas esse código de exemplo aceita apenas solicitações de `/ws`.</span><span class="sxs-lookup"><span data-stu-id="3177c-144">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="3177c-145">Ao usar um WebSocket, você **deve** manter o pipeline de middleware em execução durante a conexão.</span><span class="sxs-lookup"><span data-stu-id="3177c-145">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="3177c-146">Se tentar enviar ou receber uma mensagem do WebSocket após o término do pipeline de middleware, você poderá receber uma exceção semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="3177c-146">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="3177c-147">Se você estiver usando um serviço em segundo plano para gravar dados em um WebSocket, mantenha o pipeline do middleware em execução.</span><span class="sxs-lookup"><span data-stu-id="3177c-147">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="3177c-148">Faça isso usando um <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="3177c-148">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="3177c-149">Passe o `TaskCompletionSource` ao seu serviço em segundo plano e faça-o chamar <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> quando terminar com o WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3177c-149">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="3177c-150">Em seguida `await` a propriedade <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante a solicitação, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3177c-150">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="3177c-151">A exceção de WebSocket Closed também pode ocorrer ao retornar muito cedo de um método de ação.</span><span class="sxs-lookup"><span data-stu-id="3177c-151">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="3177c-152">Ao aceitar um soquete em um método de ação, aguarde o código que usa o soquete para ser concluído antes de retornar do método de ação.</span><span class="sxs-lookup"><span data-stu-id="3177c-152">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="3177c-153">Nunca use `Task.Wait`, `Task.Result` ou chamadas de bloqueio semelhantes para aguardar a conclusão do soquete, pois isso pode causar sérios problemas de threading.</span><span class="sxs-lookup"><span data-stu-id="3177c-153">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="3177c-154">Sempre use `await`.</span><span class="sxs-lookup"><span data-stu-id="3177c-154">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="3177c-155">Enviar e receber mensagens</span><span class="sxs-lookup"><span data-stu-id="3177c-155">Send and receive messages</span></span>

<span data-ttu-id="3177c-156">O método `AcceptWebSocketAsync` atualiza a conexão TCP para uma conexão WebSocket e fornece um objeto [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="3177c-156">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="3177c-157">Use o objeto `WebSocket` para enviar e receber mensagens.</span><span class="sxs-lookup"><span data-stu-id="3177c-157">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="3177c-158">O código mostrado anteriormente que aceita a solicitação do WebSocket passa o objeto `WebSocket` para um método `Echo`.</span><span class="sxs-lookup"><span data-stu-id="3177c-158">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="3177c-159">O código recebe uma mensagem e envia de volta imediatamente a mesma mensagem.</span><span class="sxs-lookup"><span data-stu-id="3177c-159">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="3177c-160">As mensagens são enviadas e recebidas em um loop até que o cliente feche a conexão:</span><span class="sxs-lookup"><span data-stu-id="3177c-160">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="3177c-161">Ao aceitar a conexão WebSocket antes de iniciar o loop, o pipeline de middleware é encerrado.</span><span class="sxs-lookup"><span data-stu-id="3177c-161">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="3177c-162">Ao fechar o soquete, o pipeline é desenrolado.</span><span class="sxs-lookup"><span data-stu-id="3177c-162">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="3177c-163">Ou seja, a solicitação deixa de avançar no pipeline quando o WebSocket é aceito.</span><span class="sxs-lookup"><span data-stu-id="3177c-163">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="3177c-164">Quando o loop é concluído e o soquete é fechado, a solicitação continua a avançar no pipeline.</span><span class="sxs-lookup"><span data-stu-id="3177c-164">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="3177c-165">Tratar desconexões de cliente</span><span class="sxs-lookup"><span data-stu-id="3177c-165">Handle client disconnects</span></span>

<span data-ttu-id="3177c-166">O servidor não é informado automaticamente quando o cliente se desconecta devido à perda de conectividade.</span><span class="sxs-lookup"><span data-stu-id="3177c-166">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="3177c-167">O servidor recebe uma mensagem de desconexão apenas quando o cliente a envia, e isso é possível somente quando há conexão com a Internet.</span><span class="sxs-lookup"><span data-stu-id="3177c-167">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="3177c-168">Se quiser tomar alguma medida quando isso ocorrer, defina um tempo limite, caso não receba nada do cliente após uma determinada janela de tempo.</span><span class="sxs-lookup"><span data-stu-id="3177c-168">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="3177c-169">Se o cliente não envia mensagens com frequência, e você prefere não definir um tempo limite apenas porque a conexão fica ociosa, peça ao cliente para usar um temporizador a fim de enviar uma mensagem de ping a cada X segundos.</span><span class="sxs-lookup"><span data-stu-id="3177c-169">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="3177c-170">No servidor, se uma mensagem não chegar dentro de 2\*X segundos após a mensagem anterior, encerre a conexão e informe que o cliente se desconectou.</span><span class="sxs-lookup"><span data-stu-id="3177c-170">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="3177c-171">Aguarde o dobro do intervalo de tempo esperado a fim de deixar um tempo extra para atrasos na rede, que possam atrasar a mensagem de ping.</span><span class="sxs-lookup"><span data-stu-id="3177c-171">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="3177c-172">Restrição de origem do WebSocket</span><span class="sxs-lookup"><span data-stu-id="3177c-172">WebSocket origin restriction</span></span>

<span data-ttu-id="3177c-173">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3177c-173">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="3177c-174">Navegadores **não**:</span><span class="sxs-lookup"><span data-stu-id="3177c-174">Browsers do **not**:</span></span>

* <span data-ttu-id="3177c-175">Executam solicitações de simulação de CORS.</span><span class="sxs-lookup"><span data-stu-id="3177c-175">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="3177c-176">Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3177c-176">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="3177c-177">No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3177c-177">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="3177c-178">Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.</span><span class="sxs-lookup"><span data-stu-id="3177c-178">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="3177c-179">Se você estiver hospedando o servidor em "https://server.com" e hospedando seu cliente em "https://client.com", adicione "https://client.com" à lista `AllowedOrigins` para o WebSockets verificar.</span><span class="sxs-lookup"><span data-stu-id="3177c-179">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="3177c-180">O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado.</span><span class="sxs-lookup"><span data-stu-id="3177c-180">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="3177c-181">**Não** use esses cabeçalhos como um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="3177c-181">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="3177c-182">Suporte ao IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="3177c-182">IIS/IIS Express support</span></span>

<span data-ttu-id="3177c-183">O Windows Server 2012 ou posterior e o Windows 8 ou posterior com o IIS/IIS Express 8 ou posterior são compatíveis com o protocolo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3177c-183">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="3177c-184">WebSockets estão sempre habilitados ao usar o IIS Express.</span><span class="sxs-lookup"><span data-stu-id="3177c-184">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="3177c-185">Habilitar WebSockets no IIS</span><span class="sxs-lookup"><span data-stu-id="3177c-185">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="3177c-186">Para habilitar o suporte para o protocolo WebSocket no Windows Server 2012 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="3177c-186">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="3177c-187">Estas etapas não são necessárias ao usar o IIS Express</span><span class="sxs-lookup"><span data-stu-id="3177c-187">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="3177c-188">Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**.</span><span class="sxs-lookup"><span data-stu-id="3177c-188">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="3177c-189">Selecione **Instalação baseada em função ou em recurso**.</span><span class="sxs-lookup"><span data-stu-id="3177c-189">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="3177c-190">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3177c-190">Select **Next**.</span></span>
1. <span data-ttu-id="3177c-191">Selecione o servidor apropriado (o servidor local é selecionado por padrão).</span><span class="sxs-lookup"><span data-stu-id="3177c-191">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="3177c-192">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3177c-192">Select **Next**.</span></span>
1. <span data-ttu-id="3177c-193">Expanda **Servidor Web (IIS)** na árvore **Funções**, expanda **Servidor Web** e, em seguida, expanda **Desenvolvimento de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="3177c-193">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="3177c-194">Selecione o **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="3177c-194">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="3177c-195">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3177c-195">Select **Next**.</span></span>
1. <span data-ttu-id="3177c-196">Se não forem necessários recursos adicionais, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3177c-196">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="3177c-197">Selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="3177c-197">Select **Install**.</span></span>
1. <span data-ttu-id="3177c-198">Quando a instalação for concluída, selecione **Fechar** para sair do assistente.</span><span class="sxs-lookup"><span data-stu-id="3177c-198">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="3177c-199">Para habilitar o suporte para o protocolo WebSocket no Windows 8 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="3177c-199">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="3177c-200">Estas etapas não são necessárias ao usar o IIS Express</span><span class="sxs-lookup"><span data-stu-id="3177c-200">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="3177c-201">Navegue até o **painel de controle**  >  **programas**  >  **programas e recursos**  >  **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="3177c-201">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3177c-202">Abra os seguintes nós: **serviços de informações da Internet** os  >  recursos de desenvolvimento de aplicativos **dos serviços World Wide Webs**  >  .</span><span class="sxs-lookup"><span data-stu-id="3177c-202">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="3177c-203">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="3177c-203">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="3177c-204">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="3177c-204">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="3177c-205">Desabilite o WebSocket ao usar o socket.io no Node.js</span><span class="sxs-lookup"><span data-stu-id="3177c-205">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="3177c-206">Se estiver usando o suporte WebSocket no [Socket.Io](https://socket.io/) no [Node.js](https://nodejs.org/), desabilite o módulo WebSocket padrão do IIS usando o `webSocket` elemento em *web.config* ou *applicationHost.config*. Se essa etapa não for executada, o módulo WebSocket do IIS tentará lidar com a comunicação WebSocket em vez de Node.js e o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3177c-206">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="3177c-207">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="3177c-207">Sample app</span></span>

<span data-ttu-id="3177c-208">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) que acompanha este artigo é um aplicativo de eco.</span><span class="sxs-lookup"><span data-stu-id="3177c-208">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="3177c-209">Ele tem uma página da Web que faz conexões WebSocket e o servidor reenvia todas as mensagens recebidas de volta para o cliente.</span><span class="sxs-lookup"><span data-stu-id="3177c-209">It has a webpage that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="3177c-210">O aplicativo de exemplo não está configurado para ser executado no Visual Studio com IIS Express, portanto, execute o aplicativo em um shell de comando com [`dotnet run`](/dotnet/core/tools/dotnet-run) e navegue em um navegador para `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="3177c-210">The sample app isn't configured to run from Visual Studio with IIS Express, so run the app in a command shell with [`dotnet run`](/dotnet/core/tools/dotnet-run) and navigate in a browser to `http://localhost:5000`.</span></span> <span data-ttu-id="3177c-211">A página da Web mostra o status da conexão:</span><span class="sxs-lookup"><span data-stu-id="3177c-211">The webpage shows the connection status:</span></span>

![Estado inicial da página da Web antes da conexão WebSockets](websockets/_static/start.png)

<span data-ttu-id="3177c-213">Selecione **Conectar** para enviar uma solicitação WebSocket para a URL exibida.</span><span class="sxs-lookup"><span data-stu-id="3177c-213">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="3177c-214">Insira uma mensagem de teste e selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="3177c-214">Enter a test message and select **Send**.</span></span> <span data-ttu-id="3177c-215">Quando terminar, selecione **Fechar Soquete**.</span><span class="sxs-lookup"><span data-stu-id="3177c-215">When done, select **Close Socket**.</span></span> <span data-ttu-id="3177c-216">A seção **Log de Comunicação** relata cada ação de abertura, envio e fechamento conforme ela ocorre.</span><span class="sxs-lookup"><span data-stu-id="3177c-216">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Estado final da página da Web após a conexão WebSockets e as mensagens de teste serem enviadas e recebidas](websockets/_static/end.png)
