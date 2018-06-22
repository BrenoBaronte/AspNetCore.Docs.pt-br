---
title: Autenticar usuários com o WS-Federation no núcleo do ASP.NET
author: chlowell
description: Este tutorial demonstra como usar o WS-Federation em um aplicativo do ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/27/2018
uid: security/authentication/ws-federation
ms.openlocfilehash: 55504ed28cf8ef1095bf16c101c09a6f374f038c
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36277433"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="ff539-103">Autenticar usuários com o WS-Federation no núcleo do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ff539-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="ff539-104">Este tutorial demonstra como habilitar usuários entrar com um provedor de autenticação do WS-Federation como o Active Directory Federation Services (ADFS) ou [Active Directory do Azure](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="ff539-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="ff539-105">Ele usa o aplicativo de exemplo do ASP.NET Core 2.0 descrito em [Facebook, Google e a autenticação do provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ff539-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ff539-106">Para aplicativos do ASP.NET Core 2.0, o suporte do WS-Federation é fornecido por [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="ff539-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="ff539-107">Esse componente é movido de [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) e compartilha muitas das mecânica do componente.</span><span class="sxs-lookup"><span data-stu-id="ff539-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="ff539-108">No entanto, os componentes são diferentes de duas maneiras importantes.</span><span class="sxs-lookup"><span data-stu-id="ff539-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="ff539-109">Por padrão, o middleware novo:</span><span class="sxs-lookup"><span data-stu-id="ff539-109">By default, the new middleware:</span></span>

* <span data-ttu-id="ff539-110">Não permitir logons não solicitados.</span><span class="sxs-lookup"><span data-stu-id="ff539-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="ff539-111">Esse recurso do protocolo WS-Federation é vulnerável a ataques XSRF.</span><span class="sxs-lookup"><span data-stu-id="ff539-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="ff539-112">No entanto, ela pode ser habilitada com o `AllowUnsolicitedLogins` opção.</span><span class="sxs-lookup"><span data-stu-id="ff539-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="ff539-113">Não verifica cada postagem de formulário para mensagens de entrada.</span><span class="sxs-lookup"><span data-stu-id="ff539-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="ff539-114">Somente solicitações para o `CallbackPath` são verificados quanto à entrada-ins `CallbackPath` assume como padrão `/signin-wsfed` , mas pode ser alterado por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade do [ WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) classe.</span><span class="sxs-lookup"><span data-stu-id="ff539-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="ff539-115">Esse caminho pode ser compartilhado com outros provedores de autenticação, permitindo que o [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) opção.</span><span class="sxs-lookup"><span data-stu-id="ff539-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="ff539-116">Registrar o aplicativo com o Active Directory</span><span class="sxs-lookup"><span data-stu-id="ff539-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="ff539-117">Serviços de Federação do Active Directory</span><span class="sxs-lookup"><span data-stu-id="ff539-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="ff539-118">Abra o servidor **terceira parte confiável Assistente para adicionar confiança** no console de gerenciamento do AD FS:</span><span class="sxs-lookup"><span data-stu-id="ff539-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: Bem-vindo](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="ff539-120">Escolha esta opção para inserir os dados manualmente:</span><span class="sxs-lookup"><span data-stu-id="ff539-120">Choose to enter data manually:</span></span>

![Adicionar Assistente de terceira parte confiável: Selecionar fonte de dados](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="ff539-122">Insira um nome para exibição para a terceira parte confiável.</span><span class="sxs-lookup"><span data-stu-id="ff539-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="ff539-123">O nome não é importante para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff539-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="ff539-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) não tem suporte para criptografia de token, portanto, não configurar um certificado de criptografia do token:</span><span class="sxs-lookup"><span data-stu-id="ff539-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Adicionar Assistente de terceira parte confiável: Configurar o certificado](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="ff539-126">Habilite o suporte para o protocolo WS-Federation Passive, usando a URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff539-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="ff539-127">Verifique se que a porta está correta para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ff539-127">Verify the port is correct for the app:</span></span>

![Adicionar Assistente de terceira parte confiável: Configurar a URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="ff539-129">Isso deve ser uma URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ff539-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="ff539-130">O IIS Express pode fornecer um certificado autoassinado ao hospedar o aplicativo durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ff539-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="ff539-131">Kestrel requer configuração manual do certificado.</span><span class="sxs-lookup"><span data-stu-id="ff539-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="ff539-132">Consulte o [documentação Kestrel](xref:fundamentals/servers/kestrel) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="ff539-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="ff539-133">Clique em **próximo** através do restante do assistente e **fechar** no final.</span><span class="sxs-lookup"><span data-stu-id="ff539-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="ff539-134">A identidade do ASP.NET Core requer um **ID de nome** de declaração.</span><span class="sxs-lookup"><span data-stu-id="ff539-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="ff539-135">Adicione um do **editar regras de declaração** caixa de diálogo:</span><span class="sxs-lookup"><span data-stu-id="ff539-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Editar regras de declaração](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="ff539-137">No **Adicionar Assistente de regra de declaração de transformação**, deixe o padrão **enviar atributos LDAP como declarações** modelo selecionado e clique em **próximo**.</span><span class="sxs-lookup"><span data-stu-id="ff539-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="ff539-138">Adicionar um mapeamento de regra a **nome de conta SAM** atributo LDAP para o **ID de nome** declaração de saída:</span><span class="sxs-lookup"><span data-stu-id="ff539-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Adicionar Assistente de regra de declaração de transformação: Configurar a regra de declaração](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="ff539-140">Clique em **concluir** > **Okey** no **editar regras de declaração** janela.</span><span class="sxs-lookup"><span data-stu-id="ff539-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="ff539-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="ff539-141">Azure Active Directory</span></span>

* <span data-ttu-id="ff539-142">Navegue até a folha de registros de aplicativo do locatário do AAD.</span><span class="sxs-lookup"><span data-stu-id="ff539-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="ff539-143">Clique em **novo registro de aplicativo**:</span><span class="sxs-lookup"><span data-stu-id="ff539-143">Click **New application registration**:</span></span>

![Active Directory do Azure: Registros de aplicativo](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="ff539-145">Insira um nome para o registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff539-145">Enter a name for the app registration.</span></span> <span data-ttu-id="ff539-146">Isso não é importante para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff539-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="ff539-147">Insira a URL do aplicativo escuta em que o **URL de logon**:</span><span class="sxs-lookup"><span data-stu-id="ff539-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Active Directory do Azure: Crie um registro de aplicativo](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="ff539-149">Clique em **pontos de extremidade** e observe o **documento de metadados de Federação** URL.</span><span class="sxs-lookup"><span data-stu-id="ff539-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="ff539-150">Este é o middleware de WS-Federation `MetadataAddress`:</span><span class="sxs-lookup"><span data-stu-id="ff539-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Active Directory do Azure: os pontos de extremidade](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="ff539-152">Navegue até o novo registro de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff539-152">Navigate to the new app registration.</span></span> <span data-ttu-id="ff539-153">Clique em **configurações** > **propriedades** e anote o **URI da ID do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="ff539-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="ff539-154">Este é o middleware de WS-Federation `Wtrealm`:</span><span class="sxs-lookup"><span data-stu-id="ff539-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Active Directory do Azure: Propriedades de registro de aplicativo](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="ff539-156">Adicionar o WS-Federation como um provedor de logon externo para a identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff539-156">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="ff539-157">Adicione uma dependência no [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="ff539-157">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="ff539-158">Adicionar o WS-Federation para o `Configure` método *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff539-158">Add WS-Federation to the `Configure` method in *Startup.cs*:</span></span>

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddAuthentication()
        .AddWsFederation(options =>
        {
            // MetadataAddress represents the Active Directory instance used to authenticate users.
            options.MetadataAddress = "https://<ADFS FQDN or AAD tenant>/FederationMetadata/2007-06/FederationMetadata.xml";

            // Wtrealm is the app's identifier in the Active Directory instance.
            // For ADFS, use the relying party's identifier, its WS-Federation Passive protocol URL:
            options.Wtrealm = "https://localhost:44307/";

            // For AAD, use the App ID URI from the app registration's Properties blade:
            options.Wtrealm = "https://wsfedsample.onmicrosoft.com/bf0e7e6d-056e-4e37-b9a6-2c36797b9f01";
        });

    services.AddMvc()
     // ...
    ```

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="ff539-159">Faça logon com o WS-Federation</span><span class="sxs-lookup"><span data-stu-id="ff539-159">Log in with WS-Federation</span></span>

<span data-ttu-id="ff539-160">Navegue até o aplicativo e clique no **login** link no cabeçalho de navegação.</span><span class="sxs-lookup"><span data-stu-id="ff539-160">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="ff539-161">Há uma opção para fazer logon com WsFederation: ![página de logon](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="ff539-161">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="ff539-162">Com o ADFS como o provedor, o botão redireciona para uma página de entrada do AD FS: ![página de entrada do AD FS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="ff539-162">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="ff539-163">Com o Azure Active Directory como o provedor, o botão redireciona para uma página de entrada do AAD: ![página de entrada do AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="ff539-163">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="ff539-164">Um bem-sucedida entrar para um novo usuário redireciona para a página de registro de usuário do aplicativo: ![página de registro](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="ff539-164">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="ff539-165">Use o WS-Federation sem identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff539-165">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="ff539-166">O middleware de WS-Federation pode ser usado sem identidade.</span><span class="sxs-lookup"><span data-stu-id="ff539-166">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="ff539-167">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff539-167">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = WsFederationDefaults.AuthenticationScheme;
    })
    .AddWsFederation(options =>
    {
        options.Wtrealm = Configuration["wsfed:realm"];
        options.MetadataAddress = Configuration["wsfed:metadata"];
    })
    .AddCookie();
}

public void Configure(IApplicationBuilder app)
{
    app.UseAuthentication();
        // …
}
```
