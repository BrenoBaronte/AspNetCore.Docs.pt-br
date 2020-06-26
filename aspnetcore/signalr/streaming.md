---
title: Usar Streaming no ASP.NET CoreSignalR
author: bradygaster
description: Saiba como transmitir dados entre o cliente e o servidor.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/streaming
ms.openlocfilehash: c7a3c7bb88230d84025bdf02deb98b51a2d1f92a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406167"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Usar Streaming no ASP.NET CoreSignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR dá suporte ao streaming do cliente para o servidor e do servidor para o cliente. Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo. Ao transmitir, cada fragmento é enviado ao cliente ou ao servidor assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O ASP.NET Core SignalR dá suporte a valores de retorno de streaming de métodos de servidor. Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo. Quando um valor de retorno é transmitido para o cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.

::: moniker-end

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Configurar um hub para streaming

::: moniker range=">= aspnetcore-3.0"

Um método de Hub se torna automaticamente um método de Hub de streaming quando retorna <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` ou `Task<ChannelReader<T>>` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um método de Hub se torna automaticamente um método de Hub de streaming quando retorna um <xref:System.Threading.Channels.ChannelReader%601> ou um `Task<ChannelReader<T>>` .

::: moniker-end

### <a name="server-to-client-streaming"></a>Streaming de servidor para cliente

::: moniker range=">= aspnetcore-3.0"

Os métodos do hub de streaming podem retornar além `IAsyncEnumerable<T>` do `ChannelReader<T>` . A maneira mais simples de retornar `IAsyncEnumerable<T>` é fazendo com que o método de Hub seja um método de iterador assíncrono como demonstrado no exemplo a seguir. Os métodos iteradores assíncronos de Hub podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo. Os métodos iteradores assíncronos evitam problemas comuns com canais, como não retornar o `ChannelReader` início suficiente ou sair do método sem concluir o <xref:System.Threading.Channels.ChannelWriter`1> .

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

O exemplo a seguir mostra as noções básicas de streaming de dados para o cliente usando canais. Sempre que um objeto é gravado no <xref:System.Threading.Channels.ChannelWriter%601> , o objeto é enviado imediatamente para o cliente. No final, o `ChannelWriter` é concluído para informar ao cliente que o fluxo está fechado.

> [!NOTE]
> Grave em `ChannelWriter<T>` um thread em segundo plano e retorne o assim `ChannelReader` que possível. Outras invocações de Hub são bloqueadas até que um `ChannelReader` seja retornado.
>
> Ajuste a lógica em um `try ... catch` . Conclua o `Channel` no `catch` e fora do `catch` para verificar se a invocação do método de Hub foi concluída corretamente.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Os métodos de Hub de streaming de servidor para cliente podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo. Use esse token para interromper a operação do servidor e liberar todos os recursos se o cliente se desconectar antes do final do fluxo.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming de cliente para servidor

Um método de Hub se torna automaticamente um método de Hub de streaming de cliente para servidor quando ele aceita um ou mais objetos do tipo <xref:System.Threading.Channels.ChannelReader%601> ou <xref:System.Collections.Generic.IAsyncEnumerable%601> . O exemplo a seguir mostra as noções básicas de leitura de dados de streaming enviados do cliente. Sempre que o cliente grava no <xref:System.Threading.Channels.ChannelWriter%601> , os dados são gravados no `ChannelReader` no servidor do qual o método de Hub está lendo.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<xref:System.Collections.Generic.IAsyncEnumerable%601>Segue uma versão do método.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a>Cliente .NET

### <a name="server-to-client-streaming"></a>Streaming de servidor para cliente


::: moniker range=">= aspnetcore-3.0"

Os `StreamAsync` `StreamAsChannelAsync` métodos e em `HubConnection` são usados para invocar métodos de transmissão de servidor para cliente. Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsync` ou `StreamAsChannelAsync` . O parâmetro genérico em `StreamAsync<T>` e `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming. Um objeto do tipo `IAsyncEnumerable<T>` ou `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.

Um `StreamAsync` exemplo que retorna `IAsyncEnumerable<int>` :

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

Um `StreamAsChannelAsync` exemplo correspondente que retorna `ChannelReader<int>` :

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

O `StreamAsChannelAsync` método on `HubConnection` é usado para invocar um método de transmissão de servidor para cliente. Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsChannelAsync` . O parâmetro Generic em `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming. Um `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

O `StreamAsChannelAsync` método on `HubConnection` é usado para invocar um método de transmissão de servidor para cliente. Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsChannelAsync` . O parâmetro Generic em `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming. Um `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming de cliente para servidor

Há duas maneiras de invocar um método de Hub de transmissão de cliente para servidor do cliente .NET. Você pode passar um `IAsyncEnumerable<T>` ou um `ChannelReader` como um argumento para `SendAsync` , `InvokeAsync` ou `StreamAsChannelAsync` , dependendo do método de Hub invocado.

Sempre que os dados são gravados no `IAsyncEnumerable` `ChannelWriter` objeto ou, o método de Hub no servidor recebe um novo item com os dados do cliente.

Se estiver usando um `IAsyncEnumerable` objeto, o fluxo termina depois que o método que retorna os itens de fluxo é encerrado.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Ou, se você estiver usando um `ChannelWriter` , você conclui o canal com `channel.Writer.Complete()` :

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Cliente JavaScript

### <a name="server-to-client-streaming"></a>Streaming de servidor para cliente

Os clientes JavaScript chamam métodos de streaming de servidor para cliente em hubs com `connection.stream` . O `stream` método aceita dois argumentos:

* O nome do método de Hub. No exemplo a seguir, o nome do método de Hub é `Counter` .
* Argumentos definidos no método Hub. No exemplo a seguir, os argumentos são uma contagem para o número de itens de fluxo a serem recebidos e o atraso entre itens de fluxo.

`connection.stream`Retorna um `IStreamResult` , que contém um `subscribe` método. Passe um `IStreamSubscriber` para `subscribe` e defina os `next` `error` `complete` retornos de chamada, e para receber notificações da `stream` invocação.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Para finalizar o fluxo do cliente, chame o `dispose` método no `ISubscription` que é retornado do `subscribe` método. Chamar esse método causa o cancelamento do `CancellationToken` parâmetro do método de Hub, se você tiver fornecido um.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Para finalizar o fluxo do cliente, chame o `dispose` método no `ISubscription` que é retornado do `subscribe` método.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming de cliente para servidor

Os clientes JavaScript chamam métodos de transmissão de cliente para servidor em hubs, passando um `Subject` como um argumento para `send` , `invoke` ou `stream` , dependendo do método de Hub invocado. O `Subject` é uma classe semelhante a um `Subject` . Por exemplo, em RxJS, você pode usar a classe [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) dessa biblioteca.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Chamar `subject.next(item)` com um item grava o item no fluxo e o método Hub recebe o item no servidor.

Para encerrar o fluxo, chame `subject.complete()` .

## <a name="java-client"></a>Cliente Java

### <a name="server-to-client-streaming"></a>Streaming de servidor para cliente

O SignalR cliente Java usa o `stream` método para invocar métodos de streaming. `stream`aceita três ou mais argumentos:

* O tipo esperado dos itens de fluxo.
* O nome do método de Hub.
* Argumentos definidos no método Hub.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

O `stream` método em `HubConnection` retorna um observável do tipo de item de fluxo. O método do tipo observável `subscribe` é `onNext` onde `onError` e os `onCompleted` manipuladores são definidos.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Hubs](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
