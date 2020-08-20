---
title: Configuração de entrada externa do Twitter com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário da conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: da0c5579b0828aee7f1c78ec7f5731db50151e90
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634053"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="1b995-103">Configuração de entrada externa do Twitter com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b995-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="1b995-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1b995-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1b995-105">Este exemplo mostra como permitir que os usuários [entrem com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um exemplo ASP.NET Core projeto 3,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1b995-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="1b995-106">Criar o aplicativo no Twitter</span><span class="sxs-lookup"><span data-stu-id="1b995-106">Create the app in Twitter</span></span>

* <span data-ttu-id="1b995-107">Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="1b995-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="1b995-108">Navegue até [https://apps.twitter.com/](https://apps.twitter.com/) e entre.</span><span class="sxs-lookup"><span data-stu-id="1b995-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="1b995-109">Se você ainda não tiver uma conta do Twitter, use o link **[inscrever-se agora](https://twitter.com/signup)** para criar uma.</span><span class="sxs-lookup"><span data-stu-id="1b995-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="1b995-110">Selecione **criar um aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="1b995-110">Select **Create an app**.</span></span> <span data-ttu-id="1b995-111">Preencha o **nome do aplicativo**, a **Descrição do aplicativo** e o URI do **site** público (isso pode ser temporário até que você registre o nome de domínio):</span><span class="sxs-lookup"><span data-stu-id="1b995-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="1b995-112">Marque a caixa ao lado de **Habilitar entrada com o Twitter**</span><span class="sxs-lookup"><span data-stu-id="1b995-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="1b995-113">Microsoft. AspNetCore.Identity</span><span class="sxs-lookup"><span data-stu-id="1b995-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="1b995-114">exige que os usuários tenham um endereço de email por padrão.</span><span class="sxs-lookup"><span data-stu-id="1b995-114">requires users to have an email address by default.</span></span> <span data-ttu-id="1b995-115">Vá para a guia **permissões** , clique no botão **Editar** e marque a caixa ao lado de **solicitar endereço de email dos usuários**.</span><span class="sxs-lookup"><span data-stu-id="1b995-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="1b995-116">Insira seu URI de desenvolvimento com `/signin-twitter` anexado no campo **URLs de retorno de chamada** (por exemplo: `https://webapp128.azurewebsites.net/signin-twitter` ).</span><span class="sxs-lookup"><span data-stu-id="1b995-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="1b995-117">O esquema de autenticação do Twitter configurado mais adiante neste exemplo tratará automaticamente as solicitações na `/signin-twitter` rota para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="1b995-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="1b995-118">O segmento URI `/signin-twitter` é definido como o retorno de chamada padrão do provedor de autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="1b995-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="1b995-119">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Twitter por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="1b995-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="1b995-120">Preencha o restante do formulário e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="1b995-120">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="1b995-121">Novos detalhes do aplicativo são exibidos:</span><span class="sxs-lookup"><span data-stu-id="1b995-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="1b995-122">Armazenar a chave e o segredo da API do consumidor do Twitter</span><span class="sxs-lookup"><span data-stu-id="1b995-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="1b995-123">Armazene configurações confidenciais, como a chave de API do consumidor do Twitter e o segredo com o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="1b995-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="1b995-124">Para este exemplo, use as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="1b995-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="1b995-125">Inicialize o projeto para o armazenamento secreto de acordo com as instruções em [habilitar armazenamento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="1b995-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="1b995-126">Armazene as configurações confidenciais no repositório de segredo local com as chaves de segredos `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` :</span><span class="sxs-lookup"><span data-stu-id="1b995-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="1b995-127">Esses tokens podem ser encontrados na guia **chaves e tokens de acesso** após a criação de um novo aplicativo do Twitter:</span><span class="sxs-lookup"><span data-stu-id="1b995-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="1b995-128">Configurar a autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="1b995-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="1b995-129">Adicione o serviço do Twitter no `ConfigureServices` método no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="1b995-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="1b995-130">Consulte a referência de API do [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obter mais informações sobre as opções de configuração com suporte pela autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="1b995-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="1b995-131">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="1b995-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="1b995-132">Entrar com o Twitter</span><span class="sxs-lookup"><span data-stu-id="1b995-132">Sign in with Twitter</span></span>

<span data-ttu-id="1b995-133">Execute o aplicativo e selecione **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="1b995-133">Run the app and select **Log in**.</span></span> <span data-ttu-id="1b995-134">É exibida uma opção para entrar com o Twitter:</span><span class="sxs-lookup"><span data-stu-id="1b995-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="1b995-135">Clicar no **Twitter** redireciona para o Twitter para autenticação:</span><span class="sxs-lookup"><span data-stu-id="1b995-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="1b995-136">Depois de inserir suas credenciais do Twitter, você será Redirecionado de volta para o site da Web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="1b995-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="1b995-137">Agora você está conectado usando suas credenciais do Twitter:</span><span class="sxs-lookup"><span data-stu-id="1b995-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="1b995-138">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="1b995-138">Troubleshooting</span></span>

* <span data-ttu-id="1b995-139">**Somente ASP.NET Core 2. x:** Se Identity não estiver configurado chamando `services.AddIdentity` em `ConfigureServices` , a tentativa de autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="1b995-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="1b995-140">O modelo de projeto usado neste exemplo garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="1b995-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="1b995-141">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* .</span><span class="sxs-lookup"><span data-stu-id="1b995-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="1b995-142">Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="1b995-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1b995-143">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="1b995-143">Next steps</span></span>

* <span data-ttu-id="1b995-144">Este artigo mostrou como você pode autenticar com o Twitter.</span><span class="sxs-lookup"><span data-stu-id="1b995-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="1b995-145">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1b995-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="1b995-146">Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.</span><span class="sxs-lookup"><span data-stu-id="1b995-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="1b995-147">Defina `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="1b995-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="1b995-148">O sistema de configuração é configurado para ler chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1b995-148">The configuration system is set up to read keys from environment variables.</span></span>
