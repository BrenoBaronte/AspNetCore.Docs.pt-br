---
title: Visão geral da autenticação ASP.NET Core
author: mjrousos
description: Saiba mais sobre a autenticação no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 1/24/2021
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
uid: security/authentication/index
ms.openlocfilehash: 72036e9c4c92ee5dd82ac4a67e766fb0e5c8f924
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057285"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="dba13-103">Visão geral da autenticação ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dba13-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="dba13-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="dba13-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="dba13-105">A autenticação é o processo de determinação da identidade de um usuário.</span><span class="sxs-lookup"><span data-stu-id="dba13-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="dba13-106">A [autorização](xref:security/authorization/introduction) é o processo de determinar se um usuário tem acesso a um recurso.</span><span class="sxs-lookup"><span data-stu-id="dba13-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="dba13-107">No ASP.NET Core, a autenticação é tratada pelo `IAuthenticationService` , que é usado pelo [middleware](xref:fundamentals/middleware/index)de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dba13-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="dba13-108">O serviço de autenticação usa manipuladores de autenticação registrados para concluir as ações relacionadas à autenticação.</span><span class="sxs-lookup"><span data-stu-id="dba13-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="dba13-109">Exemplos de ações relacionadas à autenticação incluem:</span><span class="sxs-lookup"><span data-stu-id="dba13-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="dba13-110">Autenticação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="dba13-110">Authenticating a user.</span></span>
* <span data-ttu-id="dba13-111">Respondendo quando um usuário não autenticado tenta acessar um recurso restrito.</span><span class="sxs-lookup"><span data-stu-id="dba13-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="dba13-112">Os manipuladores de autenticação registrados e suas opções de configuração são chamados de "esquemas".</span><span class="sxs-lookup"><span data-stu-id="dba13-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="dba13-113">Os esquemas de autenticação são especificados pelo registro dos serviços de autenticação no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dba13-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="dba13-114">Chamando um método de extensão específico de esquema após uma chamada para `services.AddAuthentication` (como `AddJwtBearer` ou `AddCookie` , por exemplo).</span><span class="sxs-lookup"><span data-stu-id="dba13-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="dba13-115">Esses métodos de extensão usam [AuthenticationBuilder. addscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) para registrar esquemas com as configurações apropriadas.</span><span class="sxs-lookup"><span data-stu-id="dba13-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="dba13-116">Com menos frequência, chamando [AuthenticationBuilder. addscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) diretamente.</span><span class="sxs-lookup"><span data-stu-id="dba13-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="dba13-117">Por exemplo, o código a seguir registra os serviços de autenticação e os manipuladores para os cookie esquemas de autenticação de portador JWT:</span><span class="sxs-lookup"><span data-stu-id="dba13-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="dba13-118">O `AddAuthentication` parâmetro `JwtBearerDefaults.AuthenticationScheme` é o nome do esquema a ser usado por padrão quando um esquema específico não é solicitado.</span><span class="sxs-lookup"><span data-stu-id="dba13-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="dba13-119">Se vários esquemas forem usados, as políticas de autorização (ou atributos de autorização) poderão [especificar o esquema de autenticação (ou esquemas)](xref:security/authorization/limitingidentitybyscheme) que dependem para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="dba13-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="dba13-120">No exemplo acima, o cookie esquema de autenticação pode ser usado especificando seu nome ( `CookieAuthenticationDefaults.AuthenticationScheme` por padrão, embora um nome diferente possa ser fornecido ao chamar `AddCookie` ).</span><span class="sxs-lookup"><span data-stu-id="dba13-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="dba13-121">Em alguns casos, a chamada para `AddAuthentication` é feita automaticamente por outros métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="dba13-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="dba13-122">Por exemplo, ao usar [ASP.NET Core Identity](xref:security/authentication/identity) , o `AddAuthentication` é chamado internamente.</span><span class="sxs-lookup"><span data-stu-id="dba13-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="dba13-123">O middleware de autenticação é adicionado no `Startup.Configure` chamando o <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> método de extensão no do aplicativo `IApplicationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dba13-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="dba13-124">Chamar `UseAuthentication` registra o middleware que usa os esquemas de autenticação registrados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="dba13-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="dba13-125">Chame `UseAuthentication` antes de qualquer middleware que dependa dos usuários que estão sendo autenticados.</span><span class="sxs-lookup"><span data-stu-id="dba13-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="dba13-126">Ao usar o roteamento de ponto de extremidade, a chamada para `UseAuthentication` deve ir:</span><span class="sxs-lookup"><span data-stu-id="dba13-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="dba13-127">Depois `UseRouting` , para que as informações de rota estejam disponíveis para decisões de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dba13-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="dba13-128">Antes `UseEndpoints` , para que os usuários sejam autenticados antes de acessar os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="dba13-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="dba13-129">Conceitos de autenticação</span><span class="sxs-lookup"><span data-stu-id="dba13-129">Authentication Concepts</span></span>

<span data-ttu-id="dba13-130">A autenticação é responsável por fornecer a <xref:System.Security.Claims.ClaimsPrincipal> autorização para tomar decisões de permissão.</span><span class="sxs-lookup"><span data-stu-id="dba13-130">Authentication is responsible for providing the <xref:System.Security.Claims.ClaimsPrincipal> for authorization to make permission decisions against.</span></span> <span data-ttu-id="dba13-131">Há várias abordagens de esquema de autenticação para selecionar qual manipulador de autenticação é responsável por gerar o conjunto correto de declarações:</span><span class="sxs-lookup"><span data-stu-id="dba13-131">There are multiple authentication scheme approaches to select which authentication handler is responsible for generating the correct set of claims:</span></span>

  * <span data-ttu-id="dba13-132">[Esquema de autenticação](xref:security/authorization/limitingidentitybyscheme), também abordado na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dba13-132">[Authentication scheme](xref:security/authorization/limitingidentitybyscheme), also discussed in the next section.</span></span>
  * <span data-ttu-id="dba13-133">O esquema de autenticação padrão, abordado na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dba13-133">The default authentication scheme, discussed in the next section.</span></span>
  * <span data-ttu-id="dba13-134">Defina o [HttpContext. User](xref:Microsoft.AspNetCore.Http.HttpContext.User)diretamente.</span><span class="sxs-lookup"><span data-stu-id="dba13-134">Directly set [HttpContext.User](xref:Microsoft.AspNetCore.Http.HttpContext.User).</span></span>

<span data-ttu-id="dba13-135">Não há nenhuma investigação automática de esquemas.</span><span class="sxs-lookup"><span data-stu-id="dba13-135">There is no automatic probing of schemes.</span></span> <span data-ttu-id="dba13-136">Se o esquema padrão não for especificado, o esquema deverá ser especificado no atributo autorizar, caso contrário, o seguinte erro será gerado:</span><span class="sxs-lookup"><span data-stu-id="dba13-136">If the default scheme is not specified, the scheme must be specified in the authorize attribute, otherwise, the following error is thrown:</span></span>

  <span data-ttu-id="dba13-137">InvalidOperationException: nenhum authenticationScheme foi especificado e nenhum DefaultAuthenticateScheme foi encontrado.</span><span class="sxs-lookup"><span data-stu-id="dba13-137">InvalidOperationException: No authenticationScheme was specified, and there was no DefaultAuthenticateScheme found.</span></span> <span data-ttu-id="dba13-138">Os esquemas padrão podem ser definidos usando addauthentication (cadeia de caracteres defaultscheme) ou addauthentication (ação &lt; authenticationoptions &gt; configureoptions).</span><span class="sxs-lookup"><span data-stu-id="dba13-138">The default schemes can be set using either AddAuthentication(string defaultScheme) or AddAuthentication(Action&lt;AuthenticationOptions&gt; configureOptions).</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="dba13-139">Esquema de autenticação</span><span class="sxs-lookup"><span data-stu-id="dba13-139">Authentication scheme</span></span>

<span data-ttu-id="dba13-140">O [esquema de autenticação](xref:security/authorization/limitingidentitybyscheme) pode selecionar qual manipulador de autenticação é responsável por gerar o conjunto correto de declarações.</span><span class="sxs-lookup"><span data-stu-id="dba13-140">The [authentication scheme](xref:security/authorization/limitingidentitybyscheme) can select which authentication handler is responsible for generating the correct set of claims.</span></span> <span data-ttu-id="dba13-141">Para obter mais informações, consulte [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="dba13-141">For more information, see [Authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span>

<span data-ttu-id="dba13-142">Um esquema de autenticação é um nome que corresponde a:</span><span class="sxs-lookup"><span data-stu-id="dba13-142">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="dba13-143">Um manipulador de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dba13-143">An authentication handler.</span></span>
* <span data-ttu-id="dba13-144">Opções para configurar essa instância específica do manipulador.</span><span class="sxs-lookup"><span data-stu-id="dba13-144">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="dba13-145">Os esquemas são úteis como um mecanismo para se referir aos comportamentos de autenticação, desafio e proíba do manipulador associado.</span><span class="sxs-lookup"><span data-stu-id="dba13-145">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="dba13-146">Por exemplo, uma política de autorização pode usar nomes de esquema para especificar qual esquema (ou esquemas) de autenticação deve ser usado para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="dba13-146">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="dba13-147">Ao configurar a autenticação, é comum especificar o esquema de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="dba13-147">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="dba13-148">O esquema padrão é usado, a menos que um recurso solicite um esquema específico.</span><span class="sxs-lookup"><span data-stu-id="dba13-148">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="dba13-149">Também é possível:</span><span class="sxs-lookup"><span data-stu-id="dba13-149">It's also possible to:</span></span>

* <span data-ttu-id="dba13-150">Especifique esquemas padrão diferentes a serem usados para ações de autenticação, desafio e proibir.</span><span class="sxs-lookup"><span data-stu-id="dba13-150">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="dba13-151">Combine vários esquemas em um usando [esquemas de política](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="dba13-151">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="dba13-152">Manipulador de autenticação</span><span class="sxs-lookup"><span data-stu-id="dba13-152">Authentication handler</span></span>

<span data-ttu-id="dba13-153">Um manipulador de autenticação:</span><span class="sxs-lookup"><span data-stu-id="dba13-153">An authentication handler:</span></span>

* <span data-ttu-id="dba13-154">É um tipo que implementa o comportamento de um esquema.</span><span class="sxs-lookup"><span data-stu-id="dba13-154">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="dba13-155">É derivado de <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> ou <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="dba13-155">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="dba13-156">O tem a principal responsabilidade de autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="dba13-156">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="dba13-157">Com base na configuração do esquema de autenticação e no contexto de solicitação de entrada, manipuladores de autenticação:</span><span class="sxs-lookup"><span data-stu-id="dba13-157">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="dba13-158">Construa <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objetos que representam a identidade do usuário se a autenticação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="dba13-158">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="dba13-159">Retorne ' sem resultado ' ou ' falha ' se a autenticação não for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="dba13-159">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="dba13-160">Ter métodos de desafio e de proibir ações para quando os usuários tentarem acessar recursos:</span><span class="sxs-lookup"><span data-stu-id="dba13-160">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="dba13-161">Eles não são autorizados a acessar (proíba).</span><span class="sxs-lookup"><span data-stu-id="dba13-161">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="dba13-162">Quando eles não são autenticados (desafio).</span><span class="sxs-lookup"><span data-stu-id="dba13-162">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="dba13-163">Authenticate</span><span class="sxs-lookup"><span data-stu-id="dba13-163">Authenticate</span></span>

<span data-ttu-id="dba13-164">A ação de autenticação de um esquema de autenticação é responsável por construir a identidade do usuário com base no contexto da solicitação.</span><span class="sxs-lookup"><span data-stu-id="dba13-164">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="dba13-165">Ele retorna um valor <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> que indica se a autenticação foi bem-sucedida e, nesse caso, a identidade do usuário em um tíquete de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dba13-165">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="dba13-166">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="dba13-166">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="dba13-167">Os exemplos de autenticação incluem:</span><span class="sxs-lookup"><span data-stu-id="dba13-167">Authenticate examples include:</span></span>

* <span data-ttu-id="dba13-168">Um cookie esquema de autenticação que constrói a identidade do usuário a partir de cookie s.</span><span class="sxs-lookup"><span data-stu-id="dba13-168">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="dba13-169">Um esquema de portador JWT desserializando e Validando um token de portador JWT para construir a identidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="dba13-169">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="dba13-170">Desafio</span><span class="sxs-lookup"><span data-stu-id="dba13-170">Challenge</span></span>

<span data-ttu-id="dba13-171">Um desafio de autenticação é invocado pela autorização quando um usuário não autenticado solicita um ponto de extremidade que requer autenticação.</span><span class="sxs-lookup"><span data-stu-id="dba13-171">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="dba13-172">Um desafio de autenticação é emitido, por exemplo, quando um usuário anônimo solicita um recurso restrito ou clica em um link de logon.</span><span class="sxs-lookup"><span data-stu-id="dba13-172">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="dba13-173">A autorização invoca um desafio usando os esquemas de autenticação especificados ou o padrão, se nenhum for especificado.</span><span class="sxs-lookup"><span data-stu-id="dba13-173">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="dba13-174">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="dba13-174">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="dba13-175">Os exemplos de desafio de autenticação incluem:</span><span class="sxs-lookup"><span data-stu-id="dba13-175">Authentication challenge examples include:</span></span>

* <span data-ttu-id="dba13-176">Um cookie esquema de autenticação redirecionando o usuário para uma página de logon.</span><span class="sxs-lookup"><span data-stu-id="dba13-176">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="dba13-177">Um esquema de portador JWT que retorna um resultado 401 com um `www-authenticate: bearer` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="dba13-177">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="dba13-178">Uma ação de desafio deve permitir que o usuário saiba qual mecanismo de autenticação usar para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="dba13-178">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="dba13-179">Proíba</span><span class="sxs-lookup"><span data-stu-id="dba13-179">Forbid</span></span>

<span data-ttu-id="dba13-180">Uma ação proibir do esquema de autenticação é chamada por autorização quando um usuário autenticado tenta acessar um recurso que não tem permissão para acessar.</span><span class="sxs-lookup"><span data-stu-id="dba13-180">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="dba13-181">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="dba13-181">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="dba13-182">A autenticação proíbem exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="dba13-182">Authentication forbid examples include:</span></span>
* <span data-ttu-id="dba13-183">Um cookie esquema de autenticação redirecionando o usuário para uma página indicando que o acesso foi proibido.</span><span class="sxs-lookup"><span data-stu-id="dba13-183">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="dba13-184">Um esquema de portador JWT que retorna um resultado 403.</span><span class="sxs-lookup"><span data-stu-id="dba13-184">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="dba13-185">Um esquema de autenticação personalizado redirecionando para uma página na qual o usuário pode solicitar acesso ao recurso.</span><span class="sxs-lookup"><span data-stu-id="dba13-185">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="dba13-186">Uma ação proíba pode permitir que o usuário saiba:</span><span class="sxs-lookup"><span data-stu-id="dba13-186">A forbid action can let the user know:</span></span>

* <span data-ttu-id="dba13-187">Eles são autenticados.</span><span class="sxs-lookup"><span data-stu-id="dba13-187">They are authenticated.</span></span>
* <span data-ttu-id="dba13-188">Eles não têm permissão para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="dba13-188">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="dba13-189">Consulte os seguintes links para obter as diferenças entre o desafio e o proíba:</span><span class="sxs-lookup"><span data-stu-id="dba13-189">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="dba13-190">[Desafio e proíba com um manipulador de recursos operacionais](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="dba13-190">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="dba13-191">[Diferenças entre desafio e proibir](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="dba13-191">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="dba13-192">Provedores de autenticação por locatário</span><span class="sxs-lookup"><span data-stu-id="dba13-192">Authentication providers per tenant</span></span>

<span data-ttu-id="dba13-193">O ASP.NET Core Framework não tem uma solução interna para autenticação multilocatário.</span><span class="sxs-lookup"><span data-stu-id="dba13-193">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="dba13-194">Embora seja certamente possível para os clientes escreverem um, usando os recursos internos, recomendamos que os clientes examinem o [Orchard Core](https://www.orchardcore.net/) para essa finalidade.</span><span class="sxs-lookup"><span data-stu-id="dba13-194">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="dba13-195">O Orchard Core é:</span><span class="sxs-lookup"><span data-stu-id="dba13-195">Orchard Core is:</span></span>

* <span data-ttu-id="dba13-196">Uma estrutura de aplicativos modular e multilocatários de software livre criada com ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dba13-196">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="dba13-197">Um CMS (sistema de gerenciamento de conteúdo) criado sobre essa estrutura de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dba13-197">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="dba13-198">Consulte a fonte do [Orchard Core](https://github.com/OrchardCMS/OrchardCore) para obter um exemplo de provedores de autenticação por locatário.</span><span class="sxs-lookup"><span data-stu-id="dba13-198">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dba13-199">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dba13-199">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="dba13-200">Exigir globalmente usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="dba13-200">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)
* [<span data-ttu-id="dba13-201">Problema do GitHub sobre como usar vários esquemas de autenticação</span><span class="sxs-lookup"><span data-stu-id="dba13-201">GitHub issue on using multiple authentication schemes</span></span>](https://github.com/dotnet/aspnetcore/issues/26002)
