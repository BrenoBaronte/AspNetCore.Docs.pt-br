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
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252988"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="f305c-103">Compartilhar s de autenticação cookie entre aplicativos ASP.net</span><span class="sxs-lookup"><span data-stu-id="f305c-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="f305c-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f305c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f305c-105">Os sites geralmente consistem em aplicativos Web individuais trabalhando juntos.</span><span class="sxs-lookup"><span data-stu-id="f305c-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="f305c-106">Para fornecer uma experiência de logon único (SSO), os aplicativos Web em um site devem compartilhar os cookie s de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f305c-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="f305c-107">Para dar suporte a esse cenário, a pilha de proteção de dados permite o compartilhamento de cookie autenticação Katana e cookie tíquetes de autenticação de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f305c-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="f305c-108">Nos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="f305c-108">In the examples that follow:</span></span>

* <span data-ttu-id="f305c-109">O nome de autenticação cookie é definido como um valor comum de `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="f305c-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="f305c-110">O `AuthenticationType` é definido como `Identity.Application` explicitamente ou por padrão.</span><span class="sxs-lookup"><span data-stu-id="f305c-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="f305c-111">Um nome de aplicativo comum é usado para permitir que o sistema de proteção de dados Compartilhe chaves de proteção de dados ( `SharedCookieApp` ).</span><span class="sxs-lookup"><span data-stu-id="f305c-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="f305c-112">`Identity.Application` é usado como o esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f305c-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="f305c-113">Seja qual for o esquema usado, ele deve ser usado consistentemente *dentro e entre* os cookie aplicativos compartilhados como o esquema padrão ou definindo-o explicitamente.</span><span class="sxs-lookup"><span data-stu-id="f305c-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="f305c-114">O esquema é usado ao criptografar e descriptografar cookie s, portanto, um esquema consistente deve ser usado em aplicativos.</span><span class="sxs-lookup"><span data-stu-id="f305c-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="f305c-115">Um local de armazenamento de [chave de proteção de dados](xref:security/data-protection/implementation/key-management) comum é usado.</span><span class="sxs-lookup"><span data-stu-id="f305c-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="f305c-116">Em ASP.NET Core aplicativos, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> é usado para definir o local de armazenamento de chaves.</span><span class="sxs-lookup"><span data-stu-id="f305c-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="f305c-117">Em aplicativos .NET Framework, Cookie o middleware de autenticação usa uma implementação de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="f305c-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="f305c-118">`DataProtectionProvider` fornece serviços de proteção de dados para criptografia e descriptografia de cookie dados de carga de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f305c-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="f305c-119">A `DataProtectionProvider` instância é isolada do sistema de proteção de dados usado por outras partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f305c-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="f305c-120">[Dataprotectionprovider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) aceita um <xref:System.IO.DirectoryInfo> para especificar o local para armazenamento de chave de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="f305c-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="f305c-121">`DataProtectionProvider` requer o pacote NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="f305c-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="f305c-122">Em aplicativos ASP.NET Core 2. x, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f305c-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="f305c-123">Em .NET Framework aplicativos, adicione uma referência de pacote a [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="f305c-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="f305c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> define o nome comum do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f305c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="f305c-125">Compartilhar cookie s de autenticação com ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f305c-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="f305c-126">Ao usar ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="f305c-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f305c-127">As chaves de proteção de dados e o nome do aplicativo devem ser compartilhados entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="f305c-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="f305c-128">Um local de armazenamento de chave comum é fornecido para o <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> método nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="f305c-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="f305c-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar um nome de aplicativo compartilhado comum ( `SharedCookieApp` nos exemplos a seguir).</span><span class="sxs-lookup"><span data-stu-id="f305c-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="f305c-130">Para obter mais informações, consulte <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="f305c-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="f305c-131">Use o <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> método de extensão para configurar o serviço de proteção de dados para cookie s.</span><span class="sxs-lookup"><span data-stu-id="f305c-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="f305c-132">O tipo de autenticação padrão é `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="f305c-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="f305c-133">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f305c-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="f305c-134">**Observação:** As instruções anteriores não funcionam com o `ITicketStore` ( `CookieAuthenticationOptions.SessionStore` ).</span><span class="sxs-lookup"><span data-stu-id="f305c-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="f305c-135">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span><span class="sxs-lookup"><span data-stu-id="f305c-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="f305c-136">Compartilhar s de autenticação cookie sem ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f305c-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="f305c-137">Ao usar cookie s diretamente sem ASP.NET Core Identity , configure a proteção de dados e a autenticação no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f305c-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f305c-138">No exemplo a seguir, o tipo de autenticação é definido como `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="f305c-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="f305c-139">Compartilhar cookie s em caminhos de base diferentes</span><span class="sxs-lookup"><span data-stu-id="f305c-139">Share cookies across different base paths</span></span>

<span data-ttu-id="f305c-140">Uma autenticação cookie usa [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) como padrão [ Cookie . Caminho](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="f305c-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="f305c-141">Se o aplicativo cookie deve ser compartilhado entre caminhos base diferentes, `Path` deve ser substituído:</span><span class="sxs-lookup"><span data-stu-id="f305c-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="f305c-142">Compartilhar cookie s em subdomínios</span><span class="sxs-lookup"><span data-stu-id="f305c-142">Share cookies across subdomains</span></span>

<span data-ttu-id="f305c-143">Ao hospedar aplicativos que compartilham cookie s em subdomínios, especifique um domínio comum no [ Cookie . ](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)Propriedade de domínio.</span><span class="sxs-lookup"><span data-stu-id="f305c-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="f305c-144">Para compartilhar cookie s entre aplicativos em `contoso.com` , como `first_subdomain.contoso.com` e `second_subdomain.contoso.com` , especifique o `Cookie.Domain` como `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="f305c-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="f305c-145">Criptografar chaves de proteção de dados em repouso</span><span class="sxs-lookup"><span data-stu-id="f305c-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="f305c-146">Para implantações de produção, configure o `DataProtectionProvider` para criptografar chaves em repouso com DPAPI ou um X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="f305c-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="f305c-147">Para obter mais informações, consulte <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="f305c-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="f305c-148">No exemplo a seguir, uma impressão digital do certificado é fornecida para <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="f305c-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="f305c-149">Compartilhar os cookie s de autenticação entre os aplicativos ASP.NET 4. x e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f305c-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="f305c-150">Os aplicativos ASP.NET 4. x que usam Cookie o middleware de autenticação Katana podem ser configurados para gerar autenticação cookie s que são compatíveis com o Cookie middleware de autenticação ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f305c-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="f305c-151">Isso permite atualizar aplicativos individuais de um site grande em várias etapas, ao mesmo tempo em que fornece uma experiência de SSO tranqüila em todo o site.</span><span class="sxs-lookup"><span data-stu-id="f305c-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="f305c-152">Quando um aplicativo usa Cookie o middleware de autenticação Katana, ele chama `UseCookieAuthentication` o arquivo *Startup.auth.cs* do projeto.</span><span class="sxs-lookup"><span data-stu-id="f305c-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="f305c-153">Os projetos de aplicativo Web ASP.NET 4. x criados com Visual Studio 2013 e posterior usam o Cookie middleware de autenticação Katana por padrão.</span><span class="sxs-lookup"><span data-stu-id="f305c-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="f305c-154">Embora `UseCookieAuthentication` o seja obsoleto e não tenha suporte para aplicativos ASP.NET Core, chamar `UseCookieAuthentication` um aplicativo ASP.NET 4. x que usa o Cookie middleware de autenticação Katana é válido.</span><span class="sxs-lookup"><span data-stu-id="f305c-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="f305c-155">Um aplicativo ASP.NET 4. x deve ter como destino .NET Framework 4.5.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="f305c-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="f305c-156">Caso contrário, os pacotes NuGet necessários falharão na instalação.</span><span class="sxs-lookup"><span data-stu-id="f305c-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="f305c-157">Para compartilhar cookie os s de autenticação entre um aplicativo ASP.NET 4. x e um aplicativo ASP.NET Core, configure o aplicativo ASP.NET Core conforme indicado na seção [s de autenticação de compartilhamento cookie entre ASP.NET Core aplicativos](#share-authentication-cookies-with-aspnet-core-identity) e, em seguida, configure o aplicativo ASP.NET 4. x da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="f305c-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="f305c-158">Confirme se os pacotes do aplicativo foram atualizados para as versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="f305c-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="f305c-159">Instale o pacote [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) em cada aplicativo ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="f305c-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="f305c-160">Localize e modifique a chamada para `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="f305c-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="f305c-161">Altere o cookie nome para corresponder ao nome usado pelo middleware de Cookie autenticação ASP.NET Core ( `.AspNet.SharedCookie` no exemplo).</span><span class="sxs-lookup"><span data-stu-id="f305c-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="f305c-162">No exemplo a seguir, o tipo de autenticação é definido como `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="f305c-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="f305c-163">Forneça uma instância de um `DataProtectionProvider` inicializado para o local de armazenamento de chave de proteção de dados comum.</span><span class="sxs-lookup"><span data-stu-id="f305c-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="f305c-164">Confirme se o nome do aplicativo está definido como o nome do aplicativo comum usado por todos os aplicativos que compartilham os s de autenticação cookie ( `SharedCookieApp` no exemplo).</span><span class="sxs-lookup"><span data-stu-id="f305c-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="f305c-165">Se não estiver configurando `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , defina <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> como uma declaração que diferencie usuários exclusivos.</span><span class="sxs-lookup"><span data-stu-id="f305c-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="f305c-166">*App_Start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="f305c-166">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="f305c-167">Ao gerar uma identidade de usuário, o tipo de autenticação ( `Identity.Application` ) deve corresponder ao tipo definido em `AuthenticationType` conjunto com `UseCookieAuthentication` em *App_Start/Startup.auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="f305c-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="f305c-168">*Modelos/ Identity Models.cs*:</span><span class="sxs-lookup"><span data-stu-id="f305c-168">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="f305c-169">Usar um banco de dados de usuário comum</span><span class="sxs-lookup"><span data-stu-id="f305c-169">Use a common user database</span></span>

<span data-ttu-id="f305c-170">Quando os aplicativos usam o mesmo Identity esquema (mesma versão do Identity ), confirme se o Identity sistema de cada aplicativo está apontado para o mesmo banco de dados de usuário.</span><span class="sxs-lookup"><span data-stu-id="f305c-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="f305c-171">Caso contrário, o sistema de identidade produz falhas em tempo de execução quando tenta corresponder as informações na autenticação em cookie relação às informações em seu banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f305c-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="f305c-172">Quando o Identity esquema é diferente entre os aplicativos, geralmente porque os aplicativos estão usando Identity versões diferentes, compartilhar um banco de dados comum com base na versão mais recente do Identity não é possível sem remapear e adicionar colunas nos esquemas de outros aplicativos Identity .</span><span class="sxs-lookup"><span data-stu-id="f305c-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="f305c-173">Geralmente, é mais eficiente atualizar os outros aplicativos para usar a versão mais recente Identity para que um banco de dados comum possa ser compartilhado pelos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="f305c-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f305c-174">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f305c-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
