---
title: Compartilhar s de autenticação cookie entre aplicativos ASP.net
author: rick-anderson
description: Saiba como compartilhar os s de autenticação cookie entre os aplicativos ASP.NET 4. x e ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059683"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Compartilhar s de autenticação cookie entre aplicativos ASP.net

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Os sites geralmente consistem em aplicativos Web individuais trabalhando juntos. Para fornecer uma experiência de logon único (SSO), os aplicativos Web em um site devem compartilhar os cookie s de autenticação. Para dar suporte a esse cenário, a pilha de proteção de dados permite o compartilhamento de cookie autenticação Katana e cookie tíquetes de autenticação de ASP.NET Core.

Nos exemplos a seguir:

* O nome de autenticação cookie é definido como um valor comum de `.AspNet.SharedCookie` .
* O `AuthenticationType` é definido como `Identity.Application` explicitamente ou por padrão.
* Um nome de aplicativo comum é usado para permitir que o sistema de proteção de dados Compartilhe chaves de proteção de dados ( `SharedCookieApp` ).
* `Identity.Application` é usado como o esquema de autenticação. Seja qual for o esquema usado, ele deve ser usado consistentemente *dentro e entre* os cookie aplicativos compartilhados como o esquema padrão ou definindo-o explicitamente. O esquema é usado ao criptografar e descriptografar cookie s, portanto, um esquema consistente deve ser usado em aplicativos.
* Um local de armazenamento de [chave de proteção de dados](xref:security/data-protection/implementation/key-management) comum é usado.
  * Em ASP.NET Core aplicativos, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> é usado para definir o local de armazenamento de chaves.
  * Em aplicativos .NET Framework, Cookie o middleware de autenticação usa uma implementação de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider` fornece serviços de proteção de dados para criptografia e descriptografia de cookie dados de carga de autenticação. A `DataProtectionProvider` instância é isolada do sistema de proteção de dados usado por outras partes do aplicativo. [Dataprotectionprovider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) aceita um <xref:System.IO.DirectoryInfo> para especificar o local para armazenamento de chave de proteção de dados.
* `DataProtectionProvider` requer o pacote NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * Em aplicativos ASP.NET Core 2. x, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * Em .NET Framework aplicativos, adicione uma referência de pacote a [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> define o nome comum do aplicativo.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>Compartilhar cookie s de autenticação com ASP.NET Core Identity

Ao usar ASP.NET Core Identity:

* As chaves de proteção de dados e o nome do aplicativo devem ser compartilhados entre aplicativos. Um local de armazenamento de chave comum é fornecido para o <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> método nos exemplos a seguir. Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar um nome de aplicativo compartilhado comum ( `SharedCookieApp` nos exemplos a seguir). Para obter mais informações, consulte <xref:security/data-protection/configuration/overview>.
* Use o <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> método de extensão para configurar o serviço de proteção de dados para cookie s.
* O tipo de autenticação padrão é `Identity.Application` .

Em `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>Compartilhar s de autenticação cookie sem ASP.NET Core Identity

Ao usar cookie s diretamente sem ASP.NET Core Identity , configure a proteção de dados e a autenticação no `Startup.ConfigureServices` . No exemplo a seguir, o tipo de autenticação é definido como `Identity.Application` :

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a>Compartilhar cookie s em caminhos de base diferentes

Uma autenticação cookie usa [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) como padrão [ Cookie . Caminho](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Se o aplicativo cookie deve ser compartilhado entre caminhos base diferentes, `Path` deve ser substituído:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Compartilhar cookie s em subdomínios

Ao hospedar aplicativos que compartilham cookie s em subdomínios, especifique um domínio comum no [ Cookie . ](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)Propriedade de domínio. Para compartilhar cookie s entre aplicativos em `contoso.com` , como `first_subdomain.contoso.com` e `second_subdomain.contoso.com` , especifique o `Cookie.Domain` como `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Criptografar chaves de proteção de dados em repouso

Para implantações de produção, configure o `DataProtectionProvider` para criptografar chaves em repouso com DPAPI ou um X509Certificate. Para obter mais informações, consulte <xref:security/data-protection/implementation/key-encryption-at-rest>. No exemplo a seguir, uma impressão digital do certificado é fornecida para <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>Compartilhar os cookie s de autenticação entre os aplicativos ASP.NET 4. x e ASP.NET Core

Os aplicativos ASP.NET 4. x que usam Cookie o middleware de autenticação Katana podem ser configurados para gerar autenticação cookie s que são compatíveis com o Cookie middleware de autenticação ASP.NET Core. Isso permite atualizar aplicativos individuais de um site grande em várias etapas, ao mesmo tempo em que fornece uma experiência de SSO tranqüila em todo o site.

Quando um aplicativo usa Cookie o middleware de autenticação Katana, ele chama `UseCookieAuthentication` o arquivo *Startup.auth.cs* do projeto. Os projetos de aplicativo Web ASP.NET 4. x criados com Visual Studio 2013 e posterior usam o Cookie middleware de autenticação Katana por padrão. Embora `UseCookieAuthentication` o seja obsoleto e não tenha suporte para aplicativos ASP.NET Core, chamar `UseCookieAuthentication` um aplicativo ASP.NET 4. x que usa o Cookie middleware de autenticação Katana é válido.

Um aplicativo ASP.NET 4. x deve ter como destino .NET Framework 4.5.1 ou posterior. Caso contrário, os pacotes NuGet necessários falharão na instalação.

Para compartilhar cookie os s de autenticação entre um aplicativo ASP.NET 4. x e um aplicativo ASP.NET Core, configure o aplicativo ASP.NET Core conforme indicado na seção [s de autenticação de compartilhamento cookie entre ASP.NET Core aplicativos](#share-authentication-cookies-with-aspnet-core-identity) e, em seguida, configure o aplicativo ASP.NET 4. x da seguinte maneira.

Confirme se os pacotes do aplicativo foram atualizados para as versões mais recentes. Instale o pacote [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) em cada aplicativo ASP.NET 4. x.

Localize e modifique a chamada para `UseCookieAuthentication` :

* Altere o cookie nome para corresponder ao nome usado pelo middleware de Cookie autenticação ASP.NET Core ( `.AspNet.SharedCookie` no exemplo).
* No exemplo a seguir, o tipo de autenticação é definido como `Identity.Application` .
* Forneça uma instância de um `DataProtectionProvider` inicializado para o local de armazenamento de chave de proteção de dados comum.
* Confirme se o nome do aplicativo está definido como o nome do aplicativo comum usado por todos os aplicativos que compartilham os s de autenticação cookie ( `SharedCookieApp` no exemplo).

Se não estiver configurando `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , defina <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> como uma declaração que diferencie usuários exclusivos.

*App_Start/Startup.auth.cs* :

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

Ao gerar uma identidade de usuário, o tipo de autenticação ( `Identity.Application` ) deve corresponder ao tipo definido em `AuthenticationType` conjunto com `UseCookieAuthentication` em *App_Start/Startup.auth.cs* .

*Modelos/ Identity Models.cs* :

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Usar um banco de dados de usuário comum

Quando os aplicativos usam o mesmo Identity esquema (mesma versão do Identity ), confirme se o Identity sistema de cada aplicativo está apontado para o mesmo banco de dados de usuário. Caso contrário, o sistema de identidade produz falhas em tempo de execução quando tenta corresponder as informações na autenticação em cookie relação às informações em seu banco de dados.

Quando o Identity esquema é diferente entre os aplicativos, geralmente porque os aplicativos estão usando Identity versões diferentes, compartilhar um banco de dados comum com base na versão mais recente do Identity não é possível sem remapear e adicionar colunas nos esquemas de outros aplicativos Identity . Geralmente, é mais eficiente atualizar os outros aplicativos para usar a versão mais recente Identity para que um banco de dados comum possa ser compartilhado pelos aplicativos.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:host-and-deploy/web-farm>
