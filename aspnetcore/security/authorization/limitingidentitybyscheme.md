---
title: Autorizar com um esquema específico no ASP.NET Core
author: rick-anderson
description: Este artigo explica como limitar a identidade a um esquema específico ao trabalhar com vários métodos de autenticação.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 66b307a3629e18e49b5bb6e65a156054c0002ba8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022102"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Autorizar com um esquema específico no ASP.NET Core

Em alguns cenários, como SPAs (aplicativos de página única), é comum usar vários métodos de autenticação. Por exemplo, o aplicativo pode usar a cookie autenticação com base para fazer logon e autenticação de portador JWT para solicitações de JavaScript. Em alguns casos, o aplicativo pode ter várias instâncias de um manipulador de autenticação. Por exemplo, dois cookie manipuladores em que um contém uma identidade básica e outro são criados quando uma MFA (autenticação multifator) foi disparada. A MFA pode ser disparada porque o usuário solicitou uma operação que requer segurança extra. Para obter mais informações sobre a imposição de MFA quando um usuário solicita um recurso que requer MFA, consulte a seção problema de proteção do GitHub [com MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).

Um esquema de autenticação é nomeado quando o serviço de autenticação é configurado durante a autenticação. Por exemplo:

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

No código anterior, dois manipuladores de autenticação foram adicionados: um para cookie s e outro para portador.

>[!NOTE]
>Especificar o esquema padrão resulta na `HttpContext.User` propriedade que está sendo definida para essa identidade. Se esse comportamento não for desejado, desabilite-o invocando a forma sem parâmetros de `AddAuthentication` .

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Selecionando o esquema com o atributo Authorize

No ponto de autorização, o aplicativo indica o manipulador a ser usado. Selecione o manipulador com o qual o aplicativo será autorizado, passando uma lista delimitada por vírgulas de esquemas de autenticação para o `[Authorize]` . O `[Authorize]` atributo especifica o esquema de autenticação ou os esquemas a serem usados, independentemente de um padrão estar configurado. Por exemplo:

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

No exemplo anterior, os cookie manipuladores e de portador são executados e têm a oportunidade de criar e acrescentar uma identidade para o usuário atual. Ao especificar apenas um único esquema, o manipulador correspondente é executado.

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

No código anterior, apenas o manipulador com o esquema "portador" é executado. As cookie identidades baseadas em qualquer um são ignoradas.

## <a name="selecting-the-scheme-with-policies"></a>Selecionando o esquema com políticas

Se preferir especificar os esquemas desejados na [política](xref:security/authorization/policies), você poderá definir a `AuthenticationSchemes` coleção ao adicionar a política:

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

No exemplo anterior, a política "Over18" só é executada em relação à identidade criada pelo manipulador "portador". Use a política definindo a `[Authorize]` Propriedade do atributo `Policy` :

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Usar vários esquemas de autenticação

Alguns aplicativos podem precisar dar suporte a vários tipos de autenticação. Por exemplo, seu aplicativo pode autenticar usuários de Azure Active Directory e de um banco de dados de usuários. Outro exemplo é um aplicativo que autentica os usuários de Serviços de Federação do Active Directory (AD FS) e Azure Active Directory B2C. Nesse caso, o aplicativo deve aceitar um token de portador JWT de vários emissores.

Adicione todos os esquemas de autenticação que você gostaria de aceitar. Por exemplo, o código a seguir no `Startup.ConfigureServices` adiciona dois esquemas de autenticação de portador JWT com emissores diferentes:

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
> Somente uma autenticação de portador JWT é registrada com o esquema de autenticação padrão `JwtBearerDefaults.AuthenticationScheme` . A autenticação adicional deve ser registrada com um esquema de autenticação exclusivo.

A próxima etapa é atualizar a política de autorização padrão para aceitar os dois esquemas de autenticação. Por exemplo:

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

Como a política de autorização padrão é substituída, é possível usar o `[Authorize]` atributo em controladores. Em seguida, o controlador aceita solicitações com JWT emitido pelo primeiro ou segundo emissor.

::: moniker-end
