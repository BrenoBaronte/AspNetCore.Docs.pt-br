---
title: Configuração de logon externo do Google no núcleo do ASP.NET
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Google em um aplicativo existente do ASP.NET Core.
manager: wpickett
ms.author: riande
ms.date: 08/02/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/google-logins
ms.openlocfilehash: ccb771dbefefb007aede1bdf05ab50ec363a3089
ms.sourcegitcommit: 545ff5a632e2281035c1becec1f99137298e4f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2018
ms.locfileid: "34689029"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="05257-103">Configuração de logon externo do Google no núcleo do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="05257-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="05257-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="05257-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="05257-105">Este tutorial mostra como habilitar os usuários entrar com sua conta Google + usando um projeto do ASP.NET Core 2.0 de exemplo criado no [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="05257-105">This tutorial shows you how to enable your users to sign in with their Google+ account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="05257-106">Vamos começar seguindo o [oficiais etapas](https://developers.google.com/identity/sign-in/web/devconsole-project) para criar um novo aplicativo no Console de API do Google.</span><span class="sxs-lookup"><span data-stu-id="05257-106">We start by following the [official steps](https://developers.google.com/identity/sign-in/web/devconsole-project) to create a new app in Google API Console.</span></span>

## <a name="create-the-app-in-google-api-console"></a><span data-ttu-id="05257-107">Criar o aplicativo no Console de API do Google</span><span class="sxs-lookup"><span data-stu-id="05257-107">Create the app in Google API Console</span></span>

* <span data-ttu-id="05257-108">Navegue até [ https://console.developers.google.com/projectselector/apis/library ](https://console.developers.google.com/projectselector/apis/library) e entrar.</span><span class="sxs-lookup"><span data-stu-id="05257-108">Navigate to [https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library) and sign in.</span></span> <span data-ttu-id="05257-109">Se você ainda não tiver uma conta do Google, use **mais opções** > **[criar conta](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)**  link para criar um:</span><span class="sxs-lookup"><span data-stu-id="05257-109">If you don't already have a Google account, use **More options** > **[Create account](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)** link to create one:</span></span>

![Console de API do Google](index/_static/GoogleConsoleLogin.png)

* <span data-ttu-id="05257-111">Você será redirecionado para **biblioteca API Manager** página:</span><span class="sxs-lookup"><span data-stu-id="05257-111">You are redirected to **API Manager Library** page:</span></span>

![Página Gerenciador de API de biblioteca](index/_static/GoogleConsoleSwitchboard.png)

* <span data-ttu-id="05257-113">Toque em **criar** e insira seu **nome do projeto**:</span><span class="sxs-lookup"><span data-stu-id="05257-113">Tap **Create** and enter your **Project name**:</span></span>

![Caixa de diálogo Novo Projeto](index/_static/GoogleConsoleNewProj.png)

* <span data-ttu-id="05257-115">Após aceitar a caixa de diálogo, você será redirecionado para a página de biblioteca, permitindo que você escolha os recursos para seu novo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="05257-115">After accepting the dialog, you are redirected back to the Library page allowing you to choose features for your new app.</span></span> <span data-ttu-id="05257-116">Localizar **API do Google +** na lista e clique em seu link para adicionar o recurso de API:</span><span class="sxs-lookup"><span data-stu-id="05257-116">Find **Google+ API** in the list and click on its link to add the API feature:</span></span>

![Página Gerenciador de API de biblioteca](index/_static/GoogleConsoleChooseApi.png)

* <span data-ttu-id="05257-118">A página para a API recém-adicionado é exibida.</span><span class="sxs-lookup"><span data-stu-id="05257-118">The page for the newly added API is displayed.</span></span> <span data-ttu-id="05257-119">Toque em **habilitar** para adicionar o Google + sinal de recurso ao seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="05257-119">Tap **Enable** to add Google+ sign in feature to your app:</span></span>

![Página Gerenciador de API do Google + API](index/_static/GoogleConsoleEnableApi.png)

* <span data-ttu-id="05257-121">Depois de habilitar a API, toque em **criar credenciais** para configurar os segredos:</span><span class="sxs-lookup"><span data-stu-id="05257-121">After enabling the API, tap **Create credentials** to configure the secrets:</span></span>

![Página Gerenciador de API do Google + API](index/_static/GoogleConsoleGoCredentials.png)

* <span data-ttu-id="05257-123">Escolha:</span><span class="sxs-lookup"><span data-stu-id="05257-123">Choose:</span></span>
   * <span data-ttu-id="05257-124">**API do Google +**</span><span class="sxs-lookup"><span data-stu-id="05257-124">**Google+ API**</span></span>
   * <span data-ttu-id="05257-125">**Servidor Web (por exemplo, node.js, Tomcat)**, e</span><span class="sxs-lookup"><span data-stu-id="05257-125">**Web server (e.g. node.js, Tomcat)**, and</span></span>
   * <span data-ttu-id="05257-126">**Dados de usuário**:</span><span class="sxs-lookup"><span data-stu-id="05257-126">**User data**:</span></span>

![Página de credenciais de Gerenciador de API: descobrir quais tipos de credenciais que você precisa de painel](index/_static/GoogleConsoleChooseCred.png)

* <span data-ttu-id="05257-128">Toque em **as credenciais que eu preciso?** que leva você para a segunda etapa de configuração de aplicativo, **criar uma ID de cliente OAuth 2.0**:</span><span class="sxs-lookup"><span data-stu-id="05257-128">Tap **What credentials do I need?** which takes you to the second step of app configuration, **Create an OAuth 2.0 client ID**:</span></span>

![Página de credenciais de Gerenciador de API: Crie uma ID de cliente OAuth 2.0](index/_static/GoogleConsoleCreateClient.png)

* <span data-ttu-id="05257-130">Porque estamos criando um projeto do Google + com apenas um recurso (entrada), podemos inserir a mesma **nome** para a ID do cliente OAuth 2.0 é usado para o projeto.</span><span class="sxs-lookup"><span data-stu-id="05257-130">Because we are creating a Google+ project with just one feature (sign in), we can enter the same **Name** for the OAuth 2.0 client ID as the one we used for the project.</span></span>

* <span data-ttu-id="05257-131">Insira o URI de desenvolvimento com */signin-google* acrescentados no **autorizados redirecionar URIs** campo (por exemplo: `https://localhost:44320/signin-google`).</span><span class="sxs-lookup"><span data-stu-id="05257-131">Enter your development URI with */signin-google* appended into the **Authorized redirect URIs** field (for example: `https://localhost:44320/signin-google`).</span></span> <span data-ttu-id="05257-132">A autenticação do Google configurada mais tarde neste tutorial automaticamente manipulará as solicitações no */signin-google* rota para implementar o fluxo do OAuth.</span><span class="sxs-lookup"><span data-stu-id="05257-132">The Google authentication configured later in this tutorial will automatically handle requests at */signin-google* route to implement the OAuth flow.</span></span>

* <span data-ttu-id="05257-133">Pressione TAB para adicionar o **autorizados redirecionar URIs** entrada.</span><span class="sxs-lookup"><span data-stu-id="05257-133">Press TAB to add the **Authorized redirect URIs** entry.</span></span>

* <span data-ttu-id="05257-134">Toque em **criar ID do cliente**, que leva você para a terceira etapa, **configurar a tela de consentimento de OAuth 2.0**:</span><span class="sxs-lookup"><span data-stu-id="05257-134">Tap **Create client ID**, which takes you to the third step, **Set up the OAuth 2.0 consent screen**:</span></span>

![Página de credenciais de Gerenciador de API: configurar a tela de consentimento de OAuth 2.0](index/_static/GoogleConsoleAddCred.png)

* <span data-ttu-id="05257-136">Insira seu voltado ao público **endereço de Email** e **nome do produto** mostrado para seu aplicativo quando Google + solicita ao usuário para entrar.</span><span class="sxs-lookup"><span data-stu-id="05257-136">Enter your public facing **Email address** and the **Product name** shown for your app when Google+ prompts the user to sign in.</span></span> <span data-ttu-id="05257-137">Opções adicionais estão disponíveis em **mais opções de personalização**.</span><span class="sxs-lookup"><span data-stu-id="05257-137">Additional options are available under **More customization options**.</span></span>

* <span data-ttu-id="05257-138">Toque em **continuar** para prosseguir para a última etapa, **baixar credenciais**:</span><span class="sxs-lookup"><span data-stu-id="05257-138">Tap **Continue** to proceed to the last step, **Download credentials**:</span></span>

![Página de credenciais de Gerenciador de API: baixar credenciais](index/_static/GoogleConsoleFinish.png)

* <span data-ttu-id="05257-140">Toque em **baixar** para salvar um arquivo JSON com segredos do aplicativo, e **feito** para concluir a criação do novo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="05257-140">Tap **Download** to save a JSON file with application secrets, and **Done** to complete creation of the new app.</span></span>

* <span data-ttu-id="05257-141">Ao implantar o site será necessário rever o **Google Console** e registre uma nova url pública.</span><span class="sxs-lookup"><span data-stu-id="05257-141">When deploying the site you'll need to revisit the **Google Console** and register a new public url.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="05257-142">Loja Google ClientID e ClientSecret</span><span class="sxs-lookup"><span data-stu-id="05257-142">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="05257-143">Vincular as configurações confidenciais com o Google `Client ID` e `Client Secret` para sua configuração de aplicativo usando o [Manager segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="05257-143">Link sensitive settings like Google `Client ID` and `Client Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="05257-144">Para os fins deste tutorial, nomeie os tokens `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret`.</span><span class="sxs-lookup"><span data-stu-id="05257-144">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`.</span></span>

<span data-ttu-id="05257-145">Os valores para esses tokens podem ser encontrados no arquivo JSON baixado na etapa anterior em `web.client_id` e `web.client_secret`.</span><span class="sxs-lookup"><span data-stu-id="05257-145">The values for these tokens can be found in the JSON file downloaded in the previous step under `web.client_id` and `web.client_secret`.</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="05257-146">Configurar a autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="05257-146">Configure Google Authentication</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="05257-147">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="05257-147">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="05257-148">Adicione o serviço do Google no `ConfigureServices` método *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="05257-148">Add the Google service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](~/includes/chain-auth-providers.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="05257-149">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="05257-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="05257-150">O modelo de projeto usado neste tutorial garante que [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) pacote está instalado.</span><span class="sxs-lookup"><span data-stu-id="05257-150">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) package is installed.</span></span>

* <span data-ttu-id="05257-151">Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="05257-151">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="05257-152">Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="05257-152">To install with .NET Core CLI, execute the following in your project directory:</span></span>

`dotnet add package Microsoft.AspNetCore.Authentication.Google`

<span data-ttu-id="05257-153">Adicionar o middleware do Google no `Configure` método *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="05257-153">Add the Google middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

---

<span data-ttu-id="05257-154">Consulte o [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) referência de API para obter mais informações sobre opções de configuração com suporte pela autenticação do Google.</span><span class="sxs-lookup"><span data-stu-id="05257-154">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="05257-155">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="05257-155">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-google"></a><span data-ttu-id="05257-156">Entrar com o Google</span><span class="sxs-lookup"><span data-stu-id="05257-156">Sign in with Google</span></span>

<span data-ttu-id="05257-157">Execute o aplicativo e clique em **login**.</span><span class="sxs-lookup"><span data-stu-id="05257-157">Run your application and click **Log in**.</span></span> <span data-ttu-id="05257-158">Uma opção para entrar com o Google aparece:</span><span class="sxs-lookup"><span data-stu-id="05257-158">An option to sign in with Google appears:</span></span>

![Aplicativo Web em execução no Microsoft Edge: usuário não autenticado](index/_static/DoneGoogle.png)

<span data-ttu-id="05257-160">Quando você clica no Google, você é redirecionado para o Google para autenticação:</span><span class="sxs-lookup"><span data-stu-id="05257-160">When you click on Google, you are redirected to Google for authentication:</span></span>

![Diálogo de autenticação do Google](index/_static/GoogleLogin.png)

<span data-ttu-id="05257-162">Depois de inserir suas credenciais do Google, em seguida, você será redirecionado para o site onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="05257-162">After entering your Google credentials, then you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="05257-163">Agora você está conectado usando suas credenciais do Google:</span><span class="sxs-lookup"><span data-stu-id="05257-163">You are now logged in using your Google credentials:</span></span>

![Aplicativo Web em execução no Microsoft Edge: usuário autenticado](index/_static/Done.png)

## <a name="troubleshooting"></a><span data-ttu-id="05257-165">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="05257-165">Troubleshooting</span></span>

* <span data-ttu-id="05257-166">Se você receber um `403 (Forbidden)` página de erro do seu próprio aplicativo quando em execução no modo de desenvolvimento (ou interrupção no depurador com o mesmo erro), certifique-se de que **API do Google +** foi habilitada no **API do Gerenciador de biblioteca** seguindo as etapas listadas [anteriores nesta página](#create-the-app-in-google-api-console).</span><span class="sxs-lookup"><span data-stu-id="05257-166">If you receive a `403 (Forbidden)` error page from your own app when running in development mode (or break into the debugger with the same error), ensure that **Google+ API** has been enabled in the **API Manager Library** by following the steps listed [earlier on this page](#create-the-app-in-google-api-console).</span></span> <span data-ttu-id="05257-167">Se a entrada não funciona e não estiver obtendo erros, alterne para o modo de desenvolvimento para facilitar o problema depurar.</span><span class="sxs-lookup"><span data-stu-id="05257-167">If the sign in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="05257-168">Apenas **ASP.NET Core 2.x:** se a identidade não for configurada chamando `services.AddIdentity` no `ConfigureServices`, a autenticação resultará em *ArgumentException: a opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="05257-168">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="05257-169">O modelo de projeto usado neste tutorial garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="05257-169">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="05257-170">Se o banco de dados do site não tiver sido criado, aplicando a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="05257-170">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="05257-171">Toque em **aplicar migrações** para criar o banco de dados e a atualização para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="05257-171">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="05257-172">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="05257-172">Next steps</span></span>

* <span data-ttu-id="05257-173">Este artigo mostrou como você pode autenticar com o Google.</span><span class="sxs-lookup"><span data-stu-id="05257-173">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="05257-174">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados no [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="05257-174">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="05257-175">Depois de publicar seu site da web para o aplicativo web do Azure, você deve redefinir o `ClientSecret` no Console de API do Google.</span><span class="sxs-lookup"><span data-stu-id="05257-175">Once you publish your web site to Azure web app, you should reset the `ClientSecret` in the Google API Console.</span></span>

* <span data-ttu-id="05257-176">Definir o `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="05257-176">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="05257-177">O sistema de configuração é configurado para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="05257-177">The configuration system is set up to read keys from environment variables.</span></span>
