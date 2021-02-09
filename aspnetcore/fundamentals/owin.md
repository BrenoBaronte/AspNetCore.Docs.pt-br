---
title: OWIN (Open Web Interface para .NET) com o ASP.NET Core
author: ardalis
description: Descubra como o ASP.NET Core dá suporte ao OWIN (Open Web Interface para .NET), que permite que aplicativos Web sejam desacoplados de servidores Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
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
uid: fundamentals/owin
ms.openlocfilehash: c44803c6f67d8e03759a01f7aa71d82088be9a11
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975268"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="6d9d6-103">OWIN (Open Web Interface para .NET) com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d9d6-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="6d9d6-104">Por [Steve Smith](https://ardalis.com/) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6d9d6-105">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6d9d6-105">ASP.NET Core:</span></span>

* <span data-ttu-id="6d9d6-106">Dá suporte à interface Web aberta para .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="6d9d6-106">Supports the Open Web Interface for .NET (OWIN).</span></span>
* <span data-ttu-id="6d9d6-107">Tem substituições compatíveis com o .NET Core para as `Microsoft.Owin.*` bibliotecas ([Katana](/aspnet/aspnet/overview/owin-and-katana/)).</span><span class="sxs-lookup"><span data-stu-id="6d9d6-107">Has .NET Core compatible replacements for the `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) libraries.</span></span>

<span data-ttu-id="6d9d6-108">O OWIN permite que os aplicativos Web sejam separados dos servidores Web.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-108">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="6d9d6-109">Ele define uma maneira padrão de usar o middleware em um pipeline para manipular solicitações e respostas associadas.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-109">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="6d9d6-110">O middleware e os aplicativos ASP.NET Core podem interoperar com aplicativos, servidores e middleware baseados no OWIN.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-110">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="6d9d6-111">O OWIN fornece uma camada de desacoplamento que permite duas estruturas com modelos de objeto diferentes para ser usadas juntas.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-111">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="6d9d6-112">O pacote `Microsoft.AspNetCore.Owin` fornece duas implementações de adaptador:</span><span class="sxs-lookup"><span data-stu-id="6d9d6-112">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="6d9d6-113">ASP.NET Core para OWIN</span><span class="sxs-lookup"><span data-stu-id="6d9d6-113">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="6d9d6-114">OWIN para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d9d6-114">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="6d9d6-115">Isso permite que o ASP.NET Core seja hospedado em um servidor/host compatível com OWIN ou que outros componentes compatíveis com OWIN sejam executados no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-115">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="6d9d6-116">O uso desses adaptadores implica um custo de desempenho.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-116">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="6d9d6-117">Os aplicativos que usam somente componentes do ASP.NET Core não devem usar o pacote `Microsoft.AspNetCore.Owin` ou os adaptadores.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-117">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="6d9d6-118">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6d9d6-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="6d9d6-119">Executando o middleware do OWIN no pipeline do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d9d6-119">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="6d9d6-120">O suporte ao OWIN do ASP.NET Core é implantado como parte do pacote `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-120">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="6d9d6-121">Importe o suporte ao OWIN para seu projeto instalando esse pacote.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-121">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="6d9d6-122">O middleware do OWIN está em conformidade com a [especificação do OWIN](https://owin.org/spec/spec/owin-1.0.0.html), que exige uma interface `Func<IDictionary<string, object>, Task>` e a definição de chaves específicas (como `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="6d9d6-122">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="6d9d6-123">O seguinte middleware simples do OWIN exibe “Olá, Mundo”:</span><span class="sxs-lookup"><span data-stu-id="6d9d6-123">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="6d9d6-124">A assinatura de exemplo retorna uma `Task` e aceita uma `IDictionary<string, object>`, conforme solicitado pelo OWIN.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-124">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="6d9d6-125">O código a seguir mostra como adicionar o middleware `OwinHello` (mostrado acima) ao pipeline do ASP.NET Core com o método de extensão `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-125">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="6d9d6-126">Configure outras ações a serem executadas no pipeline do OWIN.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-126">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="6d9d6-127">Os cabeçalhos de resposta devem ser modificados apenas antes da primeira gravação no fluxo de resposta.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-127">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="6d9d6-128">Não é recomendado fazer várias chamadas a `UseOwin` por motivos de desempenho.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-128">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="6d9d6-129">Os componentes do OWIN funcionarão melhor se forem agrupados.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-129">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="6d9d6-130">Executar o ASP.NET Core em um servidor baseado no OWIN e usar seu suporte do WebSockets</span><span class="sxs-lookup"><span data-stu-id="6d9d6-130">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="6d9d6-131">Outro exemplo de como os recursos dos servidores baseados no OWIN podem ser aproveitados pelo ASP.NET Core é o acesso a recursos como o WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-131">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="6d9d6-132">O servidor Web do OWIN no .NET usado no exemplo anterior tem suporte para Soquetes da Web internos, que podem ser aproveitados por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-132">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="6d9d6-133">O exemplo abaixo mostra um aplicativo Web simples que dá suporte a Soquetes da Web e repete tudo o que foi enviado ao servidor por meio do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-133">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="6d9d6-134">Ambiente do OWIN</span><span class="sxs-lookup"><span data-stu-id="6d9d6-134">OWIN environment</span></span>

<span data-ttu-id="6d9d6-135">Você pode construir um ambiente do OWIN usando o `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-135">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="6d9d6-136">Chaves do OWIN</span><span class="sxs-lookup"><span data-stu-id="6d9d6-136">OWIN keys</span></span>

<span data-ttu-id="6d9d6-137">O OWIN depende de um objeto `IDictionary<string,object>` para transmitir informações em uma troca de Solicitação/Resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-137">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="6d9d6-138">O ASP.NET Core implementa as chaves listadas abaixo.</span><span class="sxs-lookup"><span data-stu-id="6d9d6-138">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="6d9d6-139">Consulte a [especificação primária, extensões](https://owin.org/#spec) e as [Diretrizes de chaves do OWIN e chaves comuns](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="6d9d6-139">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="6d9d6-140">Dados de solicitação (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-140">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="6d9d6-141">Chave</span><span class="sxs-lookup"><span data-stu-id="6d9d6-141">Key</span></span>               | <span data-ttu-id="6d9d6-142">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-142">Value (type)</span></span> | <span data-ttu-id="6d9d6-143">Descrição</span><span class="sxs-lookup"><span data-stu-id="6d9d6-143">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="6d9d6-144">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="6d9d6-144">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="6d9d6-145">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="6d9d6-145">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="6d9d6-146">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="6d9d6-146">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="6d9d6-147">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="6d9d6-147">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="6d9d6-148">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="6d9d6-148">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="6d9d6-149">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="6d9d6-149">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="6d9d6-150">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="6d9d6-150">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="6d9d6-151">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="6d9d6-151">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="6d9d6-152">Dados de solicitação (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-152">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="6d9d6-153">Chave</span><span class="sxs-lookup"><span data-stu-id="6d9d6-153">Key</span></span>               | <span data-ttu-id="6d9d6-154">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-154">Value (type)</span></span> | <span data-ttu-id="6d9d6-155">Descrição</span><span class="sxs-lookup"><span data-stu-id="6d9d6-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="6d9d6-156">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="6d9d6-156">owin.RequestId</span></span> | `String` | <span data-ttu-id="6d9d6-157">Opcional</span><span class="sxs-lookup"><span data-stu-id="6d9d6-157">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="6d9d6-158">Dados de resposta (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-158">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="6d9d6-159">Chave</span><span class="sxs-lookup"><span data-stu-id="6d9d6-159">Key</span></span>               | <span data-ttu-id="6d9d6-160">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-160">Value (type)</span></span> | <span data-ttu-id="6d9d6-161">Descrição</span><span class="sxs-lookup"><span data-stu-id="6d9d6-161">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="6d9d6-162">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="6d9d6-162">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="6d9d6-163">Opcional</span><span class="sxs-lookup"><span data-stu-id="6d9d6-163">Optional</span></span> |
| <span data-ttu-id="6d9d6-164">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="6d9d6-164">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="6d9d6-165">Opcional</span><span class="sxs-lookup"><span data-stu-id="6d9d6-165">Optional</span></span> |
| <span data-ttu-id="6d9d6-166">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="6d9d6-166">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="6d9d6-167">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="6d9d6-167">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="6d9d6-168">Outros dados (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-168">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="6d9d6-169">Chave</span><span class="sxs-lookup"><span data-stu-id="6d9d6-169">Key</span></span>               | <span data-ttu-id="6d9d6-170">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-170">Value (type)</span></span> | <span data-ttu-id="6d9d6-171">Descrição</span><span class="sxs-lookup"><span data-stu-id="6d9d6-171">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="6d9d6-172">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="6d9d6-172">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="6d9d6-173">owin.Version</span><span class="sxs-lookup"><span data-stu-id="6d9d6-173">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="6d9d6-174">Chaves comuns</span><span class="sxs-lookup"><span data-stu-id="6d9d6-174">Common keys</span></span>

| <span data-ttu-id="6d9d6-175">Chave</span><span class="sxs-lookup"><span data-stu-id="6d9d6-175">Key</span></span>               | <span data-ttu-id="6d9d6-176">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-176">Value (type)</span></span> | <span data-ttu-id="6d9d6-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="6d9d6-177">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="6d9d6-178">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="6d9d6-178">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="6d9d6-179">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="6d9d6-179">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="6d9d6-180">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="6d9d6-180">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="6d9d6-181">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="6d9d6-181">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="6d9d6-182">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="6d9d6-182">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="6d9d6-183">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="6d9d6-183">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="6d9d6-184">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="6d9d6-184">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="6d9d6-185">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="6d9d6-185">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="6d9d6-186">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="6d9d6-186">SendFiles v0.3.0</span></span>

| <span data-ttu-id="6d9d6-187">Chave</span><span class="sxs-lookup"><span data-stu-id="6d9d6-187">Key</span></span>               | <span data-ttu-id="6d9d6-188">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-188">Value (type)</span></span> | <span data-ttu-id="6d9d6-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="6d9d6-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="6d9d6-190">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="6d9d6-190">sendfile.SendAsync</span></span> | <span data-ttu-id="6d9d6-191">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-191">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="6d9d6-192">Por solicitação</span><span class="sxs-lookup"><span data-stu-id="6d9d6-192">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="6d9d6-193">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="6d9d6-193">Opaque v0.3.0</span></span>

| <span data-ttu-id="6d9d6-194">Chave</span><span class="sxs-lookup"><span data-stu-id="6d9d6-194">Key</span></span>               | <span data-ttu-id="6d9d6-195">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-195">Value (type)</span></span> | <span data-ttu-id="6d9d6-196">Descrição</span><span class="sxs-lookup"><span data-stu-id="6d9d6-196">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="6d9d6-197">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="6d9d6-197">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="6d9d6-198">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="6d9d6-198">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="6d9d6-199">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-199">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="6d9d6-200">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="6d9d6-200">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="6d9d6-201">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="6d9d6-201">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="6d9d6-202">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="6d9d6-202">WebSocket v0.3.0</span></span>

| <span data-ttu-id="6d9d6-203">Chave</span><span class="sxs-lookup"><span data-stu-id="6d9d6-203">Key</span></span>               | <span data-ttu-id="6d9d6-204">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-204">Value (type)</span></span> | <span data-ttu-id="6d9d6-205">Descrição</span><span class="sxs-lookup"><span data-stu-id="6d9d6-205">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="6d9d6-206">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="6d9d6-206">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="6d9d6-207">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="6d9d6-207">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="6d9d6-208">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-208">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="6d9d6-209">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="6d9d6-209">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="6d9d6-210">Sem especificação</span><span class="sxs-lookup"><span data-stu-id="6d9d6-210">Non-spec</span></span> |
| <span data-ttu-id="6d9d6-211">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="6d9d6-211">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="6d9d6-212">Consulte a etapa 5.5 [RFC6455 Seção 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-212">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="6d9d6-213">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="6d9d6-213">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="6d9d6-214">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="6d9d6-215">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="6d9d6-215">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="6d9d6-216">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="6d9d6-217">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="6d9d6-217">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="6d9d6-218">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="6d9d6-218">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="6d9d6-219">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="6d9d6-219">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="6d9d6-220">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="6d9d6-220">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="6d9d6-221">Opcional</span><span class="sxs-lookup"><span data-stu-id="6d9d6-221">Optional</span></span> |
| <span data-ttu-id="6d9d6-222">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="6d9d6-222">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="6d9d6-223">Opcional</span><span class="sxs-lookup"><span data-stu-id="6d9d6-223">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="6d9d6-224">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6d9d6-224">Additional resources</span></span>

* [<span data-ttu-id="6d9d6-225">Middleware</span><span class="sxs-lookup"><span data-stu-id="6d9d6-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="6d9d6-226">Servidores</span><span class="sxs-lookup"><span data-stu-id="6d9d6-226">Servers</span></span>](xref:fundamentals/servers/index)
