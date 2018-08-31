---
title: Manter declarações adicionais e os tokens de provedores externos no ASP.NET Core
author: guardrex
description: Saiba como estabelecer declarações adicionais e tokens de provedores externos.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/28/2018
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 6386dd5f0bd901be01cf56081a6b9ca7f408f9f9
ms.sourcegitcommit: a669c4e3f42e387e214a354ac4143555602e6f66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336164"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="a9513-103">Manter declarações adicionais e os tokens de provedores externos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9513-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="a9513-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a9513-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a9513-105">Um aplicativo ASP.NET Core pode estabelecer declarações adicionais e tokens de provedores de autenticação externa, como Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="a9513-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="a9513-106">Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados de usuário em declarações adicionais é o mesmo.</span><span class="sxs-lookup"><span data-stu-id="a9513-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="a9513-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9513-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="prerequisite"></a><span data-ttu-id="a9513-108">Pré-requisito</span><span class="sxs-lookup"><span data-stu-id="a9513-108">Prerequisite</span></span>

<span data-ttu-id="a9513-109">Decida quais provedores de autenticação externa para dar suporte a no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9513-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="a9513-110">Para cada provedor, registrar o aplicativo e obter o ID do cliente e segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="a9513-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="a9513-111">Para obter mais informações, consulte <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="a9513-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="a9513-112">O [aplicativo de exemplo](#sample-app-instructions) usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="a9513-112">The [sample app](#sample-app-instructions) uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="authentication-provider-configuration"></a><span data-ttu-id="a9513-113">Configuração do provedor de autenticação</span><span class="sxs-lookup"><span data-stu-id="a9513-113">Authentication provider configuration</span></span>

### <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="a9513-114">Defina a ID do cliente e segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="a9513-114">Set the client ID and client secret</span></span>

<span data-ttu-id="a9513-115">O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID de cliente e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="a9513-115">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="a9513-116">ID do cliente e valores de segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor.</span><span class="sxs-lookup"><span data-stu-id="a9513-116">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="a9513-117">Cada provedor externo que usa o aplicativo deve ser configurado independentemente com a ID do cliente e o segredo do cliente do provedor.</span><span class="sxs-lookup"><span data-stu-id="a9513-117">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="a9513-118">Para obter mais informações, consulte os tópicos de provedor de autenticação externa que se aplicam ao seu cenário:</span><span class="sxs-lookup"><span data-stu-id="a9513-118">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="a9513-119">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="a9513-119">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="a9513-120">Autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="a9513-120">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="a9513-121">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="a9513-121">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="a9513-122">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="a9513-122">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="a9513-123">Outros provedores de autenticação</span><span class="sxs-lookup"><span data-stu-id="a9513-123">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="a9513-124">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="a9513-124">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="a9513-125">O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e segredo do cliente fornecido pelo Google:</span><span class="sxs-lookup"><span data-stu-id="a9513-125">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,6)]

### <a name="establish-the-authentication-scope"></a><span data-ttu-id="a9513-126">Estabelecer o escopo de autenticação</span><span class="sxs-lookup"><span data-stu-id="a9513-126">Establish the authentication scope</span></span>

<span data-ttu-id="a9513-127">Especifique a lista de permissões para recuperar do provedor, especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="a9513-127">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="a9513-128">Escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="a9513-128">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="a9513-129">Provider</span><span class="sxs-lookup"><span data-stu-id="a9513-129">Provider</span></span>  | <span data-ttu-id="a9513-130">Escopo</span><span class="sxs-lookup"><span data-stu-id="a9513-130">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="a9513-131">Facebook</span><span class="sxs-lookup"><span data-stu-id="a9513-131">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="a9513-132">Google</span><span class="sxs-lookup"><span data-stu-id="a9513-132">Google</span></span>    | `https://www.googleapis.com/auth/plus.login`                     |
| <span data-ttu-id="a9513-133">Microsoft</span><span class="sxs-lookup"><span data-stu-id="a9513-133">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="a9513-134">Twitter</span><span class="sxs-lookup"><span data-stu-id="a9513-134">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="a9513-135">O aplicativo de exemplo adiciona o Google `plus.login` escopo para solicitar a entrada do Google + nas permissões:</span><span class="sxs-lookup"><span data-stu-id="a9513-135">The sample app adds the Google `plus.login` scope to request Google+ sign in permissions:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=7)]

### <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="a9513-136">Mapear chaves de dados de usuário e criar declarações</span><span class="sxs-lookup"><span data-stu-id="a9513-136">Map user data keys and create claims</span></span>

<span data-ttu-id="a9513-137">Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> para cada chave nos dados de usuário do provedor externo JSON para a identidade do aplicativo ler em entrar.</span><span class="sxs-lookup"><span data-stu-id="a9513-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> for each key in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="a9513-138">Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="a9513-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="a9513-139">O aplicativo de exemplo cria um <xref:System.Security.Claims.ClaimTypes.Gender> reivindicar do `gender` chave nos dados de usuário do Google:</span><span class="sxs-lookup"><span data-stu-id="a9513-139">The sample app creates a <xref:System.Security.Claims.ClaimTypes.Gender> claim from the `gender` key in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=8)]

<span data-ttu-id="a9513-140">Na <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, uma <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager`1.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="a9513-140">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager`1.SignInAsync*>.</span></span> <span data-ttu-id="a9513-141">Durante o logon no processo, o <xref:Microsoft.AspNetCore.Identity.UserManager`1> pode armazenar um `ApplicationUser` de declaração para os dados de usuário disponíveis no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="a9513-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager`1> can store an `ApplicationUser` claim for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="a9513-142">No aplicativo de exemplo, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) estabelece uma <xref:System.Security.Claims.ClaimTypes.Gender> de declaração para assinado no `ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="a9513-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes a <xref:System.Security.Claims.ClaimTypes.Gender> claim for the signed in `ApplicationUser`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=30-31)]

### <a name="save-the-access-token"></a><span data-ttu-id="a9513-143">Salve o token de acesso</span><span class="sxs-lookup"><span data-stu-id="a9513-143">Save the access token</span></span>

<span data-ttu-id="a9513-144"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Define se os tokens de acesso e de atualização devem ser armazenadas do <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="a9513-144"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="a9513-145">`SaveTokens` é definido como `false` por padrão para reduzir o tamanho do cookie de autenticação final.</span><span class="sxs-lookup"><span data-stu-id="a9513-145">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="a9513-146">O aplicativo de exemplo define o valor de `SaveTokens` à `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="a9513-146">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=9)]

<span data-ttu-id="a9513-147">Quando `OnPostConfirmationAsync` é executado, armazenar o token de acesso ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo no `ApplicationUser`do `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="a9513-147">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="a9513-148">O aplicativo de exemplo salva o token de acesso em:</span><span class="sxs-lookup"><span data-stu-id="a9513-148">The sample app saves the access token in:</span></span>

* <span data-ttu-id="a9513-149">`OnPostConfirmationAsync` &ndash; É executado para o novo registro do usuário.</span><span class="sxs-lookup"><span data-stu-id="a9513-149">`OnPostConfirmationAsync` &ndash; Executes for new user registration.</span></span>
* <span data-ttu-id="a9513-150">`OnGetCallbackAsync` &ndash; Executa quando um usuário registrado anteriormente entra no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9513-150">`OnGetCallbackAsync` &ndash; Executes when a previously registered user signs into the app.</span></span>

<span data-ttu-id="a9513-151">*Account/ExternalLogin.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a9513-151">*Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=34-35)]

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnGetCallbackAsync&highlight=31-32)]

### <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="a9513-152">Como adicionar tokens personalizados adicionais</span><span class="sxs-lookup"><span data-stu-id="a9513-152">How to add additional custom tokens</span></span>

<span data-ttu-id="a9513-153">Para demonstrar como adicionar um token personalizado, que é armazenado como parte da `SaveTokens`, o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="a9513-153">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=10-21)]

## <a name="sample-app-instructions"></a><span data-ttu-id="a9513-154">Instruções do aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="a9513-154">Sample app instructions</span></span>

<span data-ttu-id="a9513-155">O aplicativo de exemplo demonstra como:</span><span class="sxs-lookup"><span data-stu-id="a9513-155">The sample app demonstrates how to:</span></span>

* <span data-ttu-id="a9513-156">Obtenha o sexo do usuário do Google e armazene uma declaração de sexo com o valor.</span><span class="sxs-lookup"><span data-stu-id="a9513-156">Obtain the user's gender from Google and store a gender claim with the value.</span></span>
* <span data-ttu-id="a9513-157">Store o token de acesso do Google, o usuário `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="a9513-157">Store the Google access token in the user's `AuthenticationProperties`.</span></span>

<span data-ttu-id="a9513-158">Para usar o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="a9513-158">To use the sample app:</span></span>

1. <span data-ttu-id="a9513-159">Registrar o aplicativo e obter uma ID de cliente válido e o segredo do cliente para autenticação do Google.</span><span class="sxs-lookup"><span data-stu-id="a9513-159">Register the app and obtain a valid client ID and client secret for Google authentication.</span></span> <span data-ttu-id="a9513-160">Para obter mais informações, consulte <xref:security/authentication/google-logins>.</span><span class="sxs-lookup"><span data-stu-id="a9513-160">For more information, see <xref:security/authentication/google-logins>.</span></span>
1. <span data-ttu-id="a9513-161">Forneça a ID de cliente e o segredo do cliente para o aplicativo na <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> de `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a9513-161">Provide the client ID and client secret to the app in the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> of `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="a9513-162">Execute o aplicativo e solicite a página Minhas declarações.</span><span class="sxs-lookup"><span data-stu-id="a9513-162">Run the app and request the My Claims page.</span></span> <span data-ttu-id="a9513-163">Quando o usuário não estiver conectado, o aplicativo redireciona para o Google.</span><span class="sxs-lookup"><span data-stu-id="a9513-163">When the user isn't signed in, the app redirects to Google.</span></span> <span data-ttu-id="a9513-164">Entrar com o Google.</span><span class="sxs-lookup"><span data-stu-id="a9513-164">Sign in with Google.</span></span> <span data-ttu-id="a9513-165">Google redireciona o usuário retorne ao aplicativo (`/Home/MyClaims`).</span><span class="sxs-lookup"><span data-stu-id="a9513-165">Google redirects the user back to the app (`/Home/MyClaims`).</span></span> <span data-ttu-id="a9513-166">O usuário é autenticado e a página Minhas declarações é carregada.</span><span class="sxs-lookup"><span data-stu-id="a9513-166">The user is authenticated, and the My Claims page is loaded.</span></span> <span data-ttu-id="a9513-167">A declaração de gênero esteja presente em **declarações de usuário** com o valor obtido do Google.</span><span class="sxs-lookup"><span data-stu-id="a9513-167">The gender claim is present under **User Claims** with the value obtained from Google.</span></span> <span data-ttu-id="a9513-168">O token de acesso será exibido na **as propriedades de autenticação**.</span><span class="sxs-lookup"><span data-stu-id="a9513-168">The access token appears in the **Authentication Properties**.</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    b36a7b09-9135-4810-b7a5-78697ff23e99
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    username@gmail.com
AspNet.Identity.SecurityStamp
    29G2TB881ATCUQFJSRFG1S0QJ0OOAWVT
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/gender
    female
http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod
    Google

Authentication Properties

.Token.access_token
    bv42.Dgw...GQMv9ArLPs
.Token.token_type
    Bearer
.Token.expires_at
    2018-08-27T19:08:00.0000000+00:00
.Token.TicketCreated
    8/27/2018 6:08:00 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.issued
    Mon, 27 Aug 2018 18:08:05 GMT
.expires
    Mon, 10 Sep 2018 18:08:05 GMT
```
