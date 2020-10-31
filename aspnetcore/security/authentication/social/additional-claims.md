---
title: Manter declarações e tokens adicionais de provedores externos no ASP.NET Core
author: rick-anderson
description: Saiba como estabelecer declarações e tokens adicionais de provedores externos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 4503291ff887f79b1ad6eacd4e56943ce23335bc
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141502"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Manter declarações e tokens adicionais de provedores externos no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Um aplicativo ASP.NET Core pode estabelecer declarações e tokens adicionais de provedores de autenticação externos, como Facebook, Google, Microsoft e Twitter. Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados do usuário em declarações adicionais é o mesmo.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

Decida quais provedores de autenticação externa dar suporte no aplicativo. Para cada provedor, registre o aplicativo e obtenha uma ID do cliente e um segredo do cliente. Para obter mais informações, consulte <xref:security/authentication/social/index>. O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Definir a ID do cliente e o segredo do cliente

O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID do cliente e o segredo do cliente. Os valores de ID do cliente e segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor. Cada provedor externo que o aplicativo usa deve ser configurado independentemente com a ID do cliente do provedor e o segredo do cliente. Para obter mais informações, consulte os tópicos do provedor de autenticação externa que se aplicam ao seu cenário:

* [Autenticação do Facebook](xref:security/authentication/facebook-logins)
* [Autenticação do Google](xref:security/authentication/google-logins)
* [Autenticação da Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticação do Twitter](xref:security/authentication/twitter-logins)
* [Outros provedores de autenticação](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e o segredo do cliente fornecidos pelo Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Estabelecer o escopo de autenticação

Especifique a lista de permissões a serem recuperadas do provedor especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . Os escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.

| Provedor  | Escopo                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `profile`, `email`, `openid`                                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

No aplicativo de exemplo, os `profile` `email` escopos, e do Google `openid` são adicionados automaticamente pelo Framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Se o aplicativo exigir escopos adicionais, adicione-os às opções. No exemplo a seguir, o escopo do Google `https://www.googleapis.com/auth/user.birthday.read` é adicionado para recuperar o aniversário de um usuário:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapear chaves de dados do usuário e criar declarações

Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário JSON do provedor externo para que a identidade do aplicativo seja lida na entrada. Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes> .

O aplicativo de exemplo cria `urn:google:locale` declarações de localidade () e de imagem ( `urn:google:picture` ) das `locale` `picture` chaves e nos dados de usuário do Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

No `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , um <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . Durante o processo de entrada, o <xref:Microsoft.AspNetCore.Identity.UserManager%601> pode armazenar uma `ApplicationUser` declaração de dados do usuário disponível no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

No aplicativo de exemplo, `OnPostConfirmationAsync` ( *Account/ExternalLogin. cshtml. cs* ) estabelece as declarações de locale ( `urn:google:locale` ) e Picture ( `urn:google:picture` ) para o conectado `ApplicationUser` , incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Por padrão, as declarações de um usuário são armazenadas na autenticação do cookie . Se a autenticação cookie for muito grande, isso poderá causar falha no aplicativo porque:

* O navegador detecta que o cookie cabeçalho é muito longo.
* O tamanho geral da solicitação é muito grande.

Se uma grande quantidade de dados do usuário for necessária para processar solicitações do usuário:

* Limite o número e o tamanho das declarações do usuário para o processamento da solicitação apenas para o que o aplicativo requer.
* Use um personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para o Cookie middleware de autenticação <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações. Preserve grandes quantidades de informações de identidade no servidor ao enviar apenas uma pequena chave de identificador de sessão para o cliente.

## <a name="save-the-access-token"></a>Salvar o token de acesso

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> define se os tokens de acesso e de atualização devem ser armazenados no <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida. `SaveTokens` é definido como `false` por padrão para reduzir o tamanho da autenticação final cookie .

O aplicativo de exemplo define o valor de `SaveTokens` para `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Quando `OnPostConfirmationAsync` o é executado, armazene o token de acesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo `ApplicationUser` no `AuthenticationProperties` .

O aplicativo de exemplo salva o token de acesso em `OnPostConfirmationAsync` (novo registro de usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin. cshtml. cs* :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Como adicionar tokens personalizados adicionais

Para demonstrar como adicionar um token personalizado, que é armazenado como parte do `SaveTokens` , o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Criando e adicionando declarações

A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção. Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .

Os usuários podem definir ações personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando o <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstract.

Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Remoção de declarações e ações de declaração

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as ações de declaração para o dado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração do dado da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> identidade. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> é usado principalmente com o [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para remover declarações geradas por protocolo.

## <a name="sample-app-output"></a>Saída do aplicativo de exemplo

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um aplicativo ASP.NET Core pode estabelecer declarações e tokens adicionais de provedores de autenticação externos, como Facebook, Google, Microsoft e Twitter. Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados do usuário em declarações adicionais é o mesmo.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

Decida quais provedores de autenticação externa dar suporte no aplicativo. Para cada provedor, registre o aplicativo e obtenha uma ID do cliente e um segredo do cliente. Para obter mais informações, consulte <xref:security/authentication/social/index>. O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Definir a ID do cliente e o segredo do cliente

O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID do cliente e o segredo do cliente. Os valores de ID do cliente e segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor. Cada provedor externo que o aplicativo usa deve ser configurado independentemente com a ID do cliente do provedor e o segredo do cliente. Para obter mais informações, consulte os tópicos do provedor de autenticação externa que se aplicam ao seu cenário:

* [Autenticação do Facebook](xref:security/authentication/facebook-logins)
* [Autenticação do Google](xref:security/authentication/google-logins)
* [Autenticação da Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticação do Twitter](xref:security/authentication/twitter-logins)
* [Outros provedores de autenticação](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e o segredo do cliente fornecidos pelo Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Estabelecer o escopo de autenticação

Especifique a lista de permissões a serem recuperadas do provedor especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . Os escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.

| Provedor  | Escopo                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

No aplicativo de exemplo, o escopo do Google `userinfo.profile` é adicionado automaticamente pelo Framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Se o aplicativo exigir escopos adicionais, adicione-os às opções. No exemplo a seguir, o escopo do Google `https://www.googleapis.com/auth/user.birthday.read` é adicionado para recuperar o aniversário de um usuário:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapear chaves de dados do usuário e criar declarações

Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário JSON do provedor externo para que a identidade do aplicativo seja lida na entrada. Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes> .

O aplicativo de exemplo cria `urn:google:locale` declarações de localidade () e de imagem ( `urn:google:picture` ) das `locale` `picture` chaves e nos dados de usuário do Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

No `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , um <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . Durante o processo de entrada, o <xref:Microsoft.AspNetCore.Identity.UserManager%601> pode armazenar uma `ApplicationUser` declaração de dados do usuário disponível no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

No aplicativo de exemplo, `OnPostConfirmationAsync` ( *Account/ExternalLogin. cshtml. cs* ) estabelece as declarações de locale ( `urn:google:locale` ) e Picture ( `urn:google:picture` ) para o conectado `ApplicationUser` , incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Por padrão, as declarações de um usuário são armazenadas na autenticação do cookie . Se a autenticação cookie for muito grande, isso poderá causar falha no aplicativo porque:

* O navegador detecta que o cookie cabeçalho é muito longo.
* O tamanho geral da solicitação é muito grande.

Se uma grande quantidade de dados do usuário for necessária para processar solicitações do usuário:

* Limite o número e o tamanho das declarações do usuário para o processamento da solicitação apenas para o que o aplicativo requer.
* Use um personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para o Cookie middleware de autenticação <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações. Preserve grandes quantidades de informações de identidade no servidor ao enviar apenas uma pequena chave de identificador de sessão para o cliente.

## <a name="save-the-access-token"></a>Salvar o token de acesso

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> define se os tokens de acesso e de atualização devem ser armazenados no <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida. `SaveTokens` é definido como `false` por padrão para reduzir o tamanho da autenticação final cookie .

O aplicativo de exemplo define o valor de `SaveTokens` para `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Quando `OnPostConfirmationAsync` o é executado, armazene o token de acesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo `ApplicationUser` no `AuthenticationProperties` .

O aplicativo de exemplo salva o token de acesso em `OnPostConfirmationAsync` (novo registro de usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin. cshtml. cs* :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Como adicionar tokens personalizados adicionais

Para demonstrar como adicionar um token personalizado, que é armazenado como parte do `SaveTokens` , o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Criando e adicionando declarações

A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção. Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .

Os usuários podem definir ações personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando o <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstract.

Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Remoção de declarações e ações de declaração

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as ações de declaração para o dado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração do dado da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> identidade. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> é usado principalmente com o [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para remover declarações geradas por protocolo.

## <a name="sample-app-output"></a>Saída do aplicativo de exemplo

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [dotnet/AspNetCore Engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): o aplicativo de exemplo vinculado está no Branch de engenharia [do repositório do GitHub dotnet/AspNetCore](https://github.com/dotnet/AspNetCore) `master` . A `master` ramificação contém código em desenvolvimento ativo para a próxima versão do ASP.NET Core. Para ver uma versão do aplicativo de exemplo para uma versão lançada do ASP.NET Core, use a lista suspensa **Branch** para selecionar um Branch de lançamento (por exemplo `release/{X.Y}` ).
