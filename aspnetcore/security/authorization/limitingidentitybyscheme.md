---
title: Autorizar com um esquema específico no ASP.NET Core
author: rick-anderson
description: Este artigo explica como limitar a identidade a um esquema específico ao trabalhar com vários métodos de autenticação.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: c4cbec1b829fb8fd47f7b6924b6870bd5dd7097d
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057298"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="5f284-103">Autorizar com um esquema específico no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f284-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="5f284-104">Para obter uma introdução aos esquemas de autenticação no ASP.NET Core, consulte [esquema de autenticação](xref:security/authentication/index#authentication-scheme).</span><span class="sxs-lookup"><span data-stu-id="5f284-104">For an introduction to authentication schemes in ASP.NET Core, see [Authentication scheme](xref:security/authentication/index#authentication-scheme).</span></span>

<span data-ttu-id="5f284-105">Em alguns cenários, como SPAs (aplicativos de página única), é comum usar vários métodos de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5f284-105">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="5f284-106">Por exemplo, o aplicativo pode usar a cookie autenticação com base para fazer logon e autenticação de portador JWT para solicitações de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5f284-106">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="5f284-107">Em alguns casos, o aplicativo pode ter várias instâncias de um manipulador de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5f284-107">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="5f284-108">Por exemplo, dois cookie manipuladores em que um contém uma identidade básica e outro são criados quando uma MFA (autenticação multifator) foi disparada.</span><span class="sxs-lookup"><span data-stu-id="5f284-108">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="5f284-109">A MFA pode ser disparada porque o usuário solicitou uma operação que requer segurança extra.</span><span class="sxs-lookup"><span data-stu-id="5f284-109">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="5f284-110">Para obter mais informações sobre a imposição de MFA quando um usuário solicita um recurso que requer MFA, consulte a seção problema de proteção do GitHub [com MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span><span class="sxs-lookup"><span data-stu-id="5f284-110">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="5f284-111">Um esquema de autenticação é nomeado quando o serviço de autenticação é configurado durante a autenticação.</span><span class="sxs-lookup"><span data-stu-id="5f284-111">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="5f284-112">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5f284-112">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="5f284-113">No código anterior, dois manipuladores de autenticação foram adicionados: um para cookie s e outro para portador.</span><span class="sxs-lookup"><span data-stu-id="5f284-113">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="5f284-114">Especificar o esquema padrão resulta na `HttpContext.User` propriedade que está sendo definida para essa identidade.</span><span class="sxs-lookup"><span data-stu-id="5f284-114">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="5f284-115">Se esse comportamento não for desejado, desabilite-o invocando a forma sem parâmetros de `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="5f284-115">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="5f284-116">Selecionando o esquema com o atributo Authorize</span><span class="sxs-lookup"><span data-stu-id="5f284-116">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="5f284-117">No ponto de autorização, o aplicativo indica o manipulador a ser usado.</span><span class="sxs-lookup"><span data-stu-id="5f284-117">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="5f284-118">Selecione o manipulador com o qual o aplicativo será autorizado, passando uma lista delimitada por vírgulas de esquemas de autenticação para o `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="5f284-118">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="5f284-119">O `[Authorize]` atributo especifica o esquema de autenticação ou os esquemas a serem usados, independentemente de um padrão estar configurado.</span><span class="sxs-lookup"><span data-stu-id="5f284-119">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="5f284-120">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5f284-120">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="5f284-121">No exemplo anterior, os cookie manipuladores e de portador são executados e têm a oportunidade de criar e acrescentar uma identidade para o usuário atual.</span><span class="sxs-lookup"><span data-stu-id="5f284-121">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="5f284-122">Ao especificar apenas um único esquema, o manipulador correspondente é executado.</span><span class="sxs-lookup"><span data-stu-id="5f284-122">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="5f284-123">No código anterior, apenas o manipulador com o esquema "portador" é executado.</span><span class="sxs-lookup"><span data-stu-id="5f284-123">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="5f284-124">As cookie identidades baseadas em qualquer um são ignoradas.</span><span class="sxs-lookup"><span data-stu-id="5f284-124">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="5f284-125">Selecionando o esquema com políticas</span><span class="sxs-lookup"><span data-stu-id="5f284-125">Selecting the scheme with policies</span></span>

<span data-ttu-id="5f284-126">Se preferir especificar os esquemas desejados na [política](xref:security/authorization/policies), você poderá definir a `AuthenticationSchemes` coleção ao adicionar a política:</span><span class="sxs-lookup"><span data-stu-id="5f284-126">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="5f284-127">No exemplo anterior, a política "Over18" só é executada em relação à identidade criada pelo manipulador "portador".</span><span class="sxs-lookup"><span data-stu-id="5f284-127">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="5f284-128">Use a política definindo a `[Authorize]` Propriedade do atributo `Policy` :</span><span class="sxs-lookup"><span data-stu-id="5f284-128">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="5f284-129">Usar vários esquemas de autenticação</span><span class="sxs-lookup"><span data-stu-id="5f284-129">Use multiple authentication schemes</span></span>

<span data-ttu-id="5f284-130">Alguns aplicativos podem precisar dar suporte a vários tipos de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5f284-130">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="5f284-131">Por exemplo, seu aplicativo pode autenticar usuários de Azure Active Directory e de um banco de dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="5f284-131">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="5f284-132">Outro exemplo é um aplicativo que autentica os usuários de Serviços de Federação do Active Directory (AD FS) e Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="5f284-132">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="5f284-133">Nesse caso, o aplicativo deve aceitar um token de portador JWT de vários emissores.</span><span class="sxs-lookup"><span data-stu-id="5f284-133">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="5f284-134">Adicione todos os esquemas de autenticação que você gostaria de aceitar.</span><span class="sxs-lookup"><span data-stu-id="5f284-134">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="5f284-135">Por exemplo, o código a seguir no `Startup.ConfigureServices` adiciona dois esquemas de autenticação de portador JWT com emissores diferentes:</span><span class="sxs-lookup"><span data-stu-id="5f284-135">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="5f284-136">Somente uma autenticação de portador JWT é registrada com o esquema de autenticação padrão `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="5f284-136">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="5f284-137">A autenticação adicional deve ser registrada com um esquema de autenticação exclusivo.</span><span class="sxs-lookup"><span data-stu-id="5f284-137">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="5f284-138">A próxima etapa é atualizar a política de autorização padrão para aceitar os dois esquemas de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5f284-138">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="5f284-139">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5f284-139">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="5f284-140">Como a política de autorização padrão é substituída, é possível usar o `[Authorize]` atributo em controladores.</span><span class="sxs-lookup"><span data-stu-id="5f284-140">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="5f284-141">Em seguida, o controlador aceita solicitações com JWT emitido pelo primeiro ou segundo emissor.</span><span class="sxs-lookup"><span data-stu-id="5f284-141">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

<span data-ttu-id="5f284-142">Consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/26002) sobre como usar vários esquemas de autenticação.</span><span class="sxs-lookup"><span data-stu-id="5f284-142">See [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/26002) on using multiple authentication schemes.</span></span>
