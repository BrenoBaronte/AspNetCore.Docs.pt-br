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
ms.openlocfilehash: 83a41d503b2d56bca3f1bac14eeb9d54a8257642
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057772"
---
# <a name="websockets-support-in-aspnet-core"></a>Suporte ao WebSockets no ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra) e [Andrew Stanton-Nurse](https://github.com/anurse)

Este artigo explica como começar a usar o WebSockets no ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) é um protocolo que permite canais de comunicação persistentes bidirecionais em conexões TCP. Ele é usado em aplicativos que se beneficiam de comunicação rápida e em tempo real, como chat, painel e aplicativos de jogos.

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([como baixar](xref:index#how-to-download-a-sample)). [Como executar](#sample-app).

## SignalR

[ASP.NET Core SignalR ](xref:signalr/introduction) é uma biblioteca que simplifica a adição de funcionalidades da Web em tempo real a aplicativos. Ele usa WebSockets sempre que possível.

Para a maioria dos aplicativos, recomendamos SignalR sobre WebSockets brutos. SignalR fornece fallback de transporte para ambientes em que o WebSockets não está disponível. Ele também fornece um modelo básico de aplicativo de chamada de procedimento remoto. E na maioria dos cenários, SignalR não há uma desvantagem significativa no desempenho em comparação com o uso de WebSockets brutos.

Para alguns aplicativos, o [gRPC no .net](xref:grpc/index) fornece uma alternativa para WebSockets.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer sistema operacional compatível com o ASP.NET Core:  
  * Windows 7/Windows Server 2008 ou posterior
  * Linux
  * macOS  
* Se o aplicativo é executado no Windows com o IIS:
  * Windows 8/Windows Server 2012 ou posterior
  * IIS 8/IIS 8 Express
  * Os WebSockets devem ser habilitados. Consulte a seção [suporte do IIS/IIS Express](#iisiis-express-support) .  
* Se o aplicativo é executado no [HTTP.sys](xref:fundamentals/servers/httpsys):
  * Windows 8/Windows Server 2012 ou posterior
* Para saber quais são os navegadores compatíveis, confira https://caniuse.com/#feat=websockets.

## <a name="configure-the-middleware"></a>Configurar o middleware

Adicione o middleware do WebSockets no método `Configure` da classe `Startup`:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

As seguintes configurações podem ser definidas:

* `KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta. O padrão é dois minutos.
* `ReceiveBufferSize` – o tamanho do buffer usado para receber dados. Os usuários avançados podem precisar alterar isso para ajuste de desempenho com base no tamanho dos dados. O padrão é 4 KB.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

As seguintes configurações podem ser definidas:

* `KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta. O padrão é dois minutos.
* <xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> – o tamanho do buffer usado para receber dados. Os usuários avançados podem precisar alterar isso para ajuste de desempenho com base no tamanho dos dados. O padrão é 4 KB.
* `AllowedOrigins` – Uma lista de valores de cabeçalho de origem permitidos para solicitações do WebSocket. Por padrão, todas as origens são permitidas. Consulte "Restrição de origem do WebSocket" abaixo para obter detalhes.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Aceitar solicitações do WebSocket

Futuramente no ciclo de vida da solicitação (mais tarde no método `Configure` ou em um método de ação, por exemplo), verifique se é uma solicitação do WebSocket e aceite-a.

O exemplo a seguir é de uma fase posterior do método `Configure`:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Uma solicitação do WebSocket pode entrar em qualquer URL, mas esse código de exemplo aceita apenas solicitações de `/ws`.

Ao usar um WebSocket, você **deve** manter o pipeline de middleware em execução durante a conexão. Se tentar enviar ou receber uma mensagem do WebSocket após o término do pipeline de middleware, você poderá receber uma exceção semelhante à seguinte:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Se você estiver usando um serviço em segundo plano para gravar dados em um WebSocket, mantenha o pipeline do middleware em execução. Faça isso usando um <xref:System.Threading.Tasks.TaskCompletionSource%601>. Passe o `TaskCompletionSource` ao seu serviço em segundo plano e faça-o chamar <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> quando terminar com o WebSocket. Em seguida `await` a propriedade <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante a solicitação, conforme é mostrado no exemplo a seguir:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

A exceção de WebSocket Closed também pode ocorrer ao retornar muito cedo de um método de ação. Ao aceitar um soquete em um método de ação, aguarde o código que usa o soquete para ser concluído antes de retornar do método de ação.

Nunca use `Task.Wait`, `Task.Result` ou chamadas de bloqueio semelhantes para aguardar a conclusão do soquete, pois isso pode causar sérios problemas de threading. Sempre use `await`.

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

O método `AcceptWebSocketAsync` atualiza a conexão TCP para uma conexão WebSocket e fornece um objeto [WebSocket](/dotnet/core/api/system.net.websockets.websocket). Use o objeto `WebSocket` para enviar e receber mensagens.

O código mostrado anteriormente que aceita a solicitação do WebSocket passa o objeto `WebSocket` para um método `Echo`. O código recebe uma mensagem e envia de volta imediatamente a mesma mensagem. As mensagens são enviadas e recebidas em um loop até que o cliente feche a conexão:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Ao aceitar a conexão WebSocket antes de iniciar o loop, o pipeline de middleware é encerrado. Ao fechar o soquete, o pipeline é desenrolado. Ou seja, a solicitação deixa de avançar no pipeline quando o WebSocket é aceito. Quando o loop é concluído e o soquete é fechado, a solicitação continua a avançar no pipeline.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Tratar desconexões de cliente

O servidor não é informado automaticamente quando o cliente se desconecta devido à perda de conectividade. O servidor recebe uma mensagem de desconexão apenas quando o cliente a envia, e isso é possível somente quando há conexão com a Internet. Se quiser tomar alguma medida quando isso ocorrer, defina um tempo limite, caso não receba nada do cliente após uma determinada janela de tempo.

Se o cliente não envia mensagens com frequência, e você prefere não definir um tempo limite apenas porque a conexão fica ociosa, peça ao cliente para usar um temporizador a fim de enviar uma mensagem de ping a cada X segundos. No servidor, se uma mensagem não chegar dentro de 2\*X segundos após a mensagem anterior, encerre a conexão e informe que o cliente se desconectou. Aguarde o dobro do intervalo de tempo esperado a fim de deixar um tempo extra para atrasos na rede, que possam atrasar a mensagem de ping.

## <a name="websocket-origin-restriction"></a>Restrição de origem do WebSocket

As proteções fornecidas pelo CORS não se aplicam ao WebSockets. Navegadores **não**:

* Executam solicitações de simulação de CORS.
* Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.

No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket. Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.

Se você estiver hospedando o servidor em "https://server.com" e hospedando seu cliente em "https://client.com", adicione "https://client.com" à lista `AllowedOrigins` para o WebSockets verificar.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado. **Não** use esses cabeçalhos como um mecanismo de autenticação.

::: moniker-end

## <a name="iisiis-express-support"></a>Suporte ao IIS/IIS Express

O Windows Server 2012 ou posterior e o Windows 8 ou posterior com o IIS/IIS Express 8 ou posterior são compatíveis com o protocolo WebSocket.

> [!NOTE]
> WebSockets estão sempre habilitados ao usar o IIS Express.

### <a name="enabling-websockets-on-iis"></a>Habilitar WebSockets no IIS

Para habilitar o suporte para o protocolo WebSocket no Windows Server 2012 ou posterior:

> [!NOTE]
> Estas etapas não são necessárias ao usar o IIS Express

1. Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**.
1. Selecione **Instalação baseada em função ou em recurso**. Selecione **Avançar**.
1. Selecione o servidor apropriado (o servidor local é selecionado por padrão). Selecione **Avançar**.
1. Expanda **Servidor Web (IIS)** na árvore **Funções**, expanda **Servidor Web** e, em seguida, expanda **Desenvolvimento de Aplicativos**.
1. Selecione o **Protocolo WebSocket**. Selecione **Avançar**.
1. Se não forem necessários recursos adicionais, selecione **Avançar**.
1. Selecione **Instalar**.
1. Quando a instalação for concluída, selecione **Fechar** para sair do assistente.

Para habilitar o suporte para o protocolo WebSocket no Windows 8 ou posterior:

> [!NOTE]
> Estas etapas não são necessárias ao usar o IIS Express

1. Navegue até o **painel de controle**  >  **programas**  >  **programas e recursos**  >  **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).
1. Abra os seguintes nós: **serviços de informações da Internet** os  >  recursos de desenvolvimento de aplicativos **dos serviços World Wide Webs**  >  .
1. Selecione o recurso **Protocolo WebSocket**. Selecione **OK**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Desabilite o WebSocket ao usar o socket.io no Node.js

Se estiver usando o suporte WebSocket no [Socket.Io](https://socket.io/) no [Node.js](https://nodejs.org/), desabilite o módulo WebSocket padrão do IIS usando o `webSocket` elemento em *web.config* ou *applicationHost.config*. Se essa etapa não for executada, o módulo WebSocket do IIS tentará lidar com a comunicação WebSocket em vez de Node.js e o aplicativo.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) que acompanha este artigo é um aplicativo de eco. Ele tem uma página da Web que faz conexões WebSocket e o servidor reenvia para o cliente todas as mensagens recebidas. Execute o aplicativo em um prompt de comando (ele não está configurado para execução no Visual Studio com o IIS Express) e navegue para http://localhost:5000. A página da Web exibe o status de conexão no canto superior esquerdo:

![Estado inicial da página da Web](websockets/_static/start.png)

Selecione **Conectar** para enviar uma solicitação WebSocket para a URL exibida. Insira uma mensagem de teste e selecione **Enviar**. Quando terminar, selecione **Fechar Soquete**. A seção **Log de Comunicação** relata cada ação de abertura, envio e fechamento conforme ela ocorre.

![Estado inicial da página da Web](websockets/_static/end.png)

