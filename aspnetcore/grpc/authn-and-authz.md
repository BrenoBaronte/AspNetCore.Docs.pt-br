---
title: Autenticação e autorização no gRPC para ASP.NET Core
author: jamesnk
description: Saiba como usar a autenticação e a autorização no gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/authn-and-authz
ms.openlocfilehash: eecdebe5ea7555df0914adfbff728331e3592093
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776162"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="5773e-103">Autenticação e autorização no gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5773e-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="5773e-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5773e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5773e-105">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5773e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="5773e-106">Autenticar usuários chamando um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="5773e-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="5773e-107">gRPC pode ser usado com a [autenticação ASP.NET Core](xref:security/authentication/identity) para associar um usuário a cada chamada.</span><span class="sxs-lookup"><span data-stu-id="5773e-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="5773e-108">Veja a seguir um exemplo de `Startup.Configure` que usa a autenticação gRPC e ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5773e-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="5773e-109">A ordem na qual você registra o ASP.NET Core de middleware de autenticação é importante.</span><span class="sxs-lookup"><span data-stu-id="5773e-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="5773e-110">Sempre chamar `UseAuthentication` e `UseAuthorization` depois `UseRouting` de e `UseEndpoints`antes.</span><span class="sxs-lookup"><span data-stu-id="5773e-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="5773e-111">O mecanismo de autenticação que seu aplicativo usa durante uma chamada precisa ser configurado.</span><span class="sxs-lookup"><span data-stu-id="5773e-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="5773e-112">A configuração de autenticação é `Startup.ConfigureServices` adicionada no e será diferente dependendo do mecanismo de autenticação usado pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5773e-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="5773e-113">Para obter exemplos de como proteger aplicativos ASP.NET Core, consulte [exemplos de autenticação](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="5773e-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="5773e-114">Depois que a autenticação tiver sido configurada, o usuário poderá ser acessado em `ServerCallContext`um método de serviço gRPC por meio do.</span><span class="sxs-lookup"><span data-stu-id="5773e-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="5773e-115">Autenticação de token de portador</span><span class="sxs-lookup"><span data-stu-id="5773e-115">Bearer token authentication</span></span>

<span data-ttu-id="5773e-116">O cliente pode fornecer um token de acesso para autenticação.</span><span class="sxs-lookup"><span data-stu-id="5773e-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="5773e-117">O servidor valida o token e o usa para identificar o usuário.</span><span class="sxs-lookup"><span data-stu-id="5773e-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="5773e-118">No servidor, a autenticação de token de portador é configurada usando o [middleware portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="5773e-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="5773e-119">No cliente .NET gRPC, o token pode ser enviado com chamadas como um cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="5773e-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

<span data-ttu-id="5773e-120">Configurar `ChannelCredentials` o em um canal é uma maneira alternativa de enviar o token para o serviço com chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="5773e-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="5773e-121">A credencial é executada cada vez que uma chamada gRPC é feita, o que evita a necessidade de escrever código em vários locais para passar o token por conta própria.</span><span class="sxs-lookup"><span data-stu-id="5773e-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="5773e-122">A credencial no exemplo a seguir configura o canal para enviar o token com cada chamada gRPC:</span><span class="sxs-lookup"><span data-stu-id="5773e-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="5773e-123">Autenticação de certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="5773e-123">Client certificate authentication</span></span>

<span data-ttu-id="5773e-124">Um cliente pode, opcionalmente, fornecer um certificado de cliente para autenticação.</span><span class="sxs-lookup"><span data-stu-id="5773e-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="5773e-125">A [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5773e-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="5773e-126">Quando a solicitação entra ASP.NET Core, o [pacote de autenticação de certificado de cliente](xref:security/authentication/certauth) permite que você resolva o `ClaimsPrincipal`certificado para um.</span><span class="sxs-lookup"><span data-stu-id="5773e-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="5773e-127">O host precisa ser configurado para aceitar certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="5773e-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="5773e-128">Consulte [Configurar o host para exigir certificados](xref:security/authentication/certauth#configure-your-host-to-require-certificates) para obter informações sobre como aceitar certificados de cliente no Kestrel, no IIS e no Azure.</span><span class="sxs-lookup"><span data-stu-id="5773e-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="5773e-129">No cliente .NET gRPC, o certificado do cliente é adicionado a `HttpClientHandler` que é usado para criar o cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="5773e-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="5773e-130">Outros mecanismos de autenticação</span><span class="sxs-lookup"><span data-stu-id="5773e-130">Other authentication mechanisms</span></span>

<span data-ttu-id="5773e-131">Muitos ASP.NET Core mecanismos de autenticação com suporte funcionam com o gRPC:</span><span class="sxs-lookup"><span data-stu-id="5773e-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="5773e-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="5773e-132">Azure Active Directory</span></span>
* <span data-ttu-id="5773e-133">Certificado do cliente</span><span class="sxs-lookup"><span data-stu-id="5773e-133">Client Certificate</span></span>
* <span data-ttu-id="5773e-134">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="5773e-134">IdentityServer</span></span>
* <span data-ttu-id="5773e-135">JWT Token</span><span class="sxs-lookup"><span data-stu-id="5773e-135">JWT Token</span></span>
* <span data-ttu-id="5773e-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="5773e-136">OAuth 2.0</span></span>
* <span data-ttu-id="5773e-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="5773e-137">OpenID Connect</span></span>
* <span data-ttu-id="5773e-138">O certificado do provedor de identidade do Web Services Federation</span><span class="sxs-lookup"><span data-stu-id="5773e-138">WS-Federation</span></span>

<span data-ttu-id="5773e-139">Para obter mais informações sobre como configurar a autenticação no servidor, consulte [ASP.NET Core autenticação](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="5773e-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="5773e-140">Configurar o cliente gRPC para usar a autenticação dependerá do mecanismo de autenticação que você está usando.</span><span class="sxs-lookup"><span data-stu-id="5773e-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="5773e-141">Os exemplos anteriores de token de portador e certificado de cliente mostram duas maneiras que o cliente gRPC pode ser configurado para enviar metadados de autenticação com chamadas gRPC:</span><span class="sxs-lookup"><span data-stu-id="5773e-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="5773e-142">Clientes gRPC fortemente tipados `HttpClient` usam internamente.</span><span class="sxs-lookup"><span data-stu-id="5773e-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="5773e-143">A autenticação pode ser configurada em [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)ou adicionando instâncias de [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personalizadas `HttpClient`ao.</span><span class="sxs-lookup"><span data-stu-id="5773e-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="5773e-144">Cada chamada gRPC tem um argumento `CallOptions` opcional.</span><span class="sxs-lookup"><span data-stu-id="5773e-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="5773e-145">Cabeçalhos personalizados podem ser enviados usando a coleção de cabeçalhos da opção.</span><span class="sxs-lookup"><span data-stu-id="5773e-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="5773e-146">A autenticação do Windows (NTLM/Kerberos/Negotiate) não pode ser usada com gRPC.</span><span class="sxs-lookup"><span data-stu-id="5773e-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="5773e-147">gRPC requer HTTP/2, e HTTP/2 não dá suporte à autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="5773e-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="5773e-148">Autorizar usuários a acessar os serviços e métodos de serviço</span><span class="sxs-lookup"><span data-stu-id="5773e-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="5773e-149">Por padrão, todos os métodos em um serviço podem ser chamados por usuários não autenticados.</span><span class="sxs-lookup"><span data-stu-id="5773e-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="5773e-150">Para exigir autenticação, aplique o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo ao serviço:</span><span class="sxs-lookup"><span data-stu-id="5773e-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="5773e-151">Você pode usar os argumentos do construtor e as propriedades `[Authorize]` do atributo para restringir o acesso a apenas os usuários que correspondem a [políticas de autorização](xref:security/authorization/policies)específicas.</span><span class="sxs-lookup"><span data-stu-id="5773e-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="5773e-152">Por exemplo, se você tiver uma política de autorização personalizada `MyAuthorizationPolicy`chamada, certifique-se de que somente os usuários que correspondem a essa política possam acessar o serviço usando o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5773e-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="5773e-153">Os métodos de serviço individuais também `[Authorize]` podem ter o atributo aplicado.</span><span class="sxs-lookup"><span data-stu-id="5773e-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="5773e-154">Se o usuário atual não **corresponder às políticas aplicadas ao método** e à classe, um erro será retornado para o chamador:</span><span class="sxs-lookup"><span data-stu-id="5773e-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="5773e-155">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5773e-155">Additional resources</span></span>

* [<span data-ttu-id="5773e-156">Autenticação de token de portador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5773e-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="5773e-157">Configurar a autenticação de certificado de cliente no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5773e-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
