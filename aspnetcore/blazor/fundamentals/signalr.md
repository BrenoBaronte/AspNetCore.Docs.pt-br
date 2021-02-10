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
ms.openlocfilehash: 31cd68baa0fbc773f09fe3a1b37091b2dfc0b0a0
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100110003"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="42eb5-103">Diretrizes de ASP.NET Core Blazor SignalR</span><span class="sxs-lookup"><span data-stu-id="42eb5-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="42eb5-104">Por [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="42eb5-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="42eb5-105">Para obter diretrizes gerais sobre a configuração do ASP.NET Core SignalR , consulte os tópicos na <xref:signalr/introduction> área da documentação do.</span><span class="sxs-lookup"><span data-stu-id="42eb5-105">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="42eb5-106">Para configurar SignalR [o adicionado a uma Blazor WebAssembly solução hospedada](xref:tutorials/signalr-blazor), consulte <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="42eb5-106">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="42eb5-107">Opções do manipulador de circuito</span><span class="sxs-lookup"><span data-stu-id="42eb5-107">Circuit handler options</span></span>

<span data-ttu-id="42eb5-108">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="42eb5-108">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="42eb5-109">Configure o Blazor Server circuito com o <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> mostrado na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="42eb5-109">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="42eb5-110">Opção</span><span class="sxs-lookup"><span data-stu-id="42eb5-110">Option</span></span> | <span data-ttu-id="42eb5-111">Padrão</span><span class="sxs-lookup"><span data-stu-id="42eb5-111">Default</span></span> | <span data-ttu-id="42eb5-112">Descrição</span><span class="sxs-lookup"><span data-stu-id="42eb5-112">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="42eb5-113">Envie mensagens de exceção detalhadas ao JavaScript quando uma exceção sem tratamento ocorrer no circuito ou quando uma invocação de método .NET por meio da interoperabilidade JS resultar em uma exceção.</span><span class="sxs-lookup"><span data-stu-id="42eb5-113">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="42eb5-114">100</span><span class="sxs-lookup"><span data-stu-id="42eb5-114">100</span></span> | <span data-ttu-id="42eb5-115">Número máximo de circuitos desconectados que um determinado servidor mantém na memória por vez.</span><span class="sxs-lookup"><span data-stu-id="42eb5-115">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="42eb5-116">3 minutos</span><span class="sxs-lookup"><span data-stu-id="42eb5-116">3 minutes</span></span> | <span data-ttu-id="42eb5-117">Quantidade máxima de tempo que um circuito desconectado é mantido na memória antes de ser interrompido.</span><span class="sxs-lookup"><span data-stu-id="42eb5-117">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="42eb5-118">1 minuto</span><span class="sxs-lookup"><span data-stu-id="42eb5-118">1 minute</span></span> | <span data-ttu-id="42eb5-119">Quantidade máxima de tempo que o servidor espera antes de atingir o tempo limite de uma invocação de função JavaScript assíncrona.</span><span class="sxs-lookup"><span data-stu-id="42eb5-119">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="42eb5-120">10</span><span class="sxs-lookup"><span data-stu-id="42eb5-120">10</span></span> | <span data-ttu-id="42eb5-121">Número máximo de lotes de renderização não confirmados que o servidor mantém na memória por circuito em um determinado momento para dar suporte à reconexão robusta.</span><span class="sxs-lookup"><span data-stu-id="42eb5-121">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="42eb5-122">Depois de atingir o limite, o servidor para de produzir novos lotes de renderização até que um ou mais lotes tenham sido confirmados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="42eb5-122">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="42eb5-123">Configure as opções em `Startup.ConfigureServices` com um delegado de opções para <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> .</span><span class="sxs-lookup"><span data-stu-id="42eb5-123">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="42eb5-124">O exemplo a seguir atribui os valores de opção padrão mostrados na tabela anterior:</span><span class="sxs-lookup"><span data-stu-id="42eb5-124">The following example assigns the default option values shown in the preceding table:</span></span>

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

<span data-ttu-id="42eb5-125">Para configurar o <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> com <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> .</span><span class="sxs-lookup"><span data-stu-id="42eb5-125">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="42eb5-126">Para obter descrições de opções, consulte <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="42eb5-126">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="42eb5-127">O exemplo a seguir atribui os valores de opção padrão:</span><span class="sxs-lookup"><span data-stu-id="42eb5-127">The following example assigns the default option values:</span></span>

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="42eb5-128">SignalR negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="42eb5-128">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="42eb5-129">*Esta seção aplica-se a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="42eb5-129">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="42eb5-130">Para configurar SignalR o cliente subjacente do para enviar credenciais, como os cookie cabeçalhos de autenticação s ou http:</span><span class="sxs-lookup"><span data-stu-id="42eb5-130">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="42eb5-131">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="42eb5-131">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="42eb5-132">Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:</span><span class="sxs-lookup"><span data-stu-id="42eb5-132">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="42eb5-133">Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="42eb5-133">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="42eb5-134">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="42eb5-134">Reflect the connection state in the UI</span></span>

<span data-ttu-id="42eb5-135">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="42eb5-135">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="42eb5-136">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="42eb5-136">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="42eb5-137">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="42eb5-137">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="42eb5-138">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="42eb5-138">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="42eb5-139">Adicione o seguinte à folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="42eb5-139">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="42eb5-140">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="42eb5-140">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="42eb5-141">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="42eb5-141">CSS class</span></span>                       | <span data-ttu-id="42eb5-142">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="42eb5-142">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="42eb5-143">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="42eb5-143">A lost connection.</span></span> <span data-ttu-id="42eb5-144">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="42eb5-144">The client is attempting to reconnect.</span></span> <span data-ttu-id="42eb5-145">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="42eb5-145">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="42eb5-146">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="42eb5-146">An active connection is re-established to the server.</span></span> <span data-ttu-id="42eb5-147">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="42eb5-147">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="42eb5-148">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="42eb5-148">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="42eb5-149">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="42eb5-149">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="42eb5-150">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="42eb5-150">Reconnection rejected.</span></span> <span data-ttu-id="42eb5-151">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="42eb5-151">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="42eb5-152">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="42eb5-152">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="42eb5-153">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="42eb5-153">This connection state may result when:</span></span><ul><li><span data-ttu-id="42eb5-154">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="42eb5-154">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="42eb5-155">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="42eb5-155">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="42eb5-156">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="42eb5-156">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="42eb5-157">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="42eb5-157">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="42eb5-158">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="42eb5-158">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="42eb5-159">*Esta seção se aplica ao Hosted Blazor WebAssembly e ao Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="42eb5-159">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="42eb5-160">Blazor os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor.</span><span class="sxs-lookup"><span data-stu-id="42eb5-160">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="42eb5-161">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="42eb5-161">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="42eb5-162">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="42eb5-162">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="42eb5-163">Blazor Server os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="42eb5-163">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="42eb5-164">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="42eb5-164">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="42eb5-165">Inicializar o Blazor circuito</span><span class="sxs-lookup"><span data-stu-id="42eb5-165">Initialize the Blazor circuit</span></span>

<span data-ttu-id="42eb5-166">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="42eb5-166">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="42eb5-167">Configure o início manual do Blazor Server [ SignalR circuito](xref:blazor/hosting-models#circuits) de um aplicativo no `Pages/_Host.cshtml` arquivo:</span><span class="sxs-lookup"><span data-stu-id="42eb5-167">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="42eb5-168">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="42eb5-168">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="42eb5-169">Coloque um script que chame `Blazor.start` após a `blazor.server.js` marca do script e dentro da marca de fechamento `</body>` .</span><span class="sxs-lookup"><span data-stu-id="42eb5-169">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="42eb5-170">Quando `autostart` o é desabilitado, qualquer aspecto do aplicativo que não dependa do circuito funciona normalmente.</span><span class="sxs-lookup"><span data-stu-id="42eb5-170">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="42eb5-171">Por exemplo, o roteamento do lado do cliente está operacional.</span><span class="sxs-lookup"><span data-stu-id="42eb5-171">For example, client-side routing is operational.</span></span> <span data-ttu-id="42eb5-172">No entanto, qualquer aspecto que dependa do circuito não estará operacional até que `Blazor.start` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="42eb5-172">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="42eb5-173">O comportamento do aplicativo é imprevisível sem um circuito estabelecido.</span><span class="sxs-lookup"><span data-stu-id="42eb5-173">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="42eb5-174">Por exemplo, os métodos de componente não são executados enquanto o circuito é desconectado.</span><span class="sxs-lookup"><span data-stu-id="42eb5-174">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="42eb5-175">Inicializar Blazor quando o documento estiver pronto</span><span class="sxs-lookup"><span data-stu-id="42eb5-175">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="42eb5-176">Para inicializar o Blazor aplicativo quando o documento estiver pronto:</span><span class="sxs-lookup"><span data-stu-id="42eb5-176">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="42eb5-177">Cadeia para o `Promise` que resulta de um início manual</span><span class="sxs-lookup"><span data-stu-id="42eb5-177">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="42eb5-178">Para executar tarefas adicionais, como a inicialização de interoperabilidade do JS, use `then` para encadear para o `Promise` que resulta de um Blazor início do aplicativo manual:</span><span class="sxs-lookup"><span data-stu-id="42eb5-178">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-signalr-client"></a><span data-ttu-id="42eb5-179">Configurar o SignalR cliente</span><span class="sxs-lookup"><span data-stu-id="42eb5-179">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="42eb5-180">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="42eb5-180">Logging</span></span>

<span data-ttu-id="42eb5-181">Para configurar SignalR o log do cliente, passe um objeto de configuração ( `configureSignalR` ) que chame `configureLogging` com o nível de log no construtor do cliente:</span><span class="sxs-lookup"><span data-stu-id="42eb5-181">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="42eb5-182">No exemplo anterior, `information` é equivalente a um nível de log de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="42eb5-182">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="42eb5-183">Modificar o manipulador de reconexão</span><span class="sxs-lookup"><span data-stu-id="42eb5-183">Modify the reconnection handler</span></span>

<span data-ttu-id="42eb5-184">Os eventos de conexão de circuito do manipulador de reconexão podem ser modificados para comportamentos personalizados, como:</span><span class="sxs-lookup"><span data-stu-id="42eb5-184">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="42eb5-185">Para notificar o usuário se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="42eb5-185">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="42eb5-186">Para executar o registro em log (do cliente) quando um circuito é conectado.</span><span class="sxs-lookup"><span data-stu-id="42eb5-186">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="42eb5-187">Para modificar os eventos de conexão, registre os retornos de chamada para as seguintes alterações de conexão:</span><span class="sxs-lookup"><span data-stu-id="42eb5-187">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="42eb5-188">Conexões descartadas usam `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="42eb5-188">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="42eb5-189">Conexões estabelecidas/restabelecidas usam `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="42eb5-189">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="42eb5-190">**Ambos** `onConnectionDown` e `onConnectionUp` deve ser especificado:</span><span class="sxs-lookup"><span data-stu-id="42eb5-190">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="42eb5-191">Ajustar a contagem de repetição de reconexão e o intervalo</span><span class="sxs-lookup"><span data-stu-id="42eb5-191">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="42eb5-192">Para ajustar a contagem de repetição de reconexão e o intervalo, defina o número de repetições ( `maxRetries` ) e o período em milissegundos permitidos para cada tentativa de repetição ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="42eb5-192">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="42eb5-193">Ocultar ou substituir a exibição de reconexão</span><span class="sxs-lookup"><span data-stu-id="42eb5-193">Hide or replace the reconnection display</span></span>

<span data-ttu-id="42eb5-194">Para ocultar a exibição de reconexão, defina o manipulador de reconexão `_reconnectionDisplay` como um objeto vazio ( `{}` ou `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="42eb5-194">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="42eb5-195">Para substituir a exibição da reconexão, defina `_reconnectionDisplay` no exemplo anterior como o elemento para exibição:</span><span class="sxs-lookup"><span data-stu-id="42eb5-195">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="42eb5-196">O espaço reservado `{ELEMENT ID}` é a ID do elemento HTML a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="42eb5-196">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="42eb5-197">Personalize o atraso antes da exibição da reconexão aparecer definindo a `transition-delay` propriedade no CSS do aplicativo ( `wwwroot/css/site.css` ) para o elemento modal.</span><span class="sxs-lookup"><span data-stu-id="42eb5-197">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="42eb5-198">O exemplo a seguir define o atraso de transição de 500 MS (padrão) para 1.000 MS (1 segundo):</span><span class="sxs-lookup"><span data-stu-id="42eb5-198">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="42eb5-199">Desconectar o Blazor circuito do cliente</span><span class="sxs-lookup"><span data-stu-id="42eb5-199">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="42eb5-200">Por padrão, um Blazor circuito é desconectado quando o [ `unload` evento da página](https://developer.mozilla.org/docs/Web/API/Window/unload_event) é disparado.</span><span class="sxs-lookup"><span data-stu-id="42eb5-200">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="42eb5-201">Para desconectar o circuito para outros cenários no cliente, invoque `Blazor.disconnect` no manipulador de eventos apropriado.</span><span class="sxs-lookup"><span data-stu-id="42eb5-201">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="42eb5-202">No exemplo a seguir, o circuito é desconectado quando a página está oculta ([ `pagehide` evento](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="42eb5-202">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="42eb5-203">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="42eb5-203">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="42eb5-204">Blazor Server eventos de reconexão e eventos de ciclo de vida do componente</span><span class="sxs-lookup"><span data-stu-id="42eb5-204">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
