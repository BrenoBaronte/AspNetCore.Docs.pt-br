---
title: Configurar ASP.NET CoreIdentity
author: AdrienTorris
description: Entenda ASP.NET Core Identity valores padrão e saiba como configurar Identity Propriedades para usar valores personalizados.
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
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
uid: security/authentication/identity-configuration
ms.openlocfilehash: b7f6eaba1a0e819a077e3d63b4f997e75b8cd317
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020594"
---
# <a name="configure-aspnet-core-no-locidentity"></a>Configurar ASP.NET CoreIdentity

ASP.NET Core Identity usa valores padrão para configurações como política de senha, bloqueio e cookie configuração. Essas configurações podem ser substituídas na `Startup` classe.

## <a name="no-locidentity-options"></a>IdentityOpções

A classe [ Identity Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) representa as opções que podem ser usadas para configurar o Identity sistema. `IdentityOptions`deve ser definido **após** a chamada `AddIdentity` ou `AddDefaultIdentity` .

### <a name="claims-no-locidentity"></a>DeclaraçõesIdentity

[ Identity Options. Claims Identity ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) especifica [as Identity Opções de declarações](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) com as propriedades mostradas na tabela a seguir.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Obtém ou define o tipo de declaração usado para uma declaração de função. | [ClaimTypes. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Obtém ou define o tipo de declaração usado para a declaração de carimbo de segurança. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Obtém ou define o tipo de declaração usado para a declaração de identificador de usuário. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Obtém ou define o tipo de declaração usado para a declaração de nome de usuário. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Bloquear

O bloqueio é definido no método [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

O código anterior se baseia no `Login` Identity modelo. 

As opções de bloqueio são definidas em `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

O código anterior define as [ Identity Opções](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [lockoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) com valores padrão.

Uma autenticação bem-sucedida redefine a contagem de tentativas de acesso com falha e redefine o relógio.

[ Identity Options. Lock](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) especifica o [lockoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Determina se um novo usuário pode ser bloqueado. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | A quantidade de tempo que um usuário é bloqueado quando ocorre um bloqueio. | 5 minutos |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | O número de tentativas de acesso com falha até que um usuário seja bloqueado, se o bloqueio estiver habilitado. | 5 |

### <a name="password"></a>Senha

Por padrão, Identity o requer que as senhas contenham um caractere maiúsculo, um caractere minúsculo, um dígito e um caractere não alfanumérico. As senhas devem ter pelo menos seis caracteres.

As senhas são configuradas com:

* <xref:Microsoft.AspNetCore.Identity.PasswordOptions>em `Startup.ConfigureServices` .
* [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) de `Password` Propriedades se o Identity for [com Scaffold no aplicativo](xref:security/authentication/scaffold-identity). `InputModel``Password`as propriedades são encontradas nos seguintes arquivos:
  * `Areas/Identity/Pages/Account/Register.cshtml.cs`
  * `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ Identity Opções. senha](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) especifica a [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Requer um número entre 0-9 na senha. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | O comprimento mínimo da senha. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Requer um caractere minúsculo na senha. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Requer um caractere não alfanumérico na senha. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Aplica-se somente ao ASP.NET Core 2,0 ou posterior.<br><br> Requer o número de caracteres distintos na senha. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Requer um caractere maiúsculo na senha. | `true` |

### <a name="sign-in"></a>Conexão

O código a seguir define `SignIn` as configurações (para valores padrão):

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ Identity Options. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) especifica o [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Requer um email confirmado para entrar. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Requer um número de telefone confirmado para entrar. | `false` |

### <a name="tokens"></a>Tokens

[ Identity Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) especifica o [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição |
| -------- | ----------- |
| [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | Obtém ou define o `AuthenticatorTokenProvider` usado para validar as entradas de dois fatores com um autenticador. |
| [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | Obtém ou define o `ChangeEmailTokenProvider` usado para gerar tokens usados em emails de confirmação de alteração de e-mail. |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | Obtém ou define o `ChangePhoneNumberTokenProvider` usado para gerar tokens usados ao alterar números de telefone. |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Obtém ou define o provedor de token usado para gerar tokens usados em emails de confirmação de conta. |
| [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Obtém ou define o [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) usado para gerar tokens usados em emails de redefinição de senha. |
| [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Usado para construir um [provedor de token de usuário](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) com a chave usada como o nome do provedor. |

### <a name="user"></a>Usuário

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ Identity Options. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) especifica o [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Caracteres permitidos no nome de usuário. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Requer que cada usuário tenha um email exclusivo. | `false` |

### <a name="no-loccookie-settings"></a>Configurações doCookie

Configure o aplicativo cookie no `Startup.ConfigureServices` . [ConfigureApplication Cookie ](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) deve ser chamado **depois** `AddIdentity` de chamar ou `AddDefaultIdentity` .

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Para obter mais informações, consulte [ Cookie authenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Opções de hash de senha

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Obtém e define opções para o hash de senha.

| Opção | Descrição |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | O modo de compatibilidade usado ao aplicar o hash de novas senhas. Assume o padrão de <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>. O primeiro byte de uma senha com hash, chamado de *marcador de formato*, especifica a versão do algoritmo de hash usado para fazer o hash da senha. Ao verificar uma senha em relação a um hash, o <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> método seleciona o algoritmo correto com base no primeiro byte. Um cliente é capaz de autenticar, independentemente de qual versão do algoritmo foi usada para fazer o hash da senha. Definir o modo de compatibilidade afeta o hash de *novas senhas*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | O número de iterações usadas ao aplicar o hash de senhas usando PBKDF2. Esse valor é usado somente quando o <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> é definido como <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . O valor deve ser um inteiro positivo e o padrão é `10000` . |

No exemplo a seguir, o <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> é definido como `12000` em `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Exigir globalmente que todos os usuários sejam autenticados

[!INCLUDE[](~/includes/requireAuth.md)]