---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com a biblioteca de autenticação
author: guardrex
description: Saiba como proteger um aplicativo ASP.NET Core Blazor WebAssembly autônomo com a biblioteca de autenticação.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 3da9ea045de996602ead052f6f13ffc999273a50
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252481"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="99ebb-103">Proteger um Blazor WebAssembly aplicativo ASP.NET Core autônomo com a biblioteca de autenticação</span><span class="sxs-lookup"><span data-stu-id="99ebb-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="99ebb-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="99ebb-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="99ebb-105">*Para Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), não siga as orientações neste tópico. Consulte os tópicos do AAD e do AAD B2C no nó Sumário.*</span><span class="sxs-lookup"><span data-stu-id="99ebb-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="99ebb-106">Para criar um [ Blazor WebAssembly aplicativo autônomo](xref:blazor/hosting-models#blazor-webassembly) que usa [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) a biblioteca do, siga as orientações para sua escolha de ferramentas.</span><span class="sxs-lookup"><span data-stu-id="99ebb-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

> [!NOTE]
> <span data-ttu-id="99ebb-107">O Identity provedor (IP) deve usar o [OpenID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="99ebb-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="99ebb-108">Por exemplo, o IP do Facebook não é um provedor compatível com OIDC, portanto, as diretrizes neste tópico não funcionam com o IP do Facebook.</span><span class="sxs-lookup"><span data-stu-id="99ebb-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="99ebb-109">Para obter mais informações, consulte <xref:blazor/security/webassembly/index#authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="99ebb-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99ebb-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99ebb-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99ebb-111">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="99ebb-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="99ebb-112">Depois de escolher o modelo de **Blazor WebAssembly aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="99ebb-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="99ebb-113">Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="99ebb-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="99ebb-114">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="99ebb-114">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="99ebb-115">Crie um novo Blazor WebAssembly projeto com um mecanismo de autenticação em uma pasta vazia.</span><span class="sxs-lookup"><span data-stu-id="99ebb-115">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="99ebb-116">Especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [Identity](xref:security/authentication/identity) sistema ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="99ebb-116">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="99ebb-117">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="99ebb-117">Placeholder</span></span>  | <span data-ttu-id="99ebb-118">Exemplo</span><span class="sxs-lookup"><span data-stu-id="99ebb-118">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="99ebb-119">A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="99ebb-119">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="99ebb-120">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="99ebb-120">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99ebb-121">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="99ebb-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="99ebb-122">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="99ebb-122">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="99ebb-123">Na etapa **configurar seu novo Blazor WebAssembly aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="99ebb-123">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="99ebb-124">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="99ebb-124">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="99ebb-125">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="99ebb-125">Authentication package</span></span>

<span data-ttu-id="99ebb-126">Quando um aplicativo é criado para usar contas de usuário individuais, o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="99ebb-126">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="99ebb-127">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="99ebb-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="99ebb-128">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="99ebb-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="99ebb-129">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="99ebb-129">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="99ebb-130">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="99ebb-130">Authentication service support</span></span>

<span data-ttu-id="99ebb-131">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método de extensão fornecido pelo [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote.</span><span class="sxs-lookup"><span data-stu-id="99ebb-131">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="99ebb-132">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="99ebb-132">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="99ebb-133">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="99ebb-133">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="99ebb-134">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="99ebb-134">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="99ebb-135">Exemplo de OIDC do Google OAuth 2,0:</span><span class="sxs-lookup"><span data-stu-id="99ebb-135">Google OAuth 2.0 OIDC example:</span></span>

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

<span data-ttu-id="99ebb-136">O URI de redirecionamento ( `https://localhost:5001/authentication/login-callback` ) é registrado no [console de APIs do Google](https://console.developers.google.com/apis/dashboard) em **credenciais**  >  **`{NAME}`**  >  **URIs de redirecionamento autorizado**, em que `{NAME}` é o nome do cliente do aplicativo na lista de aplicativos **IDs de cliente do OAuth 2,0** do console do Google APIs.</span><span class="sxs-lookup"><span data-stu-id="99ebb-136">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="99ebb-137">O suporte de autenticação para aplicativos autônomos é oferecido usando o OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="99ebb-137">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="99ebb-138">O <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando o OIDC.</span><span class="sxs-lookup"><span data-stu-id="99ebb-138">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="99ebb-139">Os valores necessários para configurar o aplicativo podem ser obtidos do IP em conformidade com o OIDC.</span><span class="sxs-lookup"><span data-stu-id="99ebb-139">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="99ebb-140">Obtenha os valores ao registrar o aplicativo, que normalmente ocorre em seu portal online.</span><span class="sxs-lookup"><span data-stu-id="99ebb-140">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="99ebb-141">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="99ebb-141">Access token scopes</span></span>

<span data-ttu-id="99ebb-142">O Blazor WebAssembly modelo configura automaticamente os escopos padrão para `openid` e `profile` .</span><span class="sxs-lookup"><span data-stu-id="99ebb-142">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="99ebb-143">O Blazor WebAssembly modelo não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="99ebb-143">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="99ebb-144">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token padrão do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="99ebb-144">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="99ebb-145">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="99ebb-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="99ebb-146">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="99ebb-146">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="99ebb-147">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="99ebb-147">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="99ebb-148">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="99ebb-148">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="99ebb-149">Página de índice</span><span class="sxs-lookup"><span data-stu-id="99ebb-149">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="99ebb-150">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="99ebb-150">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="99ebb-151">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="99ebb-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="99ebb-152">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="99ebb-152">LoginDisplay component</span></span>

<span data-ttu-id="99ebb-153">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="99ebb-153">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="99ebb-154">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="99ebb-154">For authenticated users:</span></span>
  * <span data-ttu-id="99ebb-155">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="99ebb-155">Displays the current username.</span></span>
  * <span data-ttu-id="99ebb-156">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="99ebb-156">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="99ebb-157">Para usuários anônimos, o oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="99ebb-157">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="99ebb-158">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="99ebb-158">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="99ebb-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="99ebb-159">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="99ebb-160">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="99ebb-160">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="99ebb-161"><xref:host-and-deploy/proxy-load-balancer>: Inclui diretrizes sobre:</span><span class="sxs-lookup"><span data-stu-id="99ebb-161"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="99ebb-162">Usando o middleware de cabeçalhos encaminhados para preservar as informações do esquema HTTPS entre servidores proxy e redes internas.</span><span class="sxs-lookup"><span data-stu-id="99ebb-162">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="99ebb-163">Cenários adicionais e casos de uso, incluindo configuração de esquema manual, alterações de caminho de solicitação para roteamento de solicitação correto e encaminhamento do esquema de solicitação para proxies inversos do Linux e não do IIS.</span><span class="sxs-lookup"><span data-stu-id="99ebb-163">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
