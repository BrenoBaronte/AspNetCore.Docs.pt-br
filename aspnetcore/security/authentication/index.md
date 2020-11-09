---
title: Visão geral da autenticação ASP.NET Core
author: mjrousos
description: Saiba mais sobre a autenticação no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/index
ms.openlocfilehash: eb8c5b3c66f9a0d845d6a1d58c69e6fddefa5b0b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053378"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="5df5e-103">Visão geral da autenticação ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5df5e-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="5df5e-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="5df5e-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="5df5e-105">A autenticação é o processo de determinação da identidade de um usuário.</span><span class="sxs-lookup"><span data-stu-id="5df5e-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="5df5e-106">A [autorização](xref:security/authorization/introduction) é o processo de determinar se um usuário tem acesso a um recurso.</span><span class="sxs-lookup"><span data-stu-id="5df5e-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="5df5e-107">No ASP.NET Core, a autenticação é tratada pelo `IAuthenticationService` , que é usado pelo [middleware](xref:fundamentals/middleware/index)de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5df5e-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="5df5e-108">O serviço de autenticação usa manipuladores de autenticação registrados para concluir as ações relacionadas à autenticação.</span><span class="sxs-lookup"><span data-stu-id="5df5e-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="5df5e-109">Exemplos de ações relacionadas à autenticação incluem:</span><span class="sxs-lookup"><span data-stu-id="5df5e-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="5df5e-110">Autenticação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="5df5e-110">Authenticating a user.</span></span>
* <span data-ttu-id="5df5e-111">Respondendo quando um usuário não autenticado tenta acessar um recurso restrito.</span><span class="sxs-lookup"><span data-stu-id="5df5e-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="5df5e-112">Os manipuladores de autenticação registrados e suas opções de configuração são chamados de "esquemas".</span><span class="sxs-lookup"><span data-stu-id="5df5e-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="5df5e-113">Os esquemas de autenticação são especificados pelo registro dos serviços de autenticação no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5df5e-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="5df5e-114">Chamando um método de extensão específico de esquema após uma chamada para `services.AddAuthentication` (como `AddJwtBearer` ou `AddCookie` , por exemplo).</span><span class="sxs-lookup"><span data-stu-id="5df5e-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="5df5e-115">Esses métodos de extensão usam [AuthenticationBuilder. addscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) para registrar esquemas com as configurações apropriadas.</span><span class="sxs-lookup"><span data-stu-id="5df5e-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="5df5e-116">Com menos frequência, chamando [AuthenticationBuilder. addscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) diretamente.</span><span class="sxs-lookup"><span data-stu-id="5df5e-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="5df5e-117">Por exemplo, o código a seguir registra os serviços de autenticação e os manipuladores para os cookie esquemas de autenticação de portador JWT:</span><span class="sxs-lookup"><span data-stu-id="5df5e-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="5df5e-118">O `AddAuthentication` parâmetro `JwtBearerDefaults.AuthenticationScheme` é o nome do esquema a ser usado por padrão quando um esquema específico não é solicitado.</span><span class="sxs-lookup"><span data-stu-id="5df5e-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="5df5e-119">Se vários esquemas forem usados, as políticas de autorização (ou atributos de autorização) poderão [especificar o esquema de autenticação (ou esquemas)](xref:security/authorization/limitingidentitybyscheme) que dependem para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="5df5e-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="5df5e-120">No exemplo acima, o cookie esquema de autenticação pode ser usado especificando seu nome ( `CookieAuthenticationDefaults.AuthenticationScheme` por padrão, embora um nome diferente possa ser fornecido ao chamar `AddCookie` ).</span><span class="sxs-lookup"><span data-stu-id="5df5e-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="5df5e-121">Em alguns casos, a chamada para `AddAuthentication` é feita automaticamente por outros métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="5df5e-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="5df5e-122">Por exemplo, ao usar [ASP.NET Core Identity](xref:security/authentication/identity) , o `AddAuthentication` é chamado internamente.</span><span class="sxs-lookup"><span data-stu-id="5df5e-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="5df5e-123">O middleware de autenticação é adicionado no `Startup.Configure` chamando o <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> método de extensão no do aplicativo `IApplicationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5df5e-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="5df5e-124">Chamar `UseAuthentication` registra o middleware que usa os esquemas de autenticação registrados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="5df5e-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="5df5e-125">Chame `UseAuthentication` antes de qualquer middleware que dependa dos usuários que estão sendo autenticados.</span><span class="sxs-lookup"><span data-stu-id="5df5e-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="5df5e-126">Ao usar o roteamento de ponto de extremidade, a chamada para `UseAuthentication` deve ir:</span><span class="sxs-lookup"><span data-stu-id="5df5e-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="5df5e-127">Depois `UseRouting` , para que as informações de rota estejam disponíveis para decisões de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5df5e-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="5df5e-128">Antes `UseEndpoints` , para que os usuários sejam autenticados antes de acessar os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="5df5e-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="5df5e-129">Conceitos de autenticação</span><span class="sxs-lookup"><span data-stu-id="5df5e-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="5df5e-130">Esquema de autenticação</span><span class="sxs-lookup"><span data-stu-id="5df5e-130">Authentication scheme</span></span>

<span data-ttu-id="5df5e-131">Um esquema de autenticação é um nome que corresponde a:</span><span class="sxs-lookup"><span data-stu-id="5df5e-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="5df5e-132">Um manipulador de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5df5e-132">An authentication handler.</span></span>
* <span data-ttu-id="5df5e-133">Opções para configurar essa instância específica do manipulador.</span><span class="sxs-lookup"><span data-stu-id="5df5e-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="5df5e-134">Os esquemas são úteis como um mecanismo para se referir aos comportamentos de autenticação, desafio e proíba do manipulador associado.</span><span class="sxs-lookup"><span data-stu-id="5df5e-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="5df5e-135">Por exemplo, uma política de autorização pode usar nomes de esquema para especificar qual esquema (ou esquemas) de autenticação deve ser usado para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="5df5e-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="5df5e-136">Ao configurar a autenticação, é comum especificar o esquema de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="5df5e-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="5df5e-137">O esquema padrão é usado, a menos que um recurso solicite um esquema específico.</span><span class="sxs-lookup"><span data-stu-id="5df5e-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="5df5e-138">Também é possível:</span><span class="sxs-lookup"><span data-stu-id="5df5e-138">It's also possible to:</span></span>

* <span data-ttu-id="5df5e-139">Especifique esquemas padrão diferentes a serem usados para ações de autenticação, desafio e proibir.</span><span class="sxs-lookup"><span data-stu-id="5df5e-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="5df5e-140">Combine vários esquemas em um usando [esquemas de política](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="5df5e-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="5df5e-141">Manipulador de autenticação</span><span class="sxs-lookup"><span data-stu-id="5df5e-141">Authentication handler</span></span>

<span data-ttu-id="5df5e-142">Um manipulador de autenticação:</span><span class="sxs-lookup"><span data-stu-id="5df5e-142">An authentication handler:</span></span>

* <span data-ttu-id="5df5e-143">É um tipo que implementa o comportamento de um esquema.</span><span class="sxs-lookup"><span data-stu-id="5df5e-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="5df5e-144">É derivado de <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> ou <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="5df5e-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="5df5e-145">O tem a principal responsabilidade de autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="5df5e-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="5df5e-146">Com base na configuração do esquema de autenticação e no contexto de solicitação de entrada, manipuladores de autenticação:</span><span class="sxs-lookup"><span data-stu-id="5df5e-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="5df5e-147">Construa <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objetos que representam a identidade do usuário se a autenticação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="5df5e-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="5df5e-148">Retorne ' sem resultado ' ou ' falha ' se a autenticação não for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="5df5e-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="5df5e-149">Ter métodos de desafio e de proibir ações para quando os usuários tentarem acessar recursos:</span><span class="sxs-lookup"><span data-stu-id="5df5e-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="5df5e-150">Eles não são autorizados a acessar (proíba).</span><span class="sxs-lookup"><span data-stu-id="5df5e-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="5df5e-151">Quando eles não são autenticados (desafio).</span><span class="sxs-lookup"><span data-stu-id="5df5e-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="5df5e-152">Authenticate</span><span class="sxs-lookup"><span data-stu-id="5df5e-152">Authenticate</span></span>

<span data-ttu-id="5df5e-153">A ação de autenticação de um esquema de autenticação é responsável por construir a identidade do usuário com base no contexto da solicitação.</span><span class="sxs-lookup"><span data-stu-id="5df5e-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="5df5e-154">Ele retorna um valor <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> que indica se a autenticação foi bem-sucedida e, nesse caso, a identidade do usuário em um tíquete de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5df5e-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="5df5e-155">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="5df5e-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="5df5e-156">Os exemplos de autenticação incluem:</span><span class="sxs-lookup"><span data-stu-id="5df5e-156">Authenticate examples include:</span></span>

* <span data-ttu-id="5df5e-157">Um cookie esquema de autenticação que constrói a identidade do usuário a partir de cookie s.</span><span class="sxs-lookup"><span data-stu-id="5df5e-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="5df5e-158">Um esquema de portador JWT desserializando e Validando um token de portador JWT para construir a identidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="5df5e-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="5df5e-159">Desafio</span><span class="sxs-lookup"><span data-stu-id="5df5e-159">Challenge</span></span>

<span data-ttu-id="5df5e-160">Um desafio de autenticação é invocado pela autorização quando um usuário não autenticado solicita um ponto de extremidade que requer autenticação.</span><span class="sxs-lookup"><span data-stu-id="5df5e-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="5df5e-161">Um desafio de autenticação é emitido, por exemplo, quando um usuário anônimo solicita um recurso restrito ou clica em um link de logon.</span><span class="sxs-lookup"><span data-stu-id="5df5e-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="5df5e-162">A autorização invoca um desafio usando os esquemas de autenticação especificados ou o padrão, se nenhum for especificado.</span><span class="sxs-lookup"><span data-stu-id="5df5e-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="5df5e-163">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="5df5e-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="5df5e-164">Os exemplos de desafio de autenticação incluem:</span><span class="sxs-lookup"><span data-stu-id="5df5e-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="5df5e-165">Um cookie esquema de autenticação redirecionando o usuário para uma página de logon.</span><span class="sxs-lookup"><span data-stu-id="5df5e-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="5df5e-166">Um esquema de portador JWT que retorna um resultado 401 com um `www-authenticate: bearer` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="5df5e-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="5df5e-167">Uma ação de desafio deve permitir que o usuário saiba qual mecanismo de autenticação usar para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="5df5e-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="5df5e-168">Proíba</span><span class="sxs-lookup"><span data-stu-id="5df5e-168">Forbid</span></span>

<span data-ttu-id="5df5e-169">Uma ação proibir do esquema de autenticação é chamada por autorização quando um usuário autenticado tenta acessar um recurso que não tem permissão para acessar.</span><span class="sxs-lookup"><span data-stu-id="5df5e-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="5df5e-170">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="5df5e-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="5df5e-171">A autenticação proíbem exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="5df5e-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="5df5e-172">Um cookie esquema de autenticação redirecionando o usuário para uma página indicando que o acesso foi proibido.</span><span class="sxs-lookup"><span data-stu-id="5df5e-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="5df5e-173">Um esquema de portador JWT que retorna um resultado 403.</span><span class="sxs-lookup"><span data-stu-id="5df5e-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="5df5e-174">Um esquema de autenticação personalizado redirecionando para uma página na qual o usuário pode solicitar acesso ao recurso.</span><span class="sxs-lookup"><span data-stu-id="5df5e-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="5df5e-175">Uma ação proíba pode permitir que o usuário saiba:</span><span class="sxs-lookup"><span data-stu-id="5df5e-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="5df5e-176">Eles são autenticados.</span><span class="sxs-lookup"><span data-stu-id="5df5e-176">They are authenticated.</span></span>
* <span data-ttu-id="5df5e-177">Eles não têm permissão para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="5df5e-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="5df5e-178">Consulte os seguintes links para obter as diferenças entre o desafio e o proíba:</span><span class="sxs-lookup"><span data-stu-id="5df5e-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="5df5e-179">[Desafio e proíba com um manipulador de recursos operacionais](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="5df5e-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="5df5e-180">[Diferenças entre desafio e proibir](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="5df5e-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="5df5e-181">Provedores de autenticação por locatário</span><span class="sxs-lookup"><span data-stu-id="5df5e-181">Authentication providers per tenant</span></span>

<span data-ttu-id="5df5e-182">O ASP.NET Core Framework não tem uma solução interna para autenticação multilocatário.</span><span class="sxs-lookup"><span data-stu-id="5df5e-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="5df5e-183">Embora seja certamente possível para os clientes escreverem um, usando os recursos internos, recomendamos que os clientes examinem o [Orchard Core](https://www.orchardcore.net/) para essa finalidade.</span><span class="sxs-lookup"><span data-stu-id="5df5e-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="5df5e-184">O Orchard Core é:</span><span class="sxs-lookup"><span data-stu-id="5df5e-184">Orchard Core is:</span></span>

* <span data-ttu-id="5df5e-185">Uma estrutura de aplicativos modular e multilocatários de software livre criada com ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5df5e-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="5df5e-186">Um CMS (sistema de gerenciamento de conteúdo) criado sobre essa estrutura de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5df5e-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="5df5e-187">Consulte a fonte do [Orchard Core](https://github.com/OrchardCMS/OrchardCore) para obter um exemplo de provedores de autenticação por locatário.</span><span class="sxs-lookup"><span data-stu-id="5df5e-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5df5e-188">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5df5e-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="5df5e-189">Exigir globalmente usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="5df5e-189">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)