---
title: Diretrizes de ASP.NET Core Blazor SignalR
author: guardrex
description: Saiba como configurar e gerenciar Blazor SignalR conexões.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/signalr
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279865"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a>Diretrizes de ASP.NET Core Blazor SignalR

Para obter diretrizes gerais sobre a configuração do ASP.NET Core SignalR , consulte os tópicos na <xref:signalr/introduction> área da documentação do. Para configurar SignalR [o adicionado a uma Blazor WebAssembly solução hospedada](xref:tutorials/signalr-blazor), consulte <xref:signalr/configuration#configure-server-options> .

## <a name="circuit-handler-options"></a>Opções do manipulador de circuito

*Esta seção aplica-se a Blazor Server .*

Configure o Blazor Server circuito com o <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> mostrado na tabela a seguir.

| Opção | Padrão | Descrição |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | Envie mensagens de exceção detalhadas ao JavaScript quando uma exceção sem tratamento ocorrer no circuito ou quando uma invocação de método .NET por meio da interoperabilidade JS resultar em uma exceção. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 100 | Número máximo de circuitos desconectados que um determinado servidor mantém na memória por vez. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 3 minutos | Quantidade máxima de tempo que um circuito desconectado é mantido na memória antes de ser interrompido. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 1 minuto | Quantidade máxima de tempo que o servidor espera antes de atingir o tempo limite de uma invocação de função JavaScript assíncrona. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 10 | Número máximo de lotes de renderização não confirmados que o servidor mantém na memória por circuito em um determinado momento para dar suporte à reconexão robusta. Depois de atingir o limite, o servidor para de produzir novos lotes de renderização até que um ou mais lotes tenham sido confirmados pelo cliente. |

Configure as opções em `Startup.ConfigureServices` com um delegado de opções para <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> . O exemplo a seguir atribui os valores de opção padrão mostrados na tabela anterior:

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

Para configurar o <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> com <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> . Para obter descrições de opções, consulte <xref:signalr/configuration#configure-server-options> . O exemplo a seguir atribui os valores de opção padrão:

```csharp
using System;

...

services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR negociação entre origens para autenticação

*Esta seção aplica-se a Blazor WebAssembly .*

Para configurar SignalR o cliente subjacente do para enviar credenciais, como os cookie cabeçalhos de autenticação s ou http:

* Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Refletir o estado da conexão na interface do usuário

*Esta seção aplica-se a Blazor Server .*

Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário receberá a opção de tentar novamente.

Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` Razor página:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Adicione o seguinte à folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ):

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.

| Classe CSS                       | Indicando&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Uma conexão perdida. O cliente está tentando se reconectar. Mostrar o modal. |
| `components-reconnect-hide`     | Uma conexão ativa é restabelecida com o servidor. Ocultar a janela restrita. |
| `components-reconnect-failed`   | Falha na reconexão, provavelmente devido a uma falha de rede. Para tentar a reconexão, chame `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Reconexão rejeitada. O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido. Para recarregar o aplicativo, chame `location.reload()` . Esse estado de conexão pode resultar quando:<ul><li>Ocorre uma falha no circuito do lado do servidor.</li><li>O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário. As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</li><li>O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</li></ul> |

## <a name="render-mode"></a>Modo de renderização

::: moniker range=">= aspnetcore-5.0"

*Esta seção se aplica ao Hosted Blazor WebAssembly e ao Blazor Server .*

Blazor os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor. Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

*Esta seção aplica-se a Blazor Server .*

Blazor Server os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a>Inicializar o Blazor circuito

*Esta seção aplica-se a Blazor Server .*

Configure o início manual do Blazor Server [ SignalR circuito](xref:blazor/hosting-models#circuits) de um aplicativo no `Pages/_Host.cshtml` arquivo:

* Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.
* Coloque um script que chame `Blazor.start` após a `blazor.server.js` marca do script e dentro da marca de fechamento `</body>` .

Quando `autostart` o é desabilitado, qualquer aspecto do aplicativo que não dependa do circuito funciona normalmente. Por exemplo, o roteamento do lado do cliente está operacional. No entanto, qualquer aspecto que dependa do circuito não estará operacional até que `Blazor.start` seja chamado. O comportamento do aplicativo é imprevisível sem um circuito estabelecido. Por exemplo, os métodos de componente não são executados enquanto o circuito é desconectado.

### <a name="initialize-blazor-when-the-document-is-ready"></a>Inicializar Blazor quando o documento estiver pronto

Para inicializar o Blazor aplicativo quando o documento estiver pronto:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>Cadeia para o `Promise` que resulta de um início manual

Para executar tarefas adicionais, como a inicialização de interoperabilidade do JS, use `then` para encadear para o `Promise` que resulta de um Blazor início do aplicativo manual:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-signalr-client"></a>Configurar o SignalR cliente

#### <a name="logging"></a>Registro em log

Para configurar SignalR o log do cliente, passe um objeto de configuração ( `configureSignalR` ) que chame `configureLogging` com o nível de log no construtor do cliente:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

No exemplo anterior, `information` é equivalente a um nível de log de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Modificar o manipulador de reconexão

Os eventos de conexão de circuito do manipulador de reconexão podem ser modificados para comportamentos personalizados, como:

* Para notificar o usuário se a conexão for descartada.
* Para executar o registro em log (do cliente) quando um circuito é conectado.

Para modificar os eventos de conexão, registre os retornos de chamada para as seguintes alterações de conexão:

* Conexões descartadas usam `onConnectionDown` .
* Conexões estabelecidas/restabelecidas usam `onConnectionUp` .

**Ambos** `onConnectionDown` e `onConnectionUp` deve ser especificado:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Ajustar a contagem de repetição de reconexão e o intervalo

Para ajustar a contagem de repetição de reconexão e o intervalo, defina o número de repetições ( `maxRetries` ) e o período em milissegundos permitidos para cada tentativa de repetição ( `retryIntervalMilliseconds` ):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a>Ocultar ou substituir a exibição de reconexão

Para ocultar a exibição de reconexão, defina o manipulador de reconexão `_reconnectionDisplay` como um objeto vazio ( `{}` ou `new Object()` ):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

Para substituir a exibição da reconexão, defina `_reconnectionDisplay` no exemplo anterior como o elemento para exibição:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

O espaço reservado `{ELEMENT ID}` é a ID do elemento HTML a ser exibido.

::: moniker range=">= aspnetcore-5.0"

Personalize o atraso antes da exibição da reconexão aparecer definindo a `transition-delay` propriedade no CSS do aplicativo ( `wwwroot/css/site.css` ) para o elemento modal. O exemplo a seguir define o atraso de transição de 500 MS (padrão) para 1.000 MS (1 segundo):

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a>Desconectar o Blazor circuito do cliente

Por padrão, um Blazor circuito é desconectado quando o [ `unload` evento da página](https://developer.mozilla.org/docs/Web/API/Window/unload_event) é disparado. Para desconectar o circuito para outros cenários no cliente, invoque `Blazor.disconnect` no manipulador de eventos apropriado. No exemplo a seguir, o circuito é desconectado quando a página está oculta ([ `pagehide` evento](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [Blazor Server eventos de reconexão e eventos de ciclo de vida do componente](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
