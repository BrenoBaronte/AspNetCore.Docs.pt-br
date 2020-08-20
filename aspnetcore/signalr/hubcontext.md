---
title: SignalR HubContext
author: bradygaster
description: Saiba como usar o serviço de ASP.NET Core SignalR HubContext para enviar notificações para clientes de fora de um Hub.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
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
uid: signalr/hubcontext
ms.openlocfilehash: c6a4926be008feb2c9b708c56597070b96d8bd3f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633013"
---
# <a name="send-messages-from-outside-a-hub"></a>Enviar mensagens de fora de um hub

Por [Mikael Mengistu](https://twitter.com/MikaelM_12)

O SignalR Hub é a abstração principal para o envio de mensagens aos clientes conectados ao SignalR servidor. Também é possível enviar mensagens de outros locais em seu aplicativo usando o `IHubContext` serviço. Este artigo explica como acessar um SignalR `IHubContext` para enviar notificações para clientes de fora de um Hub.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Obter uma instância de IHubContext

No ASP.NET Core SignalR , você pode acessar uma instância do `IHubContext` por meio de injeção de dependência. Você pode injetar uma instância do `IHubContext` em um controlador, middleware ou outro serviço de di. Use a instância do para enviar mensagens aos clientes.

> [!NOTE]
> Isso difere do ASP.NET 4. x, SignalR que usava GlobalHost para fornecer acesso ao `IHubContext` . ASP.NET Core tem uma estrutura de injeção de dependência que elimina a necessidade desse singleton global.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Injetar uma instância de IHubContext em um controlador

Você pode injetar uma instância do `IHubContext` em um controlador adicionando-a ao seu construtor:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Agora, com acesso a uma instância do `IHubContext` , você pode chamar métodos de Hub como se estivesse no próprio Hub.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Obter uma instância de IHubContext no middleware

Acesse o `IHubContext` no pipeline de middleware da seguinte forma:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> Quando os métodos de Hub são chamados de fora da `Hub` classe, não há nenhum chamador associado à invocação. Portanto, não há acesso às `ConnectionId` `Caller` Propriedades, e `Others` .

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a>Obter uma instância de IHubContext de IHost

O acesso `IHubContext` a um do host da Web é útil para integração com áreas fora do ASP.NET Core, por exemplo, usando estruturas de injeção de dependência de terceiros:

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a>Injetar um HubContext fortemente tipado

Para injetar um HubContext fortemente tipado, verifique se o seu hub é herdado de `Hub<T>` . Insira-o usando a `IHubContext<THub, T>` interface em vez de `IHubContext<THub>` .

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
