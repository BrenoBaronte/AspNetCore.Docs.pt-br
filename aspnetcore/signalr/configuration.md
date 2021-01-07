---
title: Configuração de ASP.NET Core SignalR
author: bradygaster
description: Saiba como configurar os SignalR aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: cef05b32731e0930d7a3cfc6fe4502236b07a236
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972061"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="d5c39-103">Configuração de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d5c39-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5c39-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-105">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5c39-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d5c39-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5c39-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5c39-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d5c39-108">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5c39-109">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d5c39-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5c39-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d5c39-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5c39-111">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d5c39-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d5c39-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d5c39-113">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5c39-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5c39-114">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d5c39-116">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="d5c39-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d5c39-117">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d5c39-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5c39-118">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-118">MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-119">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5c39-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5c39-120">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-121">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5c39-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d5c39-122">Configure server options</span></span>

<span data-ttu-id="d5c39-123">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d5c39-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5c39-124">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-124">Option</span></span> | <span data-ttu-id="d5c39-125">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-125">Default Value</span></span> | <span data-ttu-id="d5c39-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5c39-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-127">30 seconds</span></span> | <span data-ttu-id="d5c39-128">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5c39-129">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5c39-130">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5c39-131">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-131">15 seconds</span></span> | <span data-ttu-id="d5c39-132">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5c39-133">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-134">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-135">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-135">15 seconds</span></span> | <span data-ttu-id="d5c39-136">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d5c39-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5c39-137">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5c39-138">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5c39-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5c39-139">All installed protocols</span></span> | <span data-ttu-id="d5c39-140">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-140">Protocols supported by this hub.</span></span> <span data-ttu-id="d5c39-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5c39-142">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5c39-143">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d5c39-144">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d5c39-145">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d5c39-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-146">32 KB</span></span> | <span data-ttu-id="d5c39-147">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="d5c39-148">1</span><span class="sxs-lookup"><span data-stu-id="d5c39-148">1</span></span> | <span data-ttu-id="d5c39-149">O número máximo de métodos de Hub que cada cliente pode chamar em paralelo antes de enfileirar.</span><span class="sxs-lookup"><span data-stu-id="d5c39-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="d5c39-150">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="d5c39-151">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5c39-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5c39-152">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d5c39-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5c39-153">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5c39-154">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="d5c39-155">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5c39-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5c39-156">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-156">Option</span></span> | <span data-ttu-id="d5c39-157">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-157">Default Value</span></span> | <span data-ttu-id="d5c39-158">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5c39-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-159">32 KB</span></span> | <span data-ttu-id="d5c39-160">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d5c39-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d5c39-161">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5c39-162">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5c39-163">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5c39-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-164">32 KB</span></span> | <span data-ttu-id="d5c39-165">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d5c39-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d5c39-166">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5c39-167">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-167">All Transports are enabled.</span></span> | <span data-ttu-id="d5c39-168">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d5c39-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5c39-169">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-169">See below.</span></span> | <span data-ttu-id="d5c39-170">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5c39-171">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-171">See below.</span></span> | <span data-ttu-id="d5c39-172">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d5c39-173">0</span><span class="sxs-lookup"><span data-stu-id="d5c39-173">0</span></span> | <span data-ttu-id="d5c39-174">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="d5c39-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d5c39-175">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d5c39-176">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5c39-177">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-177">Option</span></span> | <span data-ttu-id="d5c39-178">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-178">Default Value</span></span> | <span data-ttu-id="d5c39-179">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5c39-180">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-180">90 seconds</span></span> | <span data-ttu-id="d5c39-181">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5c39-182">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d5c39-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5c39-183">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5c39-184">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-184">Option</span></span> | <span data-ttu-id="d5c39-185">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-185">Default Value</span></span> | <span data-ttu-id="d5c39-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5c39-187">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-187">5 seconds</span></span> | <span data-ttu-id="d5c39-188">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5c39-189">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5c39-190">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5c39-191">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d5c39-191">Configure client options</span></span>

<span data-ttu-id="d5c39-192">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5c39-193">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d5c39-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5c39-194">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d5c39-194">Configure logging</span></span>

<span data-ttu-id="d5c39-195">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5c39-196">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5c39-197">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-198">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d5c39-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5c39-199">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5c39-200">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5c39-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5c39-201">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5c39-202">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d5c39-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5c39-203">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d5c39-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5c39-204">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d5c39-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d5c39-205">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d5c39-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d5c39-206">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d5c39-207">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d5c39-207">The following table lists the available log levels.</span></span> <span data-ttu-id="d5c39-208">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d5c39-209">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela** serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d5c39-210">Cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="d5c39-210">String</span></span>                      | <span data-ttu-id="d5c39-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d5c39-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d5c39-212">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d5c39-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d5c39-213">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d5c39-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d5c39-214">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5c39-215">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5c39-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5c39-216">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d5c39-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5c39-217">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d5c39-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5c39-218">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d5c39-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5c39-219">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d5c39-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5c39-220">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d5c39-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5c39-221">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5c39-221">Configure allowed transports</span></span>

<span data-ttu-id="d5c39-222">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5c39-223">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5c39-224">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-224">All transports are enabled by default.</span></span>

<span data-ttu-id="d5c39-225">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="d5c39-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5c39-226">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5c39-227">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d5c39-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d5c39-228">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d5c39-229">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-230">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d5c39-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5c39-231">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d5c39-231">Configure bearer authentication</span></span>

<span data-ttu-id="d5c39-232">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5c39-233">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5c39-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5c39-234">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5c39-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5c39-235">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5c39-236">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5c39-237">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="d5c39-238">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5c39-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5c39-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5c39-240">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5c39-241">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5c39-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5c39-242">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5c39-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-243">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-244">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-244">Option</span></span> | <span data-ttu-id="d5c39-245">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-245">Default value</span></span> | <span data-ttu-id="d5c39-246">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5c39-247">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-248">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-248">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-249">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-250">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-251">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5c39-252">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-252">15 seconds</span></span> | <span data-ttu-id="d5c39-253">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-254">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-255">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-256">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-257">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-257">15 seconds</span></span> | <span data-ttu-id="d5c39-258">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-259">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-260">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5c39-261">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d5c39-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5c39-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-263">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-263">Option</span></span> | <span data-ttu-id="d5c39-264">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-264">Default value</span></span> | <span data-ttu-id="d5c39-265">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5c39-266">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-267">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-267">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-268">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5c39-269">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-270">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5c39-271">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-272">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-273">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-274">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-275">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-275">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-276">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-276">Option</span></span> | <span data-ttu-id="d5c39-277">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-277">Default value</span></span> | <span data-ttu-id="d5c39-278">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5c39-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5c39-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5c39-280">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-281">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-281">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-282">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-283">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-284">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5c39-285">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-285">15 seconds</span></span> | <span data-ttu-id="d5c39-286">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-287">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-288">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-289">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5c39-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5c39-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5c39-291">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-292">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-293">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-294">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5c39-295">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-295">Configure additional options</span></span>

<span data-ttu-id="d5c39-296">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5c39-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-297">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-298">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-298">.NET Option</span></span> |  <span data-ttu-id="d5c39-299">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-299">Default value</span></span> | <span data-ttu-id="d5c39-300">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5c39-301">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5c39-302">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-303">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-304">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5c39-305">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-305">Empty</span></span> | <span data-ttu-id="d5c39-306">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5c39-307">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-307">Empty</span></span> | <span data-ttu-id="d5c39-308">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5c39-309">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-309">Empty</span></span> | <span data-ttu-id="d5c39-310">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5c39-311">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-311">5 seconds</span></span> | <span data-ttu-id="d5c39-312">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-312">WebSockets only.</span></span> <span data-ttu-id="d5c39-313">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5c39-314">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d5c39-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5c39-315">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-315">Empty</span></span> | <span data-ttu-id="d5c39-316">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5c39-317">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5c39-318">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5c39-319">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d5c39-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5c39-320">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d5c39-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5c39-321">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d5c39-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5c39-322">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5c39-323">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5c39-324">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d5c39-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5c39-325">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5c39-326">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d5c39-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5c39-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-328">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-328">JavaScript Option</span></span> | <span data-ttu-id="d5c39-329">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-329">Default Value</span></span> | <span data-ttu-id="d5c39-330">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5c39-331">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d5c39-332">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="d5c39-333">Dicionário de cabeçalhos enviado com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="d5c39-334">O envio de cabeçalhos no navegador não funciona para WebSockets ou <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> fluxo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5c39-335">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5c39-336">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-337">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-338">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="d5c39-339">Especifica se as credenciais serão enviadas com a solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="d5c39-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="d5c39-340">Azure App serviço usa cookie s para sessões adesivas e precisa que essa opção esteja habilitada para funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="d5c39-341">Para obter mais informações sobre o CORS com SignalR , consulte <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="d5c39-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-342">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-342">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-343">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-343">Java Option</span></span> | <span data-ttu-id="d5c39-344">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-344">Default Value</span></span> | <span data-ttu-id="d5c39-345">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5c39-346">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5c39-347">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-348">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-349">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5c39-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5c39-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5c39-351">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-351">Empty</span></span> | <span data-ttu-id="d5c39-352">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5c39-353">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5c39-354">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="d5c39-355">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5c39-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5c39-356">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5c39-357">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-358">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5c39-359">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d5c39-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5c39-360">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5c39-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d5c39-361">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5c39-362">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d5c39-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5c39-363">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d5c39-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5c39-364">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d5c39-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d5c39-365">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d5c39-366">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5c39-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5c39-367">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-368">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d5c39-369">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="d5c39-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d5c39-370">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d5c39-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5c39-371">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-371">MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-372">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5c39-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5c39-373">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-374">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5c39-375">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d5c39-375">Configure server options</span></span>

<span data-ttu-id="d5c39-376">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d5c39-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5c39-377">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-377">Option</span></span> | <span data-ttu-id="d5c39-378">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-378">Default Value</span></span> | <span data-ttu-id="d5c39-379">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5c39-380">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-380">30 seconds</span></span> | <span data-ttu-id="d5c39-381">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5c39-382">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5c39-383">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5c39-384">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-384">15 seconds</span></span> | <span data-ttu-id="d5c39-385">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5c39-386">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-387">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-388">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-388">15 seconds</span></span> | <span data-ttu-id="d5c39-389">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d5c39-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5c39-390">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5c39-391">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5c39-392">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5c39-392">All installed protocols</span></span> | <span data-ttu-id="d5c39-393">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-393">Protocols supported by this hub.</span></span> <span data-ttu-id="d5c39-394">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5c39-395">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5c39-396">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d5c39-397">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d5c39-398">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d5c39-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-399">32 KB</span></span> | <span data-ttu-id="d5c39-400">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d5c39-401">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="d5c39-402">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5c39-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5c39-403">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d5c39-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5c39-404">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5c39-405">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="d5c39-406">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5c39-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5c39-407">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-407">Option</span></span> | <span data-ttu-id="d5c39-408">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-408">Default Value</span></span> | <span data-ttu-id="d5c39-409">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5c39-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-410">32 KB</span></span> | <span data-ttu-id="d5c39-411">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d5c39-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d5c39-412">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5c39-413">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5c39-414">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5c39-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-415">32 KB</span></span> | <span data-ttu-id="d5c39-416">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d5c39-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d5c39-417">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5c39-418">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-418">All Transports are enabled.</span></span> | <span data-ttu-id="d5c39-419">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d5c39-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5c39-420">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-420">See below.</span></span> | <span data-ttu-id="d5c39-421">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5c39-422">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-422">See below.</span></span> | <span data-ttu-id="d5c39-423">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d5c39-424">0</span><span class="sxs-lookup"><span data-stu-id="d5c39-424">0</span></span> | <span data-ttu-id="d5c39-425">Especifique a versão mínima do protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="d5c39-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d5c39-426">Isso é usado para limitar clientes a versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d5c39-427">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5c39-428">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-428">Option</span></span> | <span data-ttu-id="d5c39-429">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-429">Default Value</span></span> | <span data-ttu-id="d5c39-430">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5c39-431">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-431">90 seconds</span></span> | <span data-ttu-id="d5c39-432">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5c39-433">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d5c39-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5c39-434">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5c39-435">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-435">Option</span></span> | <span data-ttu-id="d5c39-436">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-436">Default Value</span></span> | <span data-ttu-id="d5c39-437">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5c39-438">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-438">5 seconds</span></span> | <span data-ttu-id="d5c39-439">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5c39-440">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5c39-441">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5c39-442">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d5c39-442">Configure client options</span></span>

<span data-ttu-id="d5c39-443">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5c39-444">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d5c39-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5c39-445">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d5c39-445">Configure logging</span></span>

<span data-ttu-id="d5c39-446">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5c39-447">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5c39-448">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-449">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d5c39-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5c39-450">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5c39-451">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5c39-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5c39-452">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5c39-453">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d5c39-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5c39-454">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d5c39-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5c39-455">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d5c39-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d5c39-456">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d5c39-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d5c39-457">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d5c39-458">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d5c39-458">The following table lists the available log levels.</span></span> <span data-ttu-id="d5c39-459">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d5c39-460">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela** serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d5c39-461">Cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="d5c39-461">String</span></span>                      | <span data-ttu-id="d5c39-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d5c39-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d5c39-463">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d5c39-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d5c39-464">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d5c39-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d5c39-465">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5c39-466">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5c39-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5c39-467">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d5c39-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5c39-468">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d5c39-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5c39-469">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d5c39-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5c39-470">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d5c39-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5c39-471">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d5c39-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5c39-472">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5c39-472">Configure allowed transports</span></span>

<span data-ttu-id="d5c39-473">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5c39-474">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5c39-475">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-475">All transports are enabled by default.</span></span>

<span data-ttu-id="d5c39-476">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="d5c39-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5c39-477">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5c39-478">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d5c39-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d5c39-479">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d5c39-480">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-481">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d5c39-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5c39-482">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d5c39-482">Configure bearer authentication</span></span>

<span data-ttu-id="d5c39-483">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5c39-484">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5c39-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5c39-485">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5c39-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5c39-486">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5c39-487">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5c39-488">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="d5c39-489">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5c39-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5c39-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5c39-491">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5c39-492">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5c39-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5c39-493">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5c39-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-494">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-495">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-495">Option</span></span> | <span data-ttu-id="d5c39-496">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-496">Default value</span></span> | <span data-ttu-id="d5c39-497">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5c39-498">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-499">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-499">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-500">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-501">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-502">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5c39-503">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-503">15 seconds</span></span> | <span data-ttu-id="d5c39-504">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-505">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-506">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-507">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-508">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-508">15 seconds</span></span> | <span data-ttu-id="d5c39-509">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-510">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-511">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5c39-512">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d5c39-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5c39-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-514">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-514">Option</span></span> | <span data-ttu-id="d5c39-515">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-515">Default value</span></span> | <span data-ttu-id="d5c39-516">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5c39-517">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-518">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-518">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-519">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5c39-520">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-521">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5c39-522">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-523">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-524">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-525">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-526">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-526">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-527">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-527">Option</span></span> | <span data-ttu-id="d5c39-528">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-528">Default value</span></span> | <span data-ttu-id="d5c39-529">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5c39-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5c39-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5c39-531">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-532">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-532">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-533">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-534">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-535">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5c39-536">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-536">15 seconds</span></span> | <span data-ttu-id="d5c39-537">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-538">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-539">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-540">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5c39-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5c39-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5c39-542">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-543">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-544">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-545">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5c39-546">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-546">Configure additional options</span></span>

<span data-ttu-id="d5c39-547">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5c39-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-548">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-549">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-549">.NET Option</span></span> |  <span data-ttu-id="d5c39-550">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-550">Default value</span></span> | <span data-ttu-id="d5c39-551">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5c39-552">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5c39-553">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-554">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-555">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5c39-556">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-556">Empty</span></span> | <span data-ttu-id="d5c39-557">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5c39-558">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-558">Empty</span></span> | <span data-ttu-id="d5c39-559">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5c39-560">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-560">Empty</span></span> | <span data-ttu-id="d5c39-561">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5c39-562">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-562">5 seconds</span></span> | <span data-ttu-id="d5c39-563">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-563">WebSockets only.</span></span> <span data-ttu-id="d5c39-564">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5c39-565">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d5c39-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5c39-566">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-566">Empty</span></span> | <span data-ttu-id="d5c39-567">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5c39-568">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5c39-569">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5c39-570">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d5c39-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5c39-571">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d5c39-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5c39-572">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d5c39-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5c39-573">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5c39-574">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5c39-575">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d5c39-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5c39-576">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5c39-577">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d5c39-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5c39-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-579">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-579">JavaScript Option</span></span> | <span data-ttu-id="d5c39-580">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-580">Default Value</span></span> | <span data-ttu-id="d5c39-581">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5c39-582">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d5c39-583">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5c39-584">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5c39-585">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-586">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-587">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-588">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-588">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-589">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-589">Java Option</span></span> | <span data-ttu-id="d5c39-590">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-590">Default Value</span></span> | <span data-ttu-id="d5c39-591">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5c39-592">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5c39-593">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-594">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-595">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5c39-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5c39-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5c39-597">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-597">Empty</span></span> | <span data-ttu-id="d5c39-598">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5c39-599">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5c39-600">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5c39-601">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5c39-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5c39-602">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5c39-603">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-604">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5c39-605">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d5c39-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5c39-606">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5c39-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d5c39-607">`AddJsonProtocol`pode ser adicionado após [Adicionar SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5c39-608">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d5c39-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5c39-609">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d5c39-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5c39-610">Para obter mais informações, consulte a [System.Text.Jsna documentação](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d5c39-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d5c39-611">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="d5c39-612">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5c39-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5c39-613">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-614">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d5c39-615">Alternar para o Newtonsoft.Jsem</span><span class="sxs-lookup"><span data-stu-id="d5c39-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d5c39-616">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json` , consulte [alternar para o Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d5c39-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5c39-617">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-617">MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-618">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5c39-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5c39-619">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-620">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5c39-621">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d5c39-621">Configure server options</span></span>

<span data-ttu-id="d5c39-622">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d5c39-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5c39-623">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-623">Option</span></span> | <span data-ttu-id="d5c39-624">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-624">Default Value</span></span> | <span data-ttu-id="d5c39-625">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5c39-626">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-626">30 seconds</span></span> | <span data-ttu-id="d5c39-627">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5c39-628">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5c39-629">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5c39-630">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-630">15 seconds</span></span> | <span data-ttu-id="d5c39-631">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5c39-632">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-633">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-634">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-634">15 seconds</span></span> | <span data-ttu-id="d5c39-635">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d5c39-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5c39-636">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5c39-637">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5c39-638">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5c39-638">All installed protocols</span></span> | <span data-ttu-id="d5c39-639">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-639">Protocols supported by this hub.</span></span> <span data-ttu-id="d5c39-640">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5c39-641">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5c39-642">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d5c39-643">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d5c39-644">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d5c39-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-645">32 KB</span></span> | <span data-ttu-id="d5c39-646">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d5c39-647">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="d5c39-648">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5c39-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5c39-649">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d5c39-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5c39-650">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5c39-651">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="d5c39-652">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5c39-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5c39-653">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-653">Option</span></span> | <span data-ttu-id="d5c39-654">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-654">Default Value</span></span> | <span data-ttu-id="d5c39-655">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5c39-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-656">32 KB</span></span> | <span data-ttu-id="d5c39-657">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="d5c39-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d5c39-658">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5c39-659">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5c39-660">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5c39-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-661">32 KB</span></span> | <span data-ttu-id="d5c39-662">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="d5c39-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d5c39-663">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5c39-664">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-664">All Transports are enabled.</span></span> | <span data-ttu-id="d5c39-665">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d5c39-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5c39-666">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-666">See below.</span></span> | <span data-ttu-id="d5c39-667">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5c39-668">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-668">See below.</span></span> | <span data-ttu-id="d5c39-669">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5c39-670">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5c39-671">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-671">Option</span></span> | <span data-ttu-id="d5c39-672">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-672">Default Value</span></span> | <span data-ttu-id="d5c39-673">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5c39-674">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-674">90 seconds</span></span> | <span data-ttu-id="d5c39-675">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5c39-676">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d5c39-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5c39-677">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5c39-678">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-678">Option</span></span> | <span data-ttu-id="d5c39-679">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-679">Default Value</span></span> | <span data-ttu-id="d5c39-680">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5c39-681">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-681">5 seconds</span></span> | <span data-ttu-id="d5c39-682">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5c39-683">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5c39-684">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5c39-685">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d5c39-685">Configure client options</span></span>

<span data-ttu-id="d5c39-686">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5c39-687">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d5c39-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5c39-688">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d5c39-688">Configure logging</span></span>

<span data-ttu-id="d5c39-689">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5c39-690">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5c39-691">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-692">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d5c39-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5c39-693">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5c39-694">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5c39-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5c39-695">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5c39-696">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d5c39-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5c39-697">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d5c39-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5c39-698">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d5c39-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d5c39-699">Em vez de um `LogLevel` valor, você também pode fornecer um `string` valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="d5c39-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d5c39-700">Isso é útil ao configurar SignalR o log em ambientes em que você não tem acesso às `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d5c39-701">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d5c39-701">The following table lists the available log levels.</span></span> <span data-ttu-id="d5c39-702">O valor que você fornece para `configureLogging` definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d5c39-703">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela** serão registrados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d5c39-704">Cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="d5c39-704">String</span></span>                      | <span data-ttu-id="d5c39-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d5c39-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d5c39-706">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="d5c39-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d5c39-707">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="d5c39-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d5c39-708">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5c39-709">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5c39-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5c39-710">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d5c39-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5c39-711">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d5c39-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5c39-712">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d5c39-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5c39-713">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d5c39-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5c39-714">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d5c39-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5c39-715">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5c39-715">Configure allowed transports</span></span>

<span data-ttu-id="d5c39-716">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5c39-717">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5c39-718">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-718">All transports are enabled by default.</span></span>

<span data-ttu-id="d5c39-719">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="d5c39-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5c39-720">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5c39-721">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d5c39-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d5c39-722">No cliente Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d5c39-723">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-724">O SignalR cliente Java ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="d5c39-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5c39-725">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d5c39-725">Configure bearer authentication</span></span>

<span data-ttu-id="d5c39-726">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5c39-727">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5c39-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5c39-728">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5c39-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5c39-729">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5c39-730">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5c39-731">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="d5c39-732">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5c39-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5c39-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5c39-734">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5c39-735">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5c39-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5c39-736">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5c39-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-737">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-738">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-738">Option</span></span> | <span data-ttu-id="d5c39-739">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-739">Default value</span></span> | <span data-ttu-id="d5c39-740">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5c39-741">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-742">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-742">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-743">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-744">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-745">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5c39-746">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-746">15 seconds</span></span> | <span data-ttu-id="d5c39-747">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-748">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-749">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-750">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-751">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-751">15 seconds</span></span> | <span data-ttu-id="d5c39-752">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-753">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-754">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5c39-755">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d5c39-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5c39-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-757">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-757">Option</span></span> | <span data-ttu-id="d5c39-758">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-758">Default value</span></span> | <span data-ttu-id="d5c39-759">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5c39-760">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-761">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-761">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-762">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5c39-763">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-764">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5c39-765">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-766">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-767">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-768">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-769">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-769">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-770">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-770">Option</span></span> | <span data-ttu-id="d5c39-771">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-771">Default value</span></span> | <span data-ttu-id="d5c39-772">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5c39-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5c39-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5c39-774">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-775">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-775">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-776">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-777">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-778">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5c39-779">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-779">15 seconds</span></span> | <span data-ttu-id="d5c39-780">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-781">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-782">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-783">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5c39-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5c39-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5c39-785">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-786">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-787">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-788">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5c39-789">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-789">Configure additional options</span></span>

<span data-ttu-id="d5c39-790">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5c39-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-791">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-792">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-792">.NET Option</span></span> |  <span data-ttu-id="d5c39-793">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-793">Default value</span></span> | <span data-ttu-id="d5c39-794">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5c39-795">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5c39-796">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-797">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-798">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5c39-799">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-799">Empty</span></span> | <span data-ttu-id="d5c39-800">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5c39-801">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-801">Empty</span></span> | <span data-ttu-id="d5c39-802">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5c39-803">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-803">Empty</span></span> | <span data-ttu-id="d5c39-804">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5c39-805">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-805">5 seconds</span></span> | <span data-ttu-id="d5c39-806">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-806">WebSockets only.</span></span> <span data-ttu-id="d5c39-807">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5c39-808">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d5c39-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5c39-809">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-809">Empty</span></span> | <span data-ttu-id="d5c39-810">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5c39-811">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5c39-812">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5c39-813">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d5c39-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5c39-814">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d5c39-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5c39-815">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d5c39-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5c39-816">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5c39-817">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5c39-818">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d5c39-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5c39-819">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5c39-820">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d5c39-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5c39-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-822">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-822">JavaScript Option</span></span> | <span data-ttu-id="d5c39-823">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-823">Default Value</span></span> | <span data-ttu-id="d5c39-824">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5c39-825">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d5c39-826">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5c39-827">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5c39-828">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-829">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-830">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-831">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-831">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-832">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-832">Java Option</span></span> | <span data-ttu-id="d5c39-833">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-833">Default Value</span></span> | <span data-ttu-id="d5c39-834">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5c39-835">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5c39-836">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-837">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-838">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5c39-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5c39-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5c39-840">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-840">Empty</span></span> | <span data-ttu-id="d5c39-841">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5c39-842">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5c39-843">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5c39-844">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5c39-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5c39-845">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5c39-846">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-847">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5c39-848">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d5c39-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5c39-849">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após a [ SignalR adição](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5c39-850">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d5c39-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5c39-851">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d5c39-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5c39-852">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d5c39-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d5c39-853">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d5c39-854">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5c39-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5c39-855">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-856">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5c39-857">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-857">MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-858">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5c39-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5c39-859">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-860">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5c39-861">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d5c39-861">Configure server options</span></span>

<span data-ttu-id="d5c39-862">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d5c39-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5c39-863">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-863">Option</span></span> | <span data-ttu-id="d5c39-864">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-864">Default Value</span></span> | <span data-ttu-id="d5c39-865">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5c39-866">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-866">30 seconds</span></span> | <span data-ttu-id="d5c39-867">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5c39-868">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5c39-869">O valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5c39-870">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-870">15 seconds</span></span> | <span data-ttu-id="d5c39-871">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5c39-872">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-873">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-874">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-874">15 seconds</span></span> | <span data-ttu-id="d5c39-875">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d5c39-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5c39-876">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5c39-877">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5c39-878">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5c39-878">All installed protocols</span></span> | <span data-ttu-id="d5c39-879">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-879">Protocols supported by this hub.</span></span> <span data-ttu-id="d5c39-880">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5c39-881">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5c39-882">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d5c39-883">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="d5c39-884">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5c39-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5c39-885">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d5c39-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5c39-886">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5c39-887">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="d5c39-888">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5c39-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5c39-889">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-889">Option</span></span> | <span data-ttu-id="d5c39-890">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-890">Default Value</span></span> | <span data-ttu-id="d5c39-891">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5c39-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-892">32 KB</span></span> | <span data-ttu-id="d5c39-893">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d5c39-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d5c39-894">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5c39-895">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5c39-896">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5c39-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-897">32 KB</span></span> | <span data-ttu-id="d5c39-898">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d5c39-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d5c39-899">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5c39-900">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-900">All Transports are enabled.</span></span> | <span data-ttu-id="d5c39-901">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d5c39-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5c39-902">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-902">See below.</span></span> | <span data-ttu-id="d5c39-903">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5c39-904">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-904">See below.</span></span> | <span data-ttu-id="d5c39-905">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5c39-906">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5c39-907">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-907">Option</span></span> | <span data-ttu-id="d5c39-908">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-908">Default Value</span></span> | <span data-ttu-id="d5c39-909">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5c39-910">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-910">90 seconds</span></span> | <span data-ttu-id="d5c39-911">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5c39-912">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d5c39-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5c39-913">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5c39-914">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-914">Option</span></span> | <span data-ttu-id="d5c39-915">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-915">Default Value</span></span> | <span data-ttu-id="d5c39-916">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5c39-917">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-917">5 seconds</span></span> | <span data-ttu-id="d5c39-918">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5c39-919">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5c39-920">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5c39-921">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d5c39-921">Configure client options</span></span>

<span data-ttu-id="d5c39-922">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5c39-923">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d5c39-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5c39-924">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d5c39-924">Configure logging</span></span>

<span data-ttu-id="d5c39-925">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5c39-926">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5c39-927">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-928">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d5c39-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5c39-929">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5c39-930">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5c39-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5c39-931">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5c39-932">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d5c39-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5c39-933">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d5c39-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5c39-934">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d5c39-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-935">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5c39-936">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5c39-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5c39-937">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d5c39-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5c39-938">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d5c39-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5c39-939">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d5c39-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5c39-940">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d5c39-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5c39-941">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d5c39-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5c39-942">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5c39-942">Configure allowed transports</span></span>

<span data-ttu-id="d5c39-943">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5c39-944">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5c39-945">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-945">All transports are enabled by default.</span></span>

<span data-ttu-id="d5c39-946">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="d5c39-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5c39-947">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5c39-948">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="d5c39-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5c39-949">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d5c39-949">Configure bearer authentication</span></span>

<span data-ttu-id="d5c39-950">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5c39-951">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5c39-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5c39-952">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5c39-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5c39-953">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5c39-954">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5c39-955">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="d5c39-956">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5c39-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5c39-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5c39-958">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5c39-959">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5c39-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5c39-960">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5c39-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-961">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-962">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-962">Option</span></span> | <span data-ttu-id="d5c39-963">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-963">Default value</span></span> | <span data-ttu-id="d5c39-964">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5c39-965">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-966">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-966">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-967">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-968">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-969">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5c39-970">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-970">15 seconds</span></span> | <span data-ttu-id="d5c39-971">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-972">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-973">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-974">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-975">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-975">15 seconds</span></span> | <span data-ttu-id="d5c39-976">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-977">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-978">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5c39-979">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d5c39-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5c39-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-981">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-981">Option</span></span> | <span data-ttu-id="d5c39-982">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-982">Default value</span></span> | <span data-ttu-id="d5c39-983">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5c39-984">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-985">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-985">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-986">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5c39-987">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-988">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5c39-989">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-990">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-991">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-992">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-993">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-993">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-994">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-994">Option</span></span> | <span data-ttu-id="d5c39-995">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-995">Default value</span></span> | <span data-ttu-id="d5c39-996">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5c39-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5c39-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5c39-998">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-999">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-999">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-1000">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-1001">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-1002">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5c39-1003">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-1003">15 seconds</span></span> | <span data-ttu-id="d5c39-1004">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-1005">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-1006">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-1007">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5c39-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5c39-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5c39-1009">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-1010">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5c39-1011">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5c39-1012">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5c39-1013">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-1013">Configure additional options</span></span>

<span data-ttu-id="d5c39-1014">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-1016">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-1016">.NET Option</span></span> |  <span data-ttu-id="d5c39-1017">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1017">Default value</span></span> | <span data-ttu-id="d5c39-1018">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5c39-1019">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5c39-1020">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-1021">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-1022">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5c39-1023">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1023">Empty</span></span> | <span data-ttu-id="d5c39-1024">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5c39-1025">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1025">Empty</span></span> | <span data-ttu-id="d5c39-1026">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5c39-1027">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1027">Empty</span></span> | <span data-ttu-id="d5c39-1028">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5c39-1029">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-1029">5 seconds</span></span> | <span data-ttu-id="d5c39-1030">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1030">WebSockets only.</span></span> <span data-ttu-id="d5c39-1031">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5c39-1032">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5c39-1033">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1033">Empty</span></span> | <span data-ttu-id="d5c39-1034">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5c39-1035">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5c39-1036">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5c39-1037">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5c39-1038">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5c39-1039">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d5c39-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5c39-1040">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5c39-1041">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5c39-1042">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5c39-1043">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5c39-1044">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5c39-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-1046">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-1046">JavaScript Option</span></span> | <span data-ttu-id="d5c39-1047">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1047">Default Value</span></span> | <span data-ttu-id="d5c39-1048">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5c39-1049">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d5c39-1050">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5c39-1051">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5c39-1052">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-1053">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-1054">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-1055">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-1056">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-1056">Java Option</span></span> | <span data-ttu-id="d5c39-1057">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1057">Default Value</span></span> | <span data-ttu-id="d5c39-1058">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5c39-1059">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5c39-1060">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-1061">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-1062">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5c39-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5c39-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5c39-1064">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1064">Empty</span></span> | <span data-ttu-id="d5c39-1065">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5c39-1066">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5c39-1067">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5c39-1068">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5c39-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5c39-1069">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5c39-1070">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-1071">O ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5c39-1072">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5c39-1073">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que pode ser adicionado após a [ SignalR adição](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5c39-1074">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5c39-1075">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5c39-1076">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d5c39-1077">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d5c39-1078">No cliente .NET, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5c39-1079">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-1080">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5c39-1081">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5c39-1081">MessagePack serialization options</span></span>

<span data-ttu-id="d5c39-1082">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5c39-1083">Consulte [MessagePack em SignalR ](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-1084">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5c39-1085">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="d5c39-1085">Configure server options</span></span>

<span data-ttu-id="d5c39-1086">A tabela a seguir descreve as opções para configurar SignalR hubs:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5c39-1087">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-1087">Option</span></span> | <span data-ttu-id="d5c39-1088">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1088">Default Value</span></span> | <span data-ttu-id="d5c39-1089">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="d5c39-1090">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-1090">15 seconds</span></span> | <span data-ttu-id="d5c39-1091">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5c39-1092">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-1093">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5c39-1094">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-1094">15 seconds</span></span> | <span data-ttu-id="d5c39-1095">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5c39-1096">Ao alterar `KeepAliveInterval` , altere a `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5c39-1097">O `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado é o dobro do `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5c39-1098">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5c39-1098">All installed protocols</span></span> | <span data-ttu-id="d5c39-1099">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="d5c39-1100">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5c39-1101">Se `true` , as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5c39-1102">O padrão é `false` , pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d5c39-1103">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a `AddSignalR` chamada em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="d5c39-1104">As opções para um único Hub substituem as opções globais fornecidas no `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5c39-1105">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="d5c39-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5c39-1106">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5c39-1107">Essas opções são configuradas passando um delegado [para \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="d5c39-1108">A tabela a seguir descreve as opções para configurar SignalR as opções http avançadas do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5c39-1109">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-1109">Option</span></span> | <span data-ttu-id="d5c39-1110">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1110">Default Value</span></span> | <span data-ttu-id="d5c39-1111">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5c39-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-1112">32 KB</span></span> | <span data-ttu-id="d5c39-1113">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d5c39-1114">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5c39-1115">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5c39-1116">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5c39-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5c39-1117">32 KB</span></span> | <span data-ttu-id="d5c39-1118">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d5c39-1119">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5c39-1120">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1120">All Transports are enabled.</span></span> | <span data-ttu-id="d5c39-1121">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5c39-1122">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1122">See below.</span></span> | <span data-ttu-id="d5c39-1123">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5c39-1124">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1124">See below.</span></span> | <span data-ttu-id="d5c39-1125">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5c39-1126">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5c39-1127">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-1127">Option</span></span> | <span data-ttu-id="d5c39-1128">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1128">Default Value</span></span> | <span data-ttu-id="d5c39-1129">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5c39-1130">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-1130">90 seconds</span></span> | <span data-ttu-id="d5c39-1131">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5c39-1132">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5c39-1133">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a `WebSockets` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5c39-1134">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-1134">Option</span></span> | <span data-ttu-id="d5c39-1135">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1135">Default Value</span></span> | <span data-ttu-id="d5c39-1136">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5c39-1137">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-1137">5 seconds</span></span> | <span data-ttu-id="d5c39-1138">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5c39-1139">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5c39-1140">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5c39-1141">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="d5c39-1141">Configure client options</span></span>

<span data-ttu-id="d5c39-1142">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5c39-1143">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como por `HubConnection` si só.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5c39-1144">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="d5c39-1144">Configure logging</span></span>

<span data-ttu-id="d5c39-1145">O registro em log é configurado no cliente .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5c39-1146">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5c39-1147">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c39-1148">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5c39-1149">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5c39-1150">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5c39-1151">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5c39-1152">No cliente JavaScript, `configureLogging` existe um método semelhante.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5c39-1153">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5c39-1154">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5c39-1155">Para desabilitar completamente o registro em log, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5c39-1156">Para obter mais informações sobre registro em log, consulte a [ SignalR documentação de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5c39-1157">O SignalR cliente Java usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5c39-1158">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5c39-1159">O trecho de código a seguir mostra como usar `java.util.logging` o com o SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5c39-1160">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5c39-1161">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5c39-1162">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5c39-1162">Configure allowed transports</span></span>

<span data-ttu-id="d5c39-1163">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamada ( `withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5c39-1164">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5c39-1165">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="d5c39-1166">Por exemplo, para desabilitar o transporte de eventos Server-Sent, mas permitir WebSockets e conexões de sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5c39-1167">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5c39-1168">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="d5c39-1168">Configure bearer authentication</span></span>

<span data-ttu-id="d5c39-1169">Para fornecer dados de autenticação juntamente com SignalR solicitações, use a `AccessTokenProvider` opção ( `accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5c39-1170">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5c39-1171">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos de Server-Sent e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5c39-1172">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5c39-1173">No cliente .NET, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5c39-1174">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="d5c39-1175">No SignalR cliente Java, você pode configurar um token de portador para usar para autenticação fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5c39-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer um [RxJava](https://github.com/ReactiveX/RxJava) [único \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5c39-1177">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5c39-1178">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5c39-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5c39-1179">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no `HubConnection` próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-1181">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-1181">Option</span></span> | <span data-ttu-id="d5c39-1182">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1182">Default value</span></span> | <span data-ttu-id="d5c39-1183">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5c39-1184">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-1185">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1185">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-1186">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-1187">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-1188">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5c39-1189">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-1189">15 seconds</span></span> | <span data-ttu-id="d5c39-1190">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-1191">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento ( `onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5c39-1192">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-1193">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="d5c39-1194">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5c39-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-1196">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-1196">Option</span></span> | <span data-ttu-id="d5c39-1197">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1197">Default value</span></span> | <span data-ttu-id="d5c39-1198">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5c39-1199">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-1200">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1200">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-1201">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5c39-1202">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-1203">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-1204">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-1205">Opção</span><span class="sxs-lookup"><span data-stu-id="d5c39-1205">Option</span></span> | <span data-ttu-id="d5c39-1206">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1206">Default value</span></span> | <span data-ttu-id="d5c39-1207">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5c39-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5c39-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5c39-1209">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="d5c39-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5c39-1210">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1210">Timeout for server activity.</span></span> <span data-ttu-id="d5c39-1211">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-1212">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5c39-1213">O valor recomendado é um número, pelo menos, o dobro do valor do servidor `KeepAliveInterval` , para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5c39-1214">15 s</span><span class="sxs-lookup"><span data-stu-id="d5c39-1214">15 seconds</span></span> | <span data-ttu-id="d5c39-1215">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5c39-1216">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5c39-1217">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5c39-1218">Para obter mais detalhes sobre o processo de handshake, consulte a [ SignalR especificação do protocolo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5c39-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5c39-1219">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-1219">Configure additional options</span></span>

<span data-ttu-id="d5c39-1220">Opções adicionais podem ser configuradas `WithUrl` no `withUrl` método (em JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5c39-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5c39-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5c39-1222">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="d5c39-1222">.NET Option</span></span> |  <span data-ttu-id="d5c39-1223">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1223">Default value</span></span> | <span data-ttu-id="d5c39-1224">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5c39-1225">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5c39-1226">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-1227">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-1228">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5c39-1229">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1229">Empty</span></span> | <span data-ttu-id="d5c39-1230">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5c39-1231">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1231">Empty</span></span> | <span data-ttu-id="d5c39-1232">Uma coleção de HTTP cookie s a ser enviada com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5c39-1233">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1233">Empty</span></span> | <span data-ttu-id="d5c39-1234">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5c39-1235">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5c39-1235">5 seconds</span></span> | <span data-ttu-id="d5c39-1236">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1236">WebSockets only.</span></span> <span data-ttu-id="d5c39-1237">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5c39-1238">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5c39-1239">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1239">Empty</span></span> | <span data-ttu-id="d5c39-1240">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5c39-1241">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5c39-1242">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5c39-1243">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5c39-1244">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5c39-1245">**Ao substituir o manipulador, certifique-se de copiar as configurações que você deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como Cookie s e cabeçalhos) não serão aplicadas ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="d5c39-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5c39-1246">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5c39-1247">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5c39-1248">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5c39-1249">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5c39-1250">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5c39-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5c39-1252">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5c39-1252">JavaScript Option</span></span> | <span data-ttu-id="d5c39-1253">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1253">Default Value</span></span> | <span data-ttu-id="d5c39-1254">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5c39-1255">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="d5c39-1256">Um <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> valor que especifica o transporte a ser usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5c39-1257">Defina como `true` para registrar em log os bytes/caracteres de mensagens enviadas e recebidas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5c39-1258">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-1259">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-1260">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5c39-1261">Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="d5c39-1262">Opção Java</span><span class="sxs-lookup"><span data-stu-id="d5c39-1262">Java Option</span></span> | <span data-ttu-id="d5c39-1263">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="d5c39-1263">Default Value</span></span> | <span data-ttu-id="d5c39-1264">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5c39-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5c39-1265">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5c39-1266">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5c39-1267">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5c39-1268">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5c39-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5c39-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5c39-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5c39-1270">Vazio</span><span class="sxs-lookup"><span data-stu-id="d5c39-1270">Empty</span></span> | <span data-ttu-id="d5c39-1271">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5c39-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5c39-1272">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5c39-1273">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5c39-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5c39-1274">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5c39-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5c39-1275">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d5c39-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
