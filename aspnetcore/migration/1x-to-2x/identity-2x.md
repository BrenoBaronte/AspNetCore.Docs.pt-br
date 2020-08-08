---
title: Migrar autenticação e Identity para ASP.NET Core 2,0
author: scottaddie
description: Este artigo descreve as etapas mais comuns para migrar a autenticação ASP.NET Core 1. x e Identity ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 46f10df25235b532f188eda2a079aef71070cd6d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015284"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="d8065-103">Migrar autenticação e Identity para ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="d8065-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="d8065-104">Por [Scott Addie](https://github.com/scottaddie) e [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="d8065-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="d8065-105">ASP.NET Core 2,0 tem um novo modelo para autenticação e [Identity](xref:security/authentication/identity) que simplifica a configuração usando serviços.</span><span class="sxs-lookup"><span data-stu-id="d8065-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="d8065-106">ASP.NET Core aplicativos 1. x que usam autenticação ou que Identity podem ser atualizados para usar o novo modelo, conforme descrito abaixo.</span><span class="sxs-lookup"><span data-stu-id="d8065-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="d8065-107">Atualizar namespaces</span><span class="sxs-lookup"><span data-stu-id="d8065-107">Update namespaces</span></span>

<span data-ttu-id="d8065-108">Em 1. x, classes como `IdentityRole` e `IdentityUser` foram encontradas no `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span><span class="sxs-lookup"><span data-stu-id="d8065-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="d8065-109">Em 2,0, o <xref:Microsoft.AspNetCore.Identity> namespace tornou-se a nova casa para várias dessas classes.</span><span class="sxs-lookup"><span data-stu-id="d8065-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="d8065-110">Com o Identity código padrão, as classes afetadas incluem `ApplicationUser` e `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d8065-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="d8065-111">Ajuste suas `using` instruções para resolver as referências afetadas.</span><span class="sxs-lookup"><span data-stu-id="d8065-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="d8065-112">Middleware e serviços de autenticação</span><span class="sxs-lookup"><span data-stu-id="d8065-112">Authentication Middleware and services</span></span>

<span data-ttu-id="d8065-113">Em projetos 1. x, a autenticação é configurada via middleware.</span><span class="sxs-lookup"><span data-stu-id="d8065-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="d8065-114">Um método de middleware é invocado para cada esquema de autenticação ao qual você deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="d8065-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="d8065-115">O exemplo 1. x a seguir configura a autenticação do Facebook com Identity no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="d8065-116">Em 2,0 projetos, a autenticação é configurada por meio de serviços.</span><span class="sxs-lookup"><span data-stu-id="d8065-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="d8065-117">Cada esquema de autenticação é registrado no `ConfigureServices` método de *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8065-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="d8065-118">O `UseIdentity` método é substituído por `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="d8065-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="d8065-119">O exemplo 2,0 a seguir configura a autenticação do Facebook com Identity no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="d8065-120">O `UseAuthentication` método adiciona um único componente de middleware de autenticação, que é responsável pela autenticação automática e pela manipulação de solicitações de autenticação remota.</span><span class="sxs-lookup"><span data-stu-id="d8065-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="d8065-121">Ele substitui todos os componentes individuais do middleware por um único componente de middleware comum.</span><span class="sxs-lookup"><span data-stu-id="d8065-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="d8065-122">Abaixo estão 2,0 instruções de migração para cada esquema de autenticação principal.</span><span class="sxs-lookup"><span data-stu-id="d8065-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="d8065-123">Cookieautenticação baseada em</span><span class="sxs-lookup"><span data-stu-id="d8065-123">Cookie-based authentication</span></span>

<span data-ttu-id="d8065-124">Selecione uma das duas opções abaixo e faça as alterações necessárias em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="d8065-125">Usar cookie s comIdentity</span><span class="sxs-lookup"><span data-stu-id="d8065-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="d8065-126">Substituir `UseIdentity` por `UseAuthentication` no `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="d8065-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="d8065-127">Invoque o `AddIdentity` método no `ConfigureServices` método para adicionar os cookie serviços de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d8065-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="d8065-128">Opcionalmente, invoque o `ConfigureApplicationCookie` `ConfigureExternalCookie` método ou no `ConfigureServices` método para ajustar as Identity cookie configurações.</span><span class="sxs-lookup"><span data-stu-id="d8065-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="d8065-129">Usar cookie s semIdentity</span><span class="sxs-lookup"><span data-stu-id="d8065-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="d8065-130">Substitua a `UseCookieAuthentication` chamada de método no `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="d8065-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="d8065-131">Invoque os `AddAuthentication` `AddCookie` métodos e no `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d8065-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="d8065-132">Autenticação de portador JWT</span><span class="sxs-lookup"><span data-stu-id="d8065-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="d8065-133">Faça as seguintes alterações no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8065-134">Substitua a `UseJwtBearerAuthentication` chamada de método no `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="d8065-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8065-135">Invoque o `AddJwtBearer` método no `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d8065-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="d8065-136">Esse trecho de código não usa Identity , portanto, o esquema padrão deve ser definido passando `JwtBearerDefaults.AuthenticationScheme` para o `AddAuthentication` método.</span><span class="sxs-lookup"><span data-stu-id="d8065-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="d8065-137">Autenticação do OpenID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="d8065-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="d8065-138">Faça as seguintes alterações no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="d8065-139">Substitua a `UseOpenIdConnectAuthentication` chamada de método no `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="d8065-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8065-140">Invoque o `AddOpenIdConnect` método no `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d8065-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="d8065-141">Substitua a `PostLogoutRedirectUri` Propriedade na `OpenIdConnectOptions` ação por `SignedOutRedirectUri` :</span><span class="sxs-lookup"><span data-stu-id="d8065-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="d8065-142">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="d8065-142">Facebook authentication</span></span>

<span data-ttu-id="d8065-143">Faça as seguintes alterações no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8065-144">Substitua a `UseFacebookAuthentication` chamada de método no `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="d8065-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8065-145">Invoque o `AddFacebook` método no `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d8065-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="d8065-146">Autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="d8065-146">Google authentication</span></span>

<span data-ttu-id="d8065-147">Faça as seguintes alterações no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8065-148">Substitua a `UseGoogleAuthentication` chamada de método no `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="d8065-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8065-149">Invoque o `AddGoogle` método no `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d8065-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="d8065-150">Autenticação de Conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="d8065-150">Microsoft Account authentication</span></span>

<span data-ttu-id="d8065-151">Para obter mais informações sobre a autenticação conta Microsoft, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span><span class="sxs-lookup"><span data-stu-id="d8065-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="d8065-152">Faça as seguintes alterações no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-152">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8065-153">Substitua a `UseMicrosoftAccountAuthentication` chamada de método no `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="d8065-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8065-154">Invoque o `AddMicrosoftAccount` método no `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d8065-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="d8065-155">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="d8065-155">Twitter authentication</span></span>

<span data-ttu-id="d8065-156">Faça as seguintes alterações no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-156">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8065-157">Substitua a `UseTwitterAuthentication` chamada de método no `Configure` método por `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="d8065-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8065-158">Invoque o `AddTwitter` método no `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d8065-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="d8065-159">Configurando esquemas de autenticação padrão</span><span class="sxs-lookup"><span data-stu-id="d8065-159">Setting default authentication schemes</span></span>

<span data-ttu-id="d8065-160">Em 1. x, as `AutomaticAuthenticate` `AutomaticChallenge` Propriedades e da classe base [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) foram destinadas a serem definidas em um único esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d8065-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="d8065-161">Não havia uma boa maneira de impor isso.</span><span class="sxs-lookup"><span data-stu-id="d8065-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="d8065-162">Em 2,0, essas duas propriedades foram removidas como propriedades na `AuthenticationOptions` instância individual.</span><span class="sxs-lookup"><span data-stu-id="d8065-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="d8065-163">Eles podem ser configurados na `AddAuthentication` chamada do método dentro do `ConfigureServices` método de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="d8065-164">No trecho de código anterior, o esquema padrão é definido como `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="d8065-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="d8065-165">Como alternativa, use uma versão sobrecarregada do `AddAuthentication` método para definir mais de uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="d8065-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="d8065-166">No exemplo de método sobrecarregado a seguir, o esquema padrão é definido como `CookieAuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="d8065-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="d8065-167">O esquema de autenticação pode, opcionalmente, ser especificado em seus `[Authorize]` atributos individuais ou políticas de autorização.</span><span class="sxs-lookup"><span data-stu-id="d8065-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="d8065-168">Defina um esquema padrão em 2,0 se uma das seguintes condições for verdadeira:</span><span class="sxs-lookup"><span data-stu-id="d8065-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="d8065-169">Você deseja que o usuário seja conectado automaticamente</span><span class="sxs-lookup"><span data-stu-id="d8065-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="d8065-170">Você usa o `[Authorize]` atributo ou as políticas de autorização sem especificar esquemas</span><span class="sxs-lookup"><span data-stu-id="d8065-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="d8065-171">Uma exceção a essa regra é o `AddIdentity` método.</span><span class="sxs-lookup"><span data-stu-id="d8065-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="d8065-172">Esse método adiciona cookie s para você e define os esquemas de autenticação e desafio padrão para o aplicativo cookie `IdentityConstants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="d8065-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="d8065-173">Além disso, ele define o esquema de entrada padrão para o externo cookie `IdentityConstants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="d8065-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="d8065-174">Usar extensões de autenticação do HttpContext</span><span class="sxs-lookup"><span data-stu-id="d8065-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="d8065-175">A `IAuthenticationManager` interface é o ponto de entrada principal no sistema de autenticação 1. x.</span><span class="sxs-lookup"><span data-stu-id="d8065-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="d8065-176">Ele foi substituído por um novo conjunto de `HttpContext` métodos de extensão no `Microsoft.AspNetCore.Authentication` namespace.</span><span class="sxs-lookup"><span data-stu-id="d8065-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="d8065-177">Por exemplo, os projetos 1. x fazem referência a uma `Authentication` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="d8065-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="d8065-178">Em 2,0 projetos, importe o `Microsoft.AspNetCore.Authentication` namespace e exclua as `Authentication` referências de propriedade:</span><span class="sxs-lookup"><span data-stu-id="d8065-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="d8065-179">Autenticação do Windows (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="d8065-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="d8065-180">Há duas variações de autenticação do Windows:</span><span class="sxs-lookup"><span data-stu-id="d8065-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="d8065-181">O host só permite usuários autenticados.</span><span class="sxs-lookup"><span data-stu-id="d8065-181">The host only allows authenticated users.</span></span> <span data-ttu-id="d8065-182">Essa variação não é afetada pelas alterações 2,0.</span><span class="sxs-lookup"><span data-stu-id="d8065-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="d8065-183">O host permite usuários anônimos e autenticados.</span><span class="sxs-lookup"><span data-stu-id="d8065-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="d8065-184">Essa variação é afetada pelas alterações 2,0.</span><span class="sxs-lookup"><span data-stu-id="d8065-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="d8065-185">Por exemplo, o aplicativo deve permitir usuários anônimos no [IIS](xref:host-and-deploy/iis/index) ou [HTTP.sys](xref:fundamentals/servers/httpsys) camada, mas autorizar os usuários no nível do controlador.</span><span class="sxs-lookup"><span data-stu-id="d8065-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="d8065-186">Nesse cenário, defina o esquema padrão no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d8065-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="d8065-187">Para [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), defina o esquema padrão como `IISDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="d8065-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="d8065-188">Para [Microsoft. AspNetCore. Server. httpsa](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), defina o esquema padrão como `HttpSysDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="d8065-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="d8065-189">Falha ao definir o esquema padrão impede que a solicitação de autorização (desafio) funcione com a seguinte exceção:</span><span class="sxs-lookup"><span data-stu-id="d8065-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="d8065-190">`System.InvalidOperationException`: Nenhum authenticationScheme foi especificado e nenhum DefaultChallengeScheme foi encontrado.</span><span class="sxs-lookup"><span data-stu-id="d8065-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="d8065-191">Para obter mais informações, consulte <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="d8065-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="d8065-192">IdentityCookieInstâncias de opções</span><span class="sxs-lookup"><span data-stu-id="d8065-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="d8065-193">Um efeito colateral das alterações 2,0 é a opção de usar opções nomeadas em vez de cookie instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="d8065-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="d8065-194">A capacidade de personalizar os Identity cookie nomes de esquema é removida.</span><span class="sxs-lookup"><span data-stu-id="d8065-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="d8065-195">Por exemplo, os projetos 1. x usam [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) para passar um parâmetro para `IdentityCookieOptions` *AccountController.cs* e *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8065-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="d8065-196">O cookie esquema de autenticação externa é acessado da instância fornecida:</span><span class="sxs-lookup"><span data-stu-id="d8065-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="d8065-197">A injeção de Construtor mencionada torna-se desnecessária em projetos 2,0, e o `_externalCookieScheme` campo pode ser excluído:</span><span class="sxs-lookup"><span data-stu-id="d8065-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="d8065-198">os projetos 1. x usaram o `_externalCookieScheme` campo da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d8065-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="d8065-199">Em 2,0 projetos, substitua o código anterior pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="d8065-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="d8065-200">A `IdentityConstants.ExternalScheme` constante pode ser usada diretamente.</span><span class="sxs-lookup"><span data-stu-id="d8065-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="d8065-201">Resolva a chamada recém adicionada `SignOutAsync` importando o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="d8065-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="d8065-202">Adicionar Identity Propriedades de navegação do usuário poco</span><span class="sxs-lookup"><span data-stu-id="d8065-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="d8065-203">As propriedades de navegação principal do Entity Framework (EF) do `IdentityUser` poco de base (objeto comum CLR) foram removidas.</span><span class="sxs-lookup"><span data-stu-id="d8065-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="d8065-204">Se o projeto 1. x usou essas propriedades, adicione-as manualmente de volta ao projeto 2,0:</span><span class="sxs-lookup"><span data-stu-id="d8065-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="d8065-205">Para evitar chaves estrangeiras duplicadas ao executar EF Core migrações, adicione o seguinte ao `IdentityDbContext` método de classe `OnModelCreating` (após a `base.OnModelCreating();` chamada):</span><span class="sxs-lookup"><span data-stu-id="d8065-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="d8065-206">Substituir GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="d8065-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="d8065-207">O método síncrono `GetExternalAuthenticationSchemes` foi removido em favor de uma versão assíncrona.</span><span class="sxs-lookup"><span data-stu-id="d8065-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="d8065-208">os projetos 1. x têm o seguinte código em *Controllers/ManageController. cs*:</span><span class="sxs-lookup"><span data-stu-id="d8065-208">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="d8065-209">Esse método aparece em *views/Account/Login. cshtml* também:</span><span class="sxs-lookup"><span data-stu-id="d8065-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="d8065-210">Em 2,0 projetos, use o <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> método.</span><span class="sxs-lookup"><span data-stu-id="d8065-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="d8065-211">A alteração em *ManageController.cs* é semelhante ao seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d8065-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="d8065-212">No *login. cshtml*, a `AuthenticationScheme` propriedade acessada no `foreach` loop muda para `Name` :</span><span class="sxs-lookup"><span data-stu-id="d8065-212">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="d8065-213">Alteração da propriedade ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="d8065-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="d8065-214">Um `ManageLoginsViewModel` objeto é usado na `ManageLogins` ação de *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8065-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="d8065-215">Em projetos 1. x, o tipo de `OtherLogins` retorno da Propriedade do objeto é `IList<AuthenticationDescription>` .</span><span class="sxs-lookup"><span data-stu-id="d8065-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="d8065-216">Esse tipo de retorno requer uma importação de `Microsoft.AspNetCore.Http.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="d8065-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="d8065-217">Em 2,0 projetos, o tipo de retorno é alterado para `IList<AuthenticationScheme>` .</span><span class="sxs-lookup"><span data-stu-id="d8065-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="d8065-218">Esse novo tipo de retorno requer a substituição da `Microsoft.AspNetCore.Http.Authentication` importação por uma `Microsoft.AspNetCore.Authentication` importação.</span><span class="sxs-lookup"><span data-stu-id="d8065-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="d8065-219">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d8065-219">Additional resources</span></span>

<span data-ttu-id="d8065-220">Para obter mais informações, consulte o artigo [discussão do problema de autenticação 2,0](https://github.com/aspnet/Security/issues/1338) no github.</span><span class="sxs-lookup"><span data-stu-id="d8065-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
