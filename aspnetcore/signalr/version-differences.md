---
title: Diferenças entre SignalR e ASP.NET Core SignalR
author: bradygaster
description: Diferenças entre SignalR e ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: c37f0a3c528b6285fbf35f41eed781fb76c68de4
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854594"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a>Diferenças entre ASP.NET SignalR e ASP.NET Core SignalR

ASP.NET Core SignalR não é compatível com clientes ou servidores para ASP.NET SignalR . Este artigo detalha os recursos que foram removidos ou alterados no ASP.NET Core SignalR .

## <a name="how-to-identify-the-no-locsignalr-version"></a>Como identificar a SignalR versão

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **Pacote NuGet do servidor** | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Nenhum. Incluído na estrutura compartilhada [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| **Pacotes NuGet do cliente** | [Microsoft. AspNet. SignalR . Cliente](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR . Cliente](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **Pacote de NPM de cliente JavaScript** | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| **Cliente Java** | [Repositório do GitHub](https://github.com/SignalR/java-client) (preterido)  | Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **Tipo de aplicativo do servidor** | ASP.NET (System. Web) ou OWIN Self-Host | ASP.NET Core |
| **Plataformas de servidor com suporte** | .NET Framework 4,5 ou posterior | .NET Core 3,0 ou posterior |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **Pacote NuGet do servidor** | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| **Pacotes NuGet do cliente** | [Microsoft. AspNet. SignalR . Cliente](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR . Cliente](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **Pacote de NPM de cliente JavaScript** | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| **Cliente Java** | [Repositório do GitHub](https://github.com/SignalR/java-client) (preterido)  | Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **Tipo de aplicativo do servidor** | ASP.NET (System. Web) ou OWIN Self-Host | ASP.NET Core |
| **Plataformas de servidor com suporte** | .NET Framework 4,5 ou posterior | .NET Framework 4.6.1 ou posterior<br>.NET Core 2,1 ou posterior |

::: moniker-end

## <a name="feature-differences"></a>Diferenças de recursos

### <a name="automatic-reconnects"></a>Reconexões automáticas

::: moniker range=">= aspnetcore-3.0"

Em ASP.NET SignalR :

* Por padrão, SignalR o tentará se reconectar ao servidor se a conexão for descartada. 

Em ASP.NET Core SignalR :

* As reconexões automáticas são aceitas com o [cliente .net](xref:signalr/dotnet-client#automatically-reconnect) e o [cliente JavaScript](xref:signalr/javascript-client#automatically-reconnect):

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Antes do ASP.NET Core 3,0, o SignalR não oferece suporte a reconexões automáticas. Se o cliente estiver desconectado, o usuário deverá iniciar explicitamente uma nova conexão para se reconectar. No ASP.NET SignalR , SignalR o tentará se reconectar ao servidor se a conexão for descartada.

::: moniker-end

### <a name="protocol-support"></a>Suporte a protocolo

ASP.NET Core SignalR dá suporte a JSON, bem como a um novo protocolo binário com base em [MessagePack](xref:signalr/messagepackhubprotocol). Além disso, os protocolos personalizados podem ser criados.

### <a name="transports"></a>Transportes

O transporte de quadro contínuo não tem suporte no ASP.NET Core SignalR .

## <a name="differences-on-the-server"></a>Diferenças no servidor

As SignalR bibliotecas do lado do servidor ASP.NET Core estão incluídas em [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), que é usado no modelo de **aplicativo Web do ASP.NET Core** para projetos do e do Razor MVC.

ASP.NET Core SignalR é um middleware ASP.NET Core. Ele deve ser configurado chamando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> em `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Para configurar o roteamento, mapeie as rotas para os hubs dentro da <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> chamada do método no `Startup.Configure` método.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Para configurar o roteamento, mapeie as rotas para os hubs dentro da <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> chamada do método no `Startup.Configure` método.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Sessões adesivas

O modelo de dimensionamento para ASP.NET SignalR permite que os clientes se reconectem e enviem mensagens para qualquer servidor no farm. No ASP.NET Core SignalR , o cliente deve interagir com o mesmo servidor durante a conexão. Para expansão usando Redis, isso significa que as sessões adesivas são necessárias. Para o scale out usando o [ SignalR serviço do Azure](/azure/azure-signalr/), as sessões adesivas não são necessárias porque o serviço lida com conexões com os clientes.

### <a name="single-hub-per-connection"></a>Hub único por conexão

No ASP.NET Core SignalR , o modelo de conexão foi simplificado. As conexões são feitas diretamente em um único Hub, em vez de uma única conexão sendo usada para compartilhar o acesso a vários hubs.

### <a name="streaming"></a>Streaming

O ASP.NET Core SignalR agora dá suporte ao [streaming de dados](xref:signalr/streaming) do hub para o cliente.

### <a name="state"></a>Estado

A capacidade de passar um estado arbitrário entre clientes e o Hub (geralmente chamado `HubState` ) foi removida, bem como suporte para mensagens de progresso. Não há nenhum equivalente de proxies de Hub no momento.

### <a name="persistentconnection-removal"></a>Remoção de PersistentConnection

No ASP.NET Core SignalR , a classe [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) foi removida.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core tem injeção de dependência (DI) incorporada à estrutura. Os serviços podem usar DI para acessar o [HubContext](xref:signalr/hubcontext). O `GlobalHost` objeto usado em ASP.NET SignalR para obter um `HubContext` não existe em ASP.NET Core SignalR .

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR não tem suporte para `HubPipeline` módulos.

## <a name="differences-on-the-client"></a>Diferenças no cliente

### <a name="typescript"></a>TypeScript

O SignalR cliente ASP.NET Core é gravado em [TypeScript](https://www.typescriptlang.org/). Você pode escrever em JavaScript ou TypeScript ao usar o [cliente JavaScript](xref:signalr/javascript-client).

### <a name="the-javascript-client-is-hosted-at-npm"></a>O cliente JavaScript está hospedado em NPM

::: moniker range=">= aspnetcore-3.0"

Em versões do ASP.NET, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio. Nas versões do ASP.NET Core, o [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) pacote NPM contém as bibliotecas JavaScript. Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** . Use o NPM para obter e instalar o `@microsoft/signalr` pacote NPM.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Em versões do ASP.NET, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio. Nas versões do ASP.NET Core, o [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) pacote NPM contém as bibliotecas JavaScript. Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** . Use o NPM para obter e instalar o `@aspnet/signalr` pacote NPM.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

A dependência do jQuery foi removida, no entanto, os projetos ainda podem usar o jQuery.

### <a name="internet-explorer-support"></a>Suporte do Internet Explorer

SignalRO ASP.NET Core não dá suporte ao Microsoft Internet Explorer, enquanto SignalR o ASP.net dá suporte ao Microsoft Internet Explorer 8 ou posterior.
Para obter mais informações, consulte <xref:signalr/supported-platforms#javascript-client>.

### <a name="javascript-client-method-syntax"></a>Sintaxe do método de cliente JavaScript

::: moniker range=">= aspnetcore-3.0"

A sintaxe JavaScript foi alterada da versão ASP.NET do SignalR . Em vez de usar o `$connection` objeto, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Use o método [on](/javascript/api/@microsoft/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

A sintaxe JavaScript foi alterada da versão ASP.NET do SignalR . Em vez de usar o `$connection` objeto, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Use o método [on](/javascript/api/@aspnet/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

Depois de criar o método de cliente, inicie a conexão de Hub. Encadeamento um método [Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) para registrar ou tratar erros.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Proxies de Hub

::: moniker range=">= aspnetcore-3.0"

Os proxies do Hub não são mais gerados automaticamente. Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) como uma cadeia de caracteres.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Os proxies do Hub não são mais gerados automaticamente. Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) como uma cadeia de caracteres.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET e outros clientes

O [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) O pacote NuGet do cliente contém as bibliotecas de cliente .net para ASP.NET Core SignalR .

Use o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> para criar e criar uma instância de uma conexão com um Hub.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Diferenças de dimensionamento

ASP.NET SignalR dá suporte a SQL Server e Redis. ASP.NET Core SignalR dá suporte ao serviço do Azure SignalR e Redis.

### <a name="aspnet"></a>ASP.NET

* [SignalR expansão com o barramento de serviço do Azure](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR scale out com Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR dimensionamento com SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Serviço do Azure SignalR](/azure/azure-signalr/)
* [Redis backplane](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Recursos adicionais

* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Plataformas com suporte](xref:signalr/supported-platforms)
