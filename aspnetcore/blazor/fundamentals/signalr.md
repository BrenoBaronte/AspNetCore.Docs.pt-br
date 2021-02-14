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
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="12a31-103">Diretrizes de ASP.NET Core Blazor SignalR</span><span class="sxs-lookup"><span data-stu-id="12a31-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="12a31-104">Para obter diretrizes gerais sobre a configuração do ASP.NET Core SignalR , consulte os tópicos na <xref:signalr/introduction> área da documentação do.</span><span class="sxs-lookup"><span data-stu-id="12a31-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="12a31-105">Para configurar SignalR [o adicionado a uma Blazor WebAssembly solução hospedada](xref:tutorials/signalr-blazor), consulte <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="12a31-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="12a31-106">Opções do manipulador de circuito</span><span class="sxs-lookup"><span data-stu-id="12a31-106">Circuit handler options</span></span>

<span data-ttu-id="12a31-107">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="12a31-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="12a31-108">Configure o Blazor Server circuito com o <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> mostrado na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="12a31-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="12a31-109">Opção</span><span class="sxs-lookup"><span data-stu-id="12a31-109">Option</span></span> | <span data-ttu-id="12a31-110">Padrão</span><span class="sxs-lookup"><span data-stu-id="12a31-110">Default</span></span> | <span data-ttu-id="12a31-111">Descrição</span><span class="sxs-lookup"><span data-stu-id="12a31-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="12a31-112">Envie mensagens de exceção detalhadas ao JavaScript quando uma exceção sem tratamento ocorrer no circuito ou quando uma invocação de método .NET por meio da interoperabilidade JS resultar em uma exceção.</span><span class="sxs-lookup"><span data-stu-id="12a31-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="12a31-113">100</span><span class="sxs-lookup"><span data-stu-id="12a31-113">100</span></span> | <span data-ttu-id="12a31-114">Número máximo de circuitos desconectados que um determinado servidor mantém na memória por vez.</span><span class="sxs-lookup"><span data-stu-id="12a31-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="12a31-115">3 minutos</span><span class="sxs-lookup"><span data-stu-id="12a31-115">3 minutes</span></span> | <span data-ttu-id="12a31-116">Quantidade máxima de tempo que um circuito desconectado é mantido na memória antes de ser interrompido.</span><span class="sxs-lookup"><span data-stu-id="12a31-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="12a31-117">1 minuto</span><span class="sxs-lookup"><span data-stu-id="12a31-117">1 minute</span></span> | <span data-ttu-id="12a31-118">Quantidade máxima de tempo que o servidor espera antes de atingir o tempo limite de uma invocação de função JavaScript assíncrona.</span><span class="sxs-lookup"><span data-stu-id="12a31-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="12a31-119">10</span><span class="sxs-lookup"><span data-stu-id="12a31-119">10</span></span> | <span data-ttu-id="12a31-120">Número máximo de lotes de renderização não confirmados que o servidor mantém na memória por circuito em um determinado momento para dar suporte à reconexão robusta.</span><span class="sxs-lookup"><span data-stu-id="12a31-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="12a31-121">Depois de atingir o limite, o servidor para de produzir novos lotes de renderização até que um ou mais lotes tenham sido confirmados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="12a31-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="12a31-122">Configure as opções em `Startup.ConfigureServices` com um delegado de opções para <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> .</span><span class="sxs-lookup"><span data-stu-id="12a31-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="12a31-123">O exemplo a seguir atribui os valores de opção padrão mostrados na tabela anterior:</span><span class="sxs-lookup"><span data-stu-id="12a31-123">The following example assigns the default option values shown in the preceding table:</span></span>

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

<span data-ttu-id="12a31-124">Para configurar o <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> com <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> .</span><span class="sxs-lookup"><span data-stu-id="12a31-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="12a31-125">Para obter descrições de opções, consulte <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="12a31-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="12a31-126">O exemplo a seguir atribui os valores de opção padrão:</span><span class="sxs-lookup"><span data-stu-id="12a31-126">The following example assigns the default option values:</span></span>

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="12a31-127">SignalR negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="12a31-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="12a31-128">*Esta seção aplica-se a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="12a31-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="12a31-129">Para configurar SignalR o cliente subjacente do para enviar credenciais, como os cookie cabeçalhos de autenticação s ou http:</span><span class="sxs-lookup"><span data-stu-id="12a31-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="12a31-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="12a31-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="12a31-131">Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:</span><span class="sxs-lookup"><span data-stu-id="12a31-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="12a31-132">Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="12a31-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="12a31-133">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="12a31-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="12a31-134">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="12a31-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="12a31-135">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="12a31-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="12a31-136">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="12a31-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="12a31-137">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="12a31-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="12a31-138">Adicione o seguinte à folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="12a31-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="12a31-139">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="12a31-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="12a31-140">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="12a31-140">CSS class</span></span>                       | <span data-ttu-id="12a31-141">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="12a31-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="12a31-142">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="12a31-142">A lost connection.</span></span> <span data-ttu-id="12a31-143">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="12a31-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="12a31-144">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="12a31-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="12a31-145">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="12a31-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="12a31-146">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="12a31-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="12a31-147">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="12a31-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="12a31-148">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="12a31-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="12a31-149">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="12a31-149">Reconnection rejected.</span></span> <span data-ttu-id="12a31-150">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="12a31-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="12a31-151">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="12a31-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="12a31-152">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="12a31-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="12a31-153">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="12a31-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="12a31-154">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="12a31-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="12a31-155">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="12a31-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="12a31-156">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="12a31-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="12a31-157">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="12a31-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="12a31-158">*Esta seção se aplica ao Hosted Blazor WebAssembly e ao Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="12a31-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="12a31-159">Blazor os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor.</span><span class="sxs-lookup"><span data-stu-id="12a31-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="12a31-160">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="12a31-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="12a31-161">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="12a31-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="12a31-162">Blazor Server os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="12a31-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="12a31-163">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="12a31-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="12a31-164">Inicializar o Blazor circuito</span><span class="sxs-lookup"><span data-stu-id="12a31-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="12a31-165">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="12a31-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="12a31-166">Configure o início manual do Blazor Server [ SignalR circuito](xref:blazor/hosting-models#circuits) de um aplicativo no `Pages/_Host.cshtml` arquivo:</span><span class="sxs-lookup"><span data-stu-id="12a31-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="12a31-167">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="12a31-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="12a31-168">Coloque um script que chame `Blazor.start` após a `blazor.server.js` marca do script e dentro da marca de fechamento `</body>` .</span><span class="sxs-lookup"><span data-stu-id="12a31-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="12a31-169">Quando `autostart` o é desabilitado, qualquer aspecto do aplicativo que não dependa do circuito funciona normalmente.</span><span class="sxs-lookup"><span data-stu-id="12a31-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="12a31-170">Por exemplo, o roteamento do lado do cliente está operacional.</span><span class="sxs-lookup"><span data-stu-id="12a31-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="12a31-171">No entanto, qualquer aspecto que dependa do circuito não estará operacional até que `Blazor.start` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="12a31-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="12a31-172">O comportamento do aplicativo é imprevisível sem um circuito estabelecido.</span><span class="sxs-lookup"><span data-stu-id="12a31-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="12a31-173">Por exemplo, os métodos de componente não são executados enquanto o circuito é desconectado.</span><span class="sxs-lookup"><span data-stu-id="12a31-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="12a31-174">Inicializar Blazor quando o documento estiver pronto</span><span class="sxs-lookup"><span data-stu-id="12a31-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="12a31-175">Para inicializar o Blazor aplicativo quando o documento estiver pronto:</span><span class="sxs-lookup"><span data-stu-id="12a31-175">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="12a31-176">Cadeia para o `Promise` que resulta de um início manual</span><span class="sxs-lookup"><span data-stu-id="12a31-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="12a31-177">Para executar tarefas adicionais, como a inicialização de interoperabilidade do JS, use `then` para encadear para o `Promise` que resulta de um Blazor início do aplicativo manual:</span><span class="sxs-lookup"><span data-stu-id="12a31-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-signalr-client"></a><span data-ttu-id="12a31-178">Configurar o SignalR cliente</span><span class="sxs-lookup"><span data-stu-id="12a31-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="12a31-179">Registro em log</span><span class="sxs-lookup"><span data-stu-id="12a31-179">Logging</span></span>

<span data-ttu-id="12a31-180">Para configurar SignalR o log do cliente, passe um objeto de configuração ( `configureSignalR` ) que chame `configureLogging` com o nível de log no construtor do cliente:</span><span class="sxs-lookup"><span data-stu-id="12a31-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="12a31-181">No exemplo anterior, `information` é equivalente a um nível de log de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="12a31-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="12a31-182">Modificar o manipulador de reconexão</span><span class="sxs-lookup"><span data-stu-id="12a31-182">Modify the reconnection handler</span></span>

<span data-ttu-id="12a31-183">Os eventos de conexão de circuito do manipulador de reconexão podem ser modificados para comportamentos personalizados, como:</span><span class="sxs-lookup"><span data-stu-id="12a31-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="12a31-184">Para notificar o usuário se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="12a31-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="12a31-185">Para executar o registro em log (do cliente) quando um circuito é conectado.</span><span class="sxs-lookup"><span data-stu-id="12a31-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="12a31-186">Para modificar os eventos de conexão, registre os retornos de chamada para as seguintes alterações de conexão:</span><span class="sxs-lookup"><span data-stu-id="12a31-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="12a31-187">Conexões descartadas usam `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="12a31-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="12a31-188">Conexões estabelecidas/restabelecidas usam `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="12a31-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="12a31-189">**Ambos** `onConnectionDown` e `onConnectionUp` deve ser especificado:</span><span class="sxs-lookup"><span data-stu-id="12a31-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="12a31-190">Ajustar a contagem de repetição de reconexão e o intervalo</span><span class="sxs-lookup"><span data-stu-id="12a31-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="12a31-191">Para ajustar a contagem de repetição de reconexão e o intervalo, defina o número de repetições ( `maxRetries` ) e o período em milissegundos permitidos para cada tentativa de repetição ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="12a31-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="12a31-192">Ocultar ou substituir a exibição de reconexão</span><span class="sxs-lookup"><span data-stu-id="12a31-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="12a31-193">Para ocultar a exibição de reconexão, defina o manipulador de reconexão `_reconnectionDisplay` como um objeto vazio ( `{}` ou `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="12a31-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="12a31-194">Para substituir a exibição da reconexão, defina `_reconnectionDisplay` no exemplo anterior como o elemento para exibição:</span><span class="sxs-lookup"><span data-stu-id="12a31-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="12a31-195">O espaço reservado `{ELEMENT ID}` é a ID do elemento HTML a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="12a31-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="12a31-196">Personalize o atraso antes da exibição da reconexão aparecer definindo a `transition-delay` propriedade no CSS do aplicativo ( `wwwroot/css/site.css` ) para o elemento modal.</span><span class="sxs-lookup"><span data-stu-id="12a31-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="12a31-197">O exemplo a seguir define o atraso de transição de 500 MS (padrão) para 1.000 MS (1 segundo):</span><span class="sxs-lookup"><span data-stu-id="12a31-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="12a31-198">Desconectar o Blazor circuito do cliente</span><span class="sxs-lookup"><span data-stu-id="12a31-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="12a31-199">Por padrão, um Blazor circuito é desconectado quando o [ `unload` evento da página](https://developer.mozilla.org/docs/Web/API/Window/unload_event) é disparado.</span><span class="sxs-lookup"><span data-stu-id="12a31-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="12a31-200">Para desconectar o circuito para outros cenários no cliente, invoque `Blazor.disconnect` no manipulador de eventos apropriado.</span><span class="sxs-lookup"><span data-stu-id="12a31-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="12a31-201">No exemplo a seguir, o circuito é desconectado quando a página está oculta ([ `pagehide` evento](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="12a31-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="12a31-202">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="12a31-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="12a31-203">Blazor Server eventos de reconexão e eventos de ciclo de vida do componente</span><span class="sxs-lookup"><span data-stu-id="12a31-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
