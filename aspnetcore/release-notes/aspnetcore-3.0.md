---
title: O que há de novo no ASP.NET Core 3,0
author: rick-anderson
description: Saiba mais sobre os novos recursos do ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: 161d6aeefe1882b86ce97cdcfa7e5b23048693f0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217499"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="79583-103">O que há de novo no ASP.NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="79583-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="79583-104">Este artigo destaca as alterações mais significativas no ASP.NET Core 3,0 com links para a documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="79583-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

<span data-ttu-id="79583-105">Blazor é uma nova estrutura no ASP.NET Core para a criação de interface do usuário da Web interativa do lado do cliente com o .NET:</span><span class="sxs-lookup"><span data-stu-id="79583-105">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="79583-106">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="79583-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="79583-107">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.</span><span class="sxs-lookup"><span data-stu-id="79583-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="79583-108">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="79583-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="79583-109">Blazor cenários com suporte do Framework:</span><span class="sxs-lookup"><span data-stu-id="79583-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="79583-110">Componentes de interface do usuário reutilizáveis ( Razor componentes)</span><span class="sxs-lookup"><span data-stu-id="79583-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="79583-111">Roteamento do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="79583-111">Client-side routing</span></span>
* <span data-ttu-id="79583-112">Layouts de componente</span><span class="sxs-lookup"><span data-stu-id="79583-112">Component layouts</span></span>
* <span data-ttu-id="79583-113">Suporte para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="79583-113">Support for dependency injection</span></span>
* <span data-ttu-id="79583-114">Formulários e validação</span><span class="sxs-lookup"><span data-stu-id="79583-114">Forms and validation</span></span>
* <span data-ttu-id="79583-115">Compilar bibliotecas de componentes com Razor bibliotecas de classes</span><span class="sxs-lookup"><span data-stu-id="79583-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="79583-116">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="79583-116">JavaScript interop</span></span>

<span data-ttu-id="79583-117">Para obter mais informações, consulte <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="79583-117">For more information, see <xref:blazor/index>.</span></span>

### Blazor Server

<span data-ttu-id="79583-118">Blazor dissocia a lógica de renderização do componente de como as atualizações da interface do usuário são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="79583-118">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="79583-119">Blazor Server fornece suporte para hospedar Razor componentes no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="79583-119">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="79583-120">As atualizações da interface do usuário são manipuladas em uma SignalR conexão.</span><span class="sxs-lookup"><span data-stu-id="79583-120">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="79583-121">Blazor Server tem suporte no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="79583-121">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="no-locblazor-webassembly-preview"></a><span data-ttu-id="79583-122">Blazor WebAssembly Apresentação</span><span class="sxs-lookup"><span data-stu-id="79583-122">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="79583-123">Blazor os aplicativos também podem ser executados diretamente no navegador usando um tempo de execução .NET baseado em Webassembly.</span><span class="sxs-lookup"><span data-stu-id="79583-123">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="79583-124">Blazor WebAssembly está em visualização e *não* tem suporte no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="79583-124">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="79583-125">Blazor WebAssembly terá suporte em uma versão futura do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="79583-125">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="no-locrazor-components"></a><span data-ttu-id="79583-126">Componentes Razor</span><span class="sxs-lookup"><span data-stu-id="79583-126">Razor components</span></span>

<span data-ttu-id="79583-127">Blazor os aplicativos são criados a partir de componentes.</span><span class="sxs-lookup"><span data-stu-id="79583-127">Blazor apps are built from components.</span></span> <span data-ttu-id="79583-128">Os componentes são partes independentes da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="79583-128">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="79583-129">Os componentes são classes normais do .NET que definem a lógica de renderização da interface do usuário e manipuladores de eventos do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="79583-129">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="79583-130">Você pode criar aplicativos Web interativos sofisticados sem JavaScript.</span><span class="sxs-lookup"><span data-stu-id="79583-130">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="79583-131">Os componentes no Blazor normalmente são criados usando Razor a sintaxe, uma mistura natural de HTML e C#.</span><span class="sxs-lookup"><span data-stu-id="79583-131">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="79583-132">Razor os componentes são semelhantes às Razor páginas e às exibições do MVC que ambos usam Razor .</span><span class="sxs-lookup"><span data-stu-id="79583-132">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="79583-133">Ao contrário de páginas e exibições, que se baseiam em um modelo de solicitação-resposta, os componentes são usados especificamente para manipular a composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="79583-133">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="79583-134">gRPC</span><span class="sxs-lookup"><span data-stu-id="79583-134">gRPC</span></span>

<span data-ttu-id="79583-135">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="79583-135">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="79583-136">É uma estrutura popular de RPC de alto desempenho (chamada de procedimento remoto).</span><span class="sxs-lookup"><span data-stu-id="79583-136">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="79583-137">Oferece uma abordagem de contrato conceituada para o desenvolvimento de API.</span><span class="sxs-lookup"><span data-stu-id="79583-137">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="79583-138">O usa tecnologias modernas, como:</span><span class="sxs-lookup"><span data-stu-id="79583-138">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="79583-139">HTTP/2 para transporte.</span><span class="sxs-lookup"><span data-stu-id="79583-139">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="79583-140">Buffers de protocolo como linguagem de descrição de interface.</span><span class="sxs-lookup"><span data-stu-id="79583-140">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="79583-141">Formato de serialização binária.</span><span class="sxs-lookup"><span data-stu-id="79583-141">Binary serialization format.</span></span>
* <span data-ttu-id="79583-142">Fornece recursos como:</span><span class="sxs-lookup"><span data-stu-id="79583-142">Provides features such as:</span></span>

  * <span data-ttu-id="79583-143">Autenticação</span><span class="sxs-lookup"><span data-stu-id="79583-143">Authentication</span></span>
  * <span data-ttu-id="79583-144">Fluxo bidirecional e controle de fluxo.</span><span class="sxs-lookup"><span data-stu-id="79583-144">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="79583-145">Cancelamento e tempos limite.</span><span class="sxs-lookup"><span data-stu-id="79583-145">Cancellation and timeouts.</span></span>

<span data-ttu-id="79583-146">a funcionalidade gRPC no ASP.NET Core 3,0 inclui:</span><span class="sxs-lookup"><span data-stu-id="79583-146">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="79583-147">[Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): uma estrutura de ASP.NET Core para hospedar serviços Grpc.</span><span class="sxs-lookup"><span data-stu-id="79583-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="79583-148">o gRPC em ASP.NET Core integra-se com recursos de ASP.NET Core padrão, como registro em log, injeção de dependência (DI), autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="79583-148">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="79583-149">[Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client): um cliente Grpc para .NET Core que se baseia no conhecido `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="79583-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="79583-150">[Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): Grpc a integração de cliente com o `HttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="79583-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="79583-151">Para obter mais informações, consulte <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="79583-151">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="79583-152">Consulte [Atualizar SignalR código](xref:migration/22-to-30#signalr) para obter instruções de migração.</span><span class="sxs-lookup"><span data-stu-id="79583-152">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="79583-153">SignalR Agora usa `System.Text.Json` para serializar/desserializar mensagens JSON.</span><span class="sxs-lookup"><span data-stu-id="79583-153">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="79583-154">Consulte [mudar para Newtonsoft.Jsem](xref:migration/22-to-30#switch-to-newtonsoftjson) para obter instruções para restaurar o `Newtonsoft.Json` serializador baseado em.</span><span class="sxs-lookup"><span data-stu-id="79583-154">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="79583-155">Nos clientes JavaScript e .NET para SignalR , o suporte foi adicionado para reconexão automática.</span><span class="sxs-lookup"><span data-stu-id="79583-155">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="79583-156">Por padrão, o cliente tenta se reconectar imediatamente e tentar novamente após 2, 10 e 30 segundos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="79583-156">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="79583-157">Se o cliente reconectar-se com êxito, ele receberá uma nova ID de conexão.</span><span class="sxs-lookup"><span data-stu-id="79583-157">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="79583-158">A reconexão automática é opcional:</span><span class="sxs-lookup"><span data-stu-id="79583-158">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="79583-159">Os intervalos de reconexão podem ser especificados passando uma matriz de durações baseadas em milissegundos:</span><span class="sxs-lookup"><span data-stu-id="79583-159">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="79583-160">Uma implementação personalizada pode ser passada para controle total dos intervalos de reconexão.</span><span class="sxs-lookup"><span data-stu-id="79583-160">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="79583-161">Se a reconexão falhar após o último intervalo de reconexão:</span><span class="sxs-lookup"><span data-stu-id="79583-161">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="79583-162">O cliente considera que a conexão está offline.</span><span class="sxs-lookup"><span data-stu-id="79583-162">The client considers the connection is offline.</span></span>
* <span data-ttu-id="79583-163">O cliente para de tentar se reconectar.</span><span class="sxs-lookup"><span data-stu-id="79583-163">The client stops trying to reconnect.</span></span>

<span data-ttu-id="79583-164">Durante as tentativas de reconexão, atualize a interface de usuário do aplicativo para notificar o usuário de que a reconexão está sendo tentada.</span><span class="sxs-lookup"><span data-stu-id="79583-164">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="79583-165">Para fornecer comentários da interface do usuário quando a conexão é interrompida, a SignalR API do cliente foi expandida para incluir os seguintes manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="79583-165">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="79583-166">`onreconnecting`: Dá aos desenvolvedores uma oportunidade para desabilitar a interface do usuário ou para permitir que os usuários saibam que o aplicativo está offline.</span><span class="sxs-lookup"><span data-stu-id="79583-166">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="79583-167">`onreconnected`: Dá aos desenvolvedores uma oportunidade de atualizar a interface do usuário quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="79583-167">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="79583-168">O código a seguir usa `onreconnecting` para atualizar a interface do usuário ao tentar se conectar:</span><span class="sxs-lookup"><span data-stu-id="79583-168">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="79583-169">O código a seguir usa `onreconnected` para atualizar a interface do usuário na conexão:</span><span class="sxs-lookup"><span data-stu-id="79583-169">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="79583-170">SignalR o 3,0 e posterior fornece um recurso personalizado para manipuladores de autorização quando um método de Hub requer autorização.</span><span class="sxs-lookup"><span data-stu-id="79583-170">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="79583-171">O recurso é uma instância do `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="79583-171">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="79583-172">O `HubInvocationContext` inclui:</span><span class="sxs-lookup"><span data-stu-id="79583-172">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="79583-173">Nome do método de Hub que está sendo invocado.</span><span class="sxs-lookup"><span data-stu-id="79583-173">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="79583-174">Argumentos para o método de Hub.</span><span class="sxs-lookup"><span data-stu-id="79583-174">Arguments to the hub method.</span></span>

<span data-ttu-id="79583-175">Considere o exemplo a seguir de um aplicativo de sala de chat que permite a entrada de várias organizações por meio de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="79583-175">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="79583-176">Qualquer pessoa com um conta Microsoft pode entrar no chat, mas somente os membros da organização proprietária podem proibir os usuários ou exibir os históricos de chat dos usuários.</span><span class="sxs-lookup"><span data-stu-id="79583-176">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="79583-177">O aplicativo pode restringir determinada funcionalidade de usuários específicos.</span><span class="sxs-lookup"><span data-stu-id="79583-177">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="79583-178">No código anterior, `DomainRestrictedRequirement` serve como um personalizado `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="79583-178">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="79583-179">Como o `HubInvocationContext` parâmetro de recurso está sendo passado, a lógica interna pode:</span><span class="sxs-lookup"><span data-stu-id="79583-179">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="79583-180">Inspecione o contexto no qual o Hub está sendo chamado.</span><span class="sxs-lookup"><span data-stu-id="79583-180">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="79583-181">Tome decisões sobre como permitir que o usuário execute métodos de Hub individuais.</span><span class="sxs-lookup"><span data-stu-id="79583-181">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="79583-182">Os métodos de Hub individuais podem ser marcados com o nome da política que o código verifica em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="79583-182">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="79583-183">À medida que os clientes tentam chamar métodos de Hub individuais, o `DomainRestrictedRequirement` manipulador é executado e controla o acesso aos métodos.</span><span class="sxs-lookup"><span data-stu-id="79583-183">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="79583-184">Com base na maneira como os `DomainRestrictedRequirement` controles são acessados:</span><span class="sxs-lookup"><span data-stu-id="79583-184">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="79583-185">Todos os usuários conectados podem chamar o `SendMessage` método.</span><span class="sxs-lookup"><span data-stu-id="79583-185">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="79583-186">Somente os usuários que fizeram logon com um `@jabbr.net` endereço de email podem exibir os históricos dos usuários.</span><span class="sxs-lookup"><span data-stu-id="79583-186">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="79583-187">`bob42@jabbr.net`O só pode proibir usuários da sala de chat.</span><span class="sxs-lookup"><span data-stu-id="79583-187">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="79583-188">A criação da `DomainRestricted` política pode envolver:</span><span class="sxs-lookup"><span data-stu-id="79583-188">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="79583-189">Em *Startup.cs*, adicionando a nova política.</span><span class="sxs-lookup"><span data-stu-id="79583-189">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="79583-190">Forneça o `DomainRestrictedRequirement` requisito personalizado como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="79583-190">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="79583-191">Registrando `DomainRestricted` com o middleware de autorização.</span><span class="sxs-lookup"><span data-stu-id="79583-191">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="79583-192">SignalR hubs usam [Roteamento de ponto de extremidade](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="79583-192">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="79583-193">SignalR a conexão de Hub foi feita explicitamente anteriormente:</span><span class="sxs-lookup"><span data-stu-id="79583-193">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="79583-194">Na versão anterior, os desenvolvedores precisavam conectar controladores, Razor páginas e hubs em uma variedade de lugares.</span><span class="sxs-lookup"><span data-stu-id="79583-194">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="79583-195">A conexão explícita resulta em uma série de segmentos de roteamento quase idênticos:</span><span class="sxs-lookup"><span data-stu-id="79583-195">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="79583-196">SignalR 3,0 hubs podem ser roteados por meio de roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="79583-196">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="79583-197">Com o roteamento de ponto de extremidade, normalmente todo o roteamento pode ser configurado em `UseRouting` :</span><span class="sxs-lookup"><span data-stu-id="79583-197">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="79583-198">ASP.NET Core 3,0 SignalR adicionado:</span><span class="sxs-lookup"><span data-stu-id="79583-198">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="79583-199">Streaming de cliente para servidor.</span><span class="sxs-lookup"><span data-stu-id="79583-199">Client-to-server streaming.</span></span> <span data-ttu-id="79583-200">Com o streaming de cliente para servidor, os métodos do lado do servidor podem ter instâncias de um `IAsyncEnumerable<T>` ou `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="79583-200">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="79583-201">No exemplo de C# a seguir, o `UploadStream` método no Hub receberá um fluxo de cadeias de caracteres do cliente:</span><span class="sxs-lookup"><span data-stu-id="79583-201">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="79583-202">Os aplicativos cliente .NET podem passar uma `IAsyncEnumerable<T>` `ChannelReader<T>` instância ou como o `stream` argumento do `UploadStream` método de Hub acima.</span><span class="sxs-lookup"><span data-stu-id="79583-202">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="79583-203">Depois que o `for` loop for concluído e a função local sair, a conclusão do fluxo será enviada:</span><span class="sxs-lookup"><span data-stu-id="79583-203">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="79583-204">Os aplicativos cliente JavaScript usam o SignalR `Subject` (ou um [assunto RxJS](https://rxjs.dev/api/index/class/Subject)) para o `stream` argumento do `UploadStream` método de Hub acima.</span><span class="sxs-lookup"><span data-stu-id="79583-204">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="79583-205">O código JavaScript pode usar o `subject.next` método para lidar com cadeias de caracteres à medida que elas são capturadas e prontas para serem enviadas ao servidor.</span><span class="sxs-lookup"><span data-stu-id="79583-205">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="79583-206">Usando um código como os dois trechos anteriores, as experiências de streaming em tempo real podem ser criadas.</span><span class="sxs-lookup"><span data-stu-id="79583-206">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="79583-207">Nova serialização JSON</span><span class="sxs-lookup"><span data-stu-id="79583-207">New JSON serialization</span></span>

<span data-ttu-id="79583-208">O ASP.NET Core 3,0 agora usa <xref:System.Text.Json> por padrão para SERIALIZAÇÃO JSON:</span><span class="sxs-lookup"><span data-stu-id="79583-208">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="79583-209">Lê e grava JSON de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="79583-209">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="79583-210">É otimizado para texto UTF-8.</span><span class="sxs-lookup"><span data-stu-id="79583-210">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="79583-211">Geralmente um desempenho maior do que o `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="79583-211">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="79583-212">Para adicionar o Json.NET ao ASP.NET Core 3,0, consulte [adicionar Newtonsoft.Jssuporte ao formato JSON baseado em](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="79583-212">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-no-locrazor-directives"></a><span data-ttu-id="79583-213">Novas Razor diretivas</span><span class="sxs-lookup"><span data-stu-id="79583-213">New Razor directives</span></span>

<span data-ttu-id="79583-214">A lista a seguir contém novas Razor diretivas:</span><span class="sxs-lookup"><span data-stu-id="79583-214">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="79583-215">[`@attribute`](xref:mvc/views/razor#attribute): A `@attribute` diretiva aplica o atributo fornecido à classe da página ou exibição gerada.</span><span class="sxs-lookup"><span data-stu-id="79583-215">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="79583-216">Por exemplo, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="79583-216">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="79583-217">[`@implements`](xref:mvc/views/razor#implements): A `@implements` diretiva implementa uma interface para a classe gerada.</span><span class="sxs-lookup"><span data-stu-id="79583-217">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="79583-218">Por exemplo, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="79583-218">For example, `@implements IDisposable`.</span></span>

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="79583-219">IdentityO Server4 dá suporte à autenticação e autorização para APIs Web e SPAs</span><span class="sxs-lookup"><span data-stu-id="79583-219">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="79583-220">O ASP.NET Core 3,0 oferece autenticação em aplicativos de página única (SPAs) usando o suporte para autorização da API Web.</span><span class="sxs-lookup"><span data-stu-id="79583-220">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="79583-221">ASP.NET Core Identitypara autenticação e armazenamento de usuários é combinado com o [ Identity Server4](https://identityserver.io/) para implementar o OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="79583-221">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="79583-222">IdentityServer4 é uma estrutura de OpenID Connect e OAuth 2,0 para ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="79583-222">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="79583-223">Ele habilita os seguintes recursos de segurança:</span><span class="sxs-lookup"><span data-stu-id="79583-223">It enables the following security features:</span></span>

* <span data-ttu-id="79583-224">AaaS (autenticação como serviço)</span><span class="sxs-lookup"><span data-stu-id="79583-224">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="79583-225">Logon único/logoff (SSO) em vários tipos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="79583-225">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="79583-226">Controle de acesso para APIs</span><span class="sxs-lookup"><span data-stu-id="79583-226">Access control for APIs</span></span>
* <span data-ttu-id="79583-227">Gateway de Federação</span><span class="sxs-lookup"><span data-stu-id="79583-227">Federation Gateway</span></span>

<span data-ttu-id="79583-228">Para obter mais informações, consulte [a Identity documentação do Server4](http://docs.identityserver.io/en/latest/index.html) ou [autenticação e autorização para Spas](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="79583-228">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="79583-229">Autenticação de certificado e Kerberos</span><span class="sxs-lookup"><span data-stu-id="79583-229">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="79583-230">A autenticação de certificado requer:</span><span class="sxs-lookup"><span data-stu-id="79583-230">Certificate authentication requires:</span></span>

* <span data-ttu-id="79583-231">Configurando o servidor para aceitar certificados.</span><span class="sxs-lookup"><span data-stu-id="79583-231">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="79583-232">Adicionar o middleware de autenticação no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="79583-232">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="79583-233">Adicionando o serviço de autenticação de certificado no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="79583-233">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="79583-234">As opções de autenticação de certificado incluem a capacidade de:</span><span class="sxs-lookup"><span data-stu-id="79583-234">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="79583-235">Aceite certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="79583-235">Accept self-signed certificates.</span></span>
* <span data-ttu-id="79583-236">Verificar revogação de certificado.</span><span class="sxs-lookup"><span data-stu-id="79583-236">Check for certificate revocation.</span></span>
* <span data-ttu-id="79583-237">Verifique se o certificado proffered tem os sinalizadores de uso corretos.</span><span class="sxs-lookup"><span data-stu-id="79583-237">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="79583-238">Uma entidade de usuário padrão é construída a partir das propriedades do certificado.</span><span class="sxs-lookup"><span data-stu-id="79583-238">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="79583-239">A entidade de usuário contém um evento que permite complementar ou substituir a entidade de segurança.</span><span class="sxs-lookup"><span data-stu-id="79583-239">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="79583-240">Para obter mais informações, consulte <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="79583-240">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="79583-241">A [autenticação do Windows](/windows-server/security/windows-authentication/windows-authentication-overview) foi estendida para Linux e MacOS.</span><span class="sxs-lookup"><span data-stu-id="79583-241">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="79583-242">Em versões anteriores, a autenticação do Windows era limitada ao [IIS](xref:host-and-deploy/iis/index) e [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="79583-242">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="79583-243">No ASP.NET Core 3,0, o [Kestrel](xref:fundamentals/servers/kestrel) tem a capacidade de usar Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM no Windows](/windows-server/security/kerberos/ntlm-overview), Linux e MacOS para hosts ingressados no domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="79583-243">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="79583-244">O suporte do Kestrel desses esquemas de autenticação é fornecido pelo pacote [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) .</span><span class="sxs-lookup"><span data-stu-id="79583-244">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="79583-245">Assim como acontece com os outros serviços de autenticação, configure o aplicativo de autenticação de todo e configure o serviço:</span><span class="sxs-lookup"><span data-stu-id="79583-245">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="79583-246">Requisitos do host:</span><span class="sxs-lookup"><span data-stu-id="79583-246">Host requirements:</span></span>

* <span data-ttu-id="79583-247">Os hosts do Windows devem ter SPNs ( [nomes da entidade de serviço](/windows/win32/ad/service-principal-names) ) adicionados à conta de usuário que hospeda o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79583-247">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="79583-248">Os computadores Linux e macOS devem ser ingressados no domínio.</span><span class="sxs-lookup"><span data-stu-id="79583-248">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="79583-249">Os SPNs devem ser criados para o processo da Web.</span><span class="sxs-lookup"><span data-stu-id="79583-249">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="79583-250">Os [arquivos keytab](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) devem ser gerados e configurados no computador host.</span><span class="sxs-lookup"><span data-stu-id="79583-250">[Keytab files](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="79583-251">Para obter mais informações, consulte <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="79583-251">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="79583-252">Alterações de modelo</span><span class="sxs-lookup"><span data-stu-id="79583-252">Template changes</span></span>

<span data-ttu-id="79583-253">Os modelos de interface do usuário da Web ( Razor páginas, MVC com controlador e exibições) têm o seguinte removido:</span><span class="sxs-lookup"><span data-stu-id="79583-253">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="79583-254">A cookie interface do usuário de consentimento não está mais incluída.</span><span class="sxs-lookup"><span data-stu-id="79583-254">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="79583-255">Para habilitar o cookie recurso de consentimento em um ASP.NET Core aplicativo gerado pelo modelo 3,0, consulte <xref:security/gdpr> .</span><span class="sxs-lookup"><span data-stu-id="79583-255">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="79583-256">Os scripts e ativos estáticos relacionados agora são referenciados como arquivos locais em vez de usar CDNs.</span><span class="sxs-lookup"><span data-stu-id="79583-256">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="79583-257">Para obter mais informações, consulte [scripts e ativos estáticos relacionados agora são referenciados como arquivos locais em vez de usar o CDNs com base no ambiente atual (ASPNET/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="79583-257">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="79583-258">O modelo angular atualizado para usar o angular 8.</span><span class="sxs-lookup"><span data-stu-id="79583-258">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="79583-259">O Razor modelo de biblioteca de classes (RCL) usa como padrão o Razor desenvolvimento de componentes por padrão.</span><span class="sxs-lookup"><span data-stu-id="79583-259">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="79583-260">Uma nova opção de modelo no Visual Studio fornece suporte de modelo para páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="79583-260">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="79583-261">Ao criar um RCL a partir do modelo em um shell de comando, passe a `--support-pages-and-views` opção ( `dotnet new razorclasslib --support-pages-and-views` ).</span><span class="sxs-lookup"><span data-stu-id="79583-261">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="79583-262">Host Genérico</span><span class="sxs-lookup"><span data-stu-id="79583-262">Generic Host</span></span>

<span data-ttu-id="79583-263">Os modelos ASP.NET Core 3,0 usam <xref:fundamentals/host/generic-host> .</span><span class="sxs-lookup"><span data-stu-id="79583-263">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="79583-264">Versões anteriores usadas <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="79583-264">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="79583-265">O uso do host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ) fornece uma melhor integração de aplicativos ASP.NET Core com outros cenários de servidor que não são específicos da Web.</span><span class="sxs-lookup"><span data-stu-id="79583-265">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="79583-266">Para obter mais informações, consulte [HostBuilder substitui WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="79583-266">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="79583-267">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="79583-267">Host configuration</span></span>

<span data-ttu-id="79583-268">Antes do lançamento do ASP.NET Core 3,0, as variáveis de ambiente prefixadas com `ASPNETCORE_` foram carregadas para a configuração de host do host da Web.</span><span class="sxs-lookup"><span data-stu-id="79583-268">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="79583-269">No 3,0, `AddEnvironmentVariables` é usado para carregar variáveis de ambiente prefixadas com `DOTNET_` para a configuração de host com `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="79583-269">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="79583-270">Alterações na injeção de construtor de inicialização</span><span class="sxs-lookup"><span data-stu-id="79583-270">Changes to Startup constructor injection</span></span>

<span data-ttu-id="79583-271">O host genérico só dá suporte aos seguintes tipos para `Startup` injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="79583-271">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="79583-272">Todos os serviços ainda podem ser injetados diretamente como argumentos para o `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="79583-272">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="79583-273">Para obter mais informações, consulte [injeção de construtor de inicialização de restrict de host genérico (ASPNET/comunicados #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="79583-273">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="79583-274">Kestrel</span><span class="sxs-lookup"><span data-stu-id="79583-274">Kestrel</span></span>

* <span data-ttu-id="79583-275">A configuração do Kestrel foi atualizada para a migração para o host genérico.</span><span class="sxs-lookup"><span data-stu-id="79583-275">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="79583-276">No 3,0, o Kestrel é configurado no construtor de hosts da Web fornecido pelo `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="79583-276">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="79583-277">Os adaptadores de conexão foram removidos do Kestrel e substituídos pelo middleware de conexão, que é semelhante ao middleware HTTP no pipeline de ASP.NET Core, mas para conexões de nível inferior.</span><span class="sxs-lookup"><span data-stu-id="79583-277">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="79583-278">A camada de transporte Kestrel foi exposta como uma interface pública no `Connections.Abstractions` .</span><span class="sxs-lookup"><span data-stu-id="79583-278">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="79583-279">A ambiguidade entre cabeçalhos e trailers foi resolvida movendo os cabeçalhos à direita para uma nova coleção.</span><span class="sxs-lookup"><span data-stu-id="79583-279">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="79583-280">APIs de e/s síncronas, como `HttpRequest.Body.Read` , são uma fonte comum de consumo de thread que leva a falhas de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79583-280">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="79583-281">No 3,0, `AllowSynchronousIO` é desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="79583-281">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="79583-282">Para obter mais informações, consulte <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="79583-282">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="79583-283">HTTP/2 habilitado por padrão</span><span class="sxs-lookup"><span data-stu-id="79583-283">HTTP/2 enabled by default</span></span>

<span data-ttu-id="79583-284">O HTTP/2 é habilitado por padrão em Kestrel para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="79583-284">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="79583-285">O suporte a HTTP/2 para IIS ou HTTP.sys é habilitado quando suportado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="79583-285">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="79583-286">EventCounters na solicitação</span><span class="sxs-lookup"><span data-stu-id="79583-286">EventCounters on request</span></span>

<span data-ttu-id="79583-287">A hospedagem EventSource, `Microsoft.AspNetCore.Hosting` , emite os seguintes novos <xref:System.Diagnostics.Tracing.EventCounter> tipos relacionados a solicitações de entrada:</span><span class="sxs-lookup"><span data-stu-id="79583-287">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="79583-288">Roteamento de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="79583-288">Endpoint routing</span></span>

<span data-ttu-id="79583-289">O roteamento de ponto de extremidade, que permite que as estruturas (por exemplo, MVC) funcionem bem com o middleware, seja aprimorado:</span><span class="sxs-lookup"><span data-stu-id="79583-289">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="79583-290">A ordem do middleware e dos pontos de extremidade é configurável no pipeline de processamento de solicitação do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="79583-290">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="79583-291">Os pontos de extremidade e o middleware compõem-se bem com outras tecnologias baseadas em ASP.NET Core, como verificações de integridade.</span><span class="sxs-lookup"><span data-stu-id="79583-291">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="79583-292">Os pontos de extremidade podem implementar uma política, como CORS ou autorização, tanto no middleware quanto no MVC.</span><span class="sxs-lookup"><span data-stu-id="79583-292">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="79583-293">Filtros e atributos podem ser colocados em métodos em controladores.</span><span class="sxs-lookup"><span data-stu-id="79583-293">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="79583-294">Para obter mais informações, consulte <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="79583-294">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="79583-295">Verificações de Integridade</span><span class="sxs-lookup"><span data-stu-id="79583-295">Health Checks</span></span>

<span data-ttu-id="79583-296">As verificações de integridade usam o roteamento de ponto de extremidade com o host genérico.</span><span class="sxs-lookup"><span data-stu-id="79583-296">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="79583-297">No `Startup.Configure` , chame `MapHealthChecks` no construtor de ponto de extremidade com a URL do ponto de extremidade ou o caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="79583-297">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="79583-298">Os pontos de extremidade de verificações de integridade podem:</span><span class="sxs-lookup"><span data-stu-id="79583-298">Health Checks endpoints can:</span></span>

* <span data-ttu-id="79583-299">Especifique um ou mais hosts/portas permitidos.</span><span class="sxs-lookup"><span data-stu-id="79583-299">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="79583-300">Exigir autorização.</span><span class="sxs-lookup"><span data-stu-id="79583-300">Require authorization.</span></span>
* <span data-ttu-id="79583-301">Exigir CORS.</span><span class="sxs-lookup"><span data-stu-id="79583-301">Require CORS.</span></span>

<span data-ttu-id="79583-302">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="79583-302">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="79583-303">Pipes em HttpContext</span><span class="sxs-lookup"><span data-stu-id="79583-303">Pipes on HttpContext</span></span>

<span data-ttu-id="79583-304">Agora é possível ler o corpo da solicitação e gravar o corpo da resposta usando a <xref:System.IO.Pipelines> API.</span><span class="sxs-lookup"><span data-stu-id="79583-304">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="79583-305">O</span><span class="sxs-lookup"><span data-stu-id="79583-305">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="79583-306">`HttpRequest.BodyReader` a propriedade fornece um <xref:System.IO.Pipelines.PipeReader> que pode ser usado para ler o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="79583-306">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="79583-307">O</span><span class="sxs-lookup"><span data-stu-id="79583-307">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="79583-308">`HttpResponse.BodyWriter` a propriedade fornece um <xref:System.IO.Pipelines.PipeWriter> que pode ser usado para gravar o corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="79583-308">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="79583-309">`HttpRequest.BodyReader` é um análogo do `HttpRequest.Body` fluxo.</span><span class="sxs-lookup"><span data-stu-id="79583-309">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="79583-310">`HttpResponse.BodyWriter` é um análogo do `HttpResponse.Body` fluxo.</span><span class="sxs-lookup"><span data-stu-id="79583-310">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="79583-311">Relatório de erros aprimorado no IIS</span><span class="sxs-lookup"><span data-stu-id="79583-311">Improved error reporting in IIS</span></span>

<span data-ttu-id="79583-312">Os erros de inicialização ao hospedar aplicativos ASP.NET Core no IIS agora produzem dados de diagnóstico mais avançados.</span><span class="sxs-lookup"><span data-stu-id="79583-312">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="79583-313">Esses erros são relatados para o log de eventos do Windows com rastreamentos de pilha onde for aplicável.</span><span class="sxs-lookup"><span data-stu-id="79583-313">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="79583-314">Além disso, todos os avisos, erros e exceções sem tratamento são registrados no log de eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="79583-314">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="79583-315">Serviço de trabalho e SDK de trabalho</span><span class="sxs-lookup"><span data-stu-id="79583-315">Worker Service and Worker SDK</span></span>

<span data-ttu-id="79583-316">O .NET Core 3,0 apresenta o novo modelo de aplicativo do serviço de trabalho.</span><span class="sxs-lookup"><span data-stu-id="79583-316">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="79583-317">Este modelo fornece um ponto de partida para escrever serviços de longa execução no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="79583-317">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="79583-318">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="79583-318">For more information, see:</span></span>

* [<span data-ttu-id="79583-319">Trabalhos do .NET Core como serviços do Windows</span><span class="sxs-lookup"><span data-stu-id="79583-319">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="79583-320">Melhorias de middleware de cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="79583-320">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="79583-321">Em versões anteriores do ASP.NET Core, chamando <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> e  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> eram problemáticas quando implantamos em um Linux do Azure ou atrás de qualquer proxy reverso que não seja o IIS.</span><span class="sxs-lookup"><span data-stu-id="79583-321">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="79583-322">A correção para versões anteriores está documentada em [encaminhar o esquema para proxies inversos do Linux e não IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="79583-322">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="79583-323">Esse cenário é corrigido no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="79583-323">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="79583-324">O host habilita o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando a `ASPNETCORE_FORWARDEDHEADERS_ENABLED` variável de ambiente é definida como `true` .</span><span class="sxs-lookup"><span data-stu-id="79583-324">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="79583-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` é definido como `true` em nossas imagens de contêiner.</span><span class="sxs-lookup"><span data-stu-id="79583-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="79583-326">Melhorias de desempenho</span><span class="sxs-lookup"><span data-stu-id="79583-326">Performance improvements</span></span>

<span data-ttu-id="79583-327">O ASP.NET Core 3,0 inclui muitas melhorias que reduzem o uso de memória e melhoram a taxa de transferência:</span><span class="sxs-lookup"><span data-stu-id="79583-327">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="79583-328">Redução no uso de memória ao usar o contêiner de injeção de dependência interna para serviços com escopo.</span><span class="sxs-lookup"><span data-stu-id="79583-328">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="79583-329">Redução nas alocações em toda a estrutura, incluindo cenários de middleware e roteamento.</span><span class="sxs-lookup"><span data-stu-id="79583-329">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="79583-330">Redução no uso de memória para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="79583-330">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="79583-331">Melhorias de taxa de transferência e redução de memória para conexões HTTPS.</span><span class="sxs-lookup"><span data-stu-id="79583-331">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="79583-332">Novo serializador JSON otimizado e totalmente assíncrono.</span><span class="sxs-lookup"><span data-stu-id="79583-332">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="79583-333">Redução no uso de memória e melhorias de taxa de transferência na análise de formulário.</span><span class="sxs-lookup"><span data-stu-id="79583-333">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="79583-334">O ASP.NET Core 3,0 só é executado no .NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="79583-334">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="79583-335">A partir de ASP.NET Core 3,0, .NET Framework não é mais uma estrutura de destino com suporte.</span><span class="sxs-lookup"><span data-stu-id="79583-335">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="79583-336">Os projetos destinados a .NET Framework podem continuar de uma maneira totalmente compatível usando a [versão LTS do .NET Core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="79583-336">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="79583-337">A maioria dos pacotes relacionados ao ASP.NET Core 2.1. x terá suporte indefinidamente, além do período de LTS de três anos para o .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="79583-337">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="79583-338">Para obter informações de migração, consulte [portar seu código do .NET Framework para o .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="79583-338">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="79583-339">Usar a estrutura compartilhada ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79583-339">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="79583-340">A estrutura compartilhada ASP.NET Core 3,0, contida no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), não requer mais um elemento explícito `<PackageReference />` no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="79583-340">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="79583-341">A estrutura compartilhada é automaticamente referenciada ao usar o `Microsoft.NET.Sdk.Web` SDK no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="79583-341">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="79583-342">Assemblies removidos da estrutura compartilhada do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79583-342">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="79583-343">Os assemblies mais notáveis removidos da estrutura compartilhada ASP.NET Core 3,0 são:</span><span class="sxs-lookup"><span data-stu-id="79583-343">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="79583-344">[Newtonsoft.Jsem](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net).</span><span class="sxs-lookup"><span data-stu-id="79583-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="79583-345">Para adicionar o Json.NET ao ASP.NET Core 3,0, consulte [adicionar Newtonsoft.Jssuporte ao formato JSON baseado em](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="79583-345">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="79583-346">ASP.NET Core 3,0 apresenta `System.Text.Json` para leitura e gravação de JSON.</span><span class="sxs-lookup"><span data-stu-id="79583-346">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="79583-347">Para obter mais informações, consulte [nova SERIALIZAÇÃO JSON](#new-json-serialization) neste documento.</span><span class="sxs-lookup"><span data-stu-id="79583-347">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="79583-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="79583-348">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="79583-349">Para obter uma lista completa de assemblies removidos da estrutura compartilhada, consulte [assemblies sendo removidos do Microsoft. AspNetCore. App 3,0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="79583-349">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="79583-350">Para obter mais informações sobre a motivação para essa alteração, consulte [alterações recentes em Microsoft. AspNetCore. app em 3,0](https://github.com/aspnet/Announcements/issues/325) e [uma primeira análise sobre as alterações que chegam no ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="79583-350">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
