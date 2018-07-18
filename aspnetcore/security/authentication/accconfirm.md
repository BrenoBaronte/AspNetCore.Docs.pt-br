---
title: Confirmação de conta e de recuperação de senha no ASP.NET Core
author: rick-anderson
description: Saiba como criar um aplicativo ASP.NET Core com a redefinição de senha e de confirmação de email.
ms.author: riande
ms.date: 2/11/2018
uid: security/authentication/accconfirm
ms.openlocfilehash: 8e175cd19ca4a9de1e7cf6b330b3d82f309b6501
ms.sourcegitcommit: e12f45ddcbe99102a74d4077df27d6c0ebba49c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2018
ms.locfileid: "39063332"
---
::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="07ddc-103">Ver [esse arquivo PDF](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/asp.net_repo_pdf_1-16-18.pdf) para o ASP.NET Core 1.1 e a versão 2.1.</span><span class="sxs-lookup"><span data-stu-id="07ddc-103">See [this PDF file](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 and 2.1 version.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="07ddc-104">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07ddc-104">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="07ddc-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="07ddc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="07ddc-106">Este tutorial mostra como criar um aplicativo ASP.NET Core com a redefinição de senha e de confirmação de email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-106">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="07ddc-107">Este tutorial é **não** um tópico de início.</span><span class="sxs-lookup"><span data-stu-id="07ddc-107">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="07ddc-108">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="07ddc-108">You should be familiar with:</span></span>

* [<span data-ttu-id="07ddc-109">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07ddc-109">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="07ddc-110">Autenticação</span><span class="sxs-lookup"><span data-stu-id="07ddc-110">Authentication</span></span>](xref:security/authentication/index)
* [<span data-ttu-id="07ddc-111">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="07ddc-111">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a><span data-ttu-id="07ddc-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="07ddc-112">Prerequisites</span></span>

<span data-ttu-id="07ddc-113">[!INCLUDE [](~/includes/2.1-SDK.md) [](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="07ddc-113">[!INCLUDE [](~/includes/2.1-SDK.md) [](~/includes/2.1-SDK.md)]</span></span>

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="07ddc-114">Criar um aplicativo web e o scaffolding de identidade</span><span class="sxs-lookup"><span data-stu-id="07ddc-114">Create a web  app and scaffold Identity</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07ddc-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07ddc-115">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="07ddc-116">No Visual Studio, crie um novo **aplicativo Web** projeto.</span><span class="sxs-lookup"><span data-stu-id="07ddc-116">In Visual Studio, create a new **Web Application** project.</span></span>
* <span data-ttu-id="07ddc-117">Selecione **ASP.NET Core 2.1**.</span><span class="sxs-lookup"><span data-stu-id="07ddc-117">Select **ASP.NET Core 2.1**.</span></span>
* <span data-ttu-id="07ddc-118">Mantenha o padrão **autenticação** definido como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="07ddc-118">Keep the default **Authentication** set to **No Authentication**.</span></span> <span data-ttu-id="07ddc-119">Autenticação é adicionada na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="07ddc-119">Authentication is added in the next step.</span></span>

<span data-ttu-id="07ddc-120">Na próxima etapa:</span><span class="sxs-lookup"><span data-stu-id="07ddc-120">In the next step:</span></span>

* <span data-ttu-id="07ddc-121">Definir a página de layout *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07ddc-121">Set the layout page to *~/Pages/Shared/_Layout.cshtml*</span></span>
* <span data-ttu-id="07ddc-122">Selecione *conta/registro*</span><span class="sxs-lookup"><span data-stu-id="07ddc-122">Select *Account/Register*</span></span>
* <span data-ttu-id="07ddc-123">Criar um novo **classe de contexto de dados**</span><span class="sxs-lookup"><span data-stu-id="07ddc-123">Create a new **Data context class**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="07ddc-124">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="07ddc-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet new webapp -o WebPWrecover
cd WebPWrecover
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -fi Account.Register -dc WebPWrecover.Models.WebPWrecoverContext
dotnet ef migrations add CreateIdentitySchema
dotnet ef database drop -f
dotnet ef database update
dotnet build
```

<span data-ttu-id="07ddc-125">Executar `dotnet aspnet-codegenerator identity --help` para obter ajuda sobre a ferramenta de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="07ddc-125">Run `dotnet aspnet-codegenerator identity --help` to get help on the scaffolding tool.</span></span>

------

<span data-ttu-id="07ddc-126">Siga as instruções em [habilitar a autenticação](xref:security/authentication/scaffold-identity#useauthentication):</span><span class="sxs-lookup"><span data-stu-id="07ddc-126">Follow the instructions in [Enable authentication](xref:security/authentication/scaffold-identity#useauthentication):</span></span>

* <span data-ttu-id="07ddc-127">Adicionar `app.UseAuthentication();` para `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="07ddc-127">Add `app.UseAuthentication();` to `Startup.Configure`</span></span>
* <span data-ttu-id="07ddc-128">Adicionar `<partial name="_LoginPartial" />` ao arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="07ddc-128">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="07ddc-129">Novo registro de usuário de teste</span><span class="sxs-lookup"><span data-stu-id="07ddc-129">Test new user registration</span></span>

<span data-ttu-id="07ddc-130">Execute o aplicativo, selecione a **registrar** vincular e registrar um usuário.</span><span class="sxs-lookup"><span data-stu-id="07ddc-130">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="07ddc-131">Neste ponto, a validação apenas no email é com o [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="07ddc-131">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="07ddc-132">Depois de enviar o registro, você está conectado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="07ddc-132">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="07ddc-133">Posteriormente no tutorial, o código é atualizado para que novos usuários não podem fazer logon até que o email é validado.</span><span class="sxs-lookup"><span data-stu-id="07ddc-133">Later in the tutorial, the code is updated so new users can't log in until their email is validated.</span></span>

## <a name="view-the-identity-database"></a><span data-ttu-id="07ddc-134">Exibir o banco de dados de identidade</span><span class="sxs-lookup"><span data-stu-id="07ddc-134">View the Identity database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07ddc-135">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07ddc-135">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="07ddc-136">Dos **modo de exibição** menu, selecione **Pesquisador de objetos do SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="07ddc-136">From the **View** menu, select **SQL Server Object Explorer** (SSOX).</span></span>
* <span data-ttu-id="07ddc-137">Navegue até **(localdb) MSSQLLocalDB (SQL Server 13)**.</span><span class="sxs-lookup"><span data-stu-id="07ddc-137">Navigate to **(localdb)MSSQLLocalDB(SQL Server 13)**.</span></span> <span data-ttu-id="07ddc-138">Clique duas vezes em **dbo. AspNetUsers** > **exibir dados**:</span><span class="sxs-lookup"><span data-stu-id="07ddc-138">Right-click on **dbo.AspNetUsers** > **View Data**:</span></span>

![Menu contextual em tabela AspNetUsers no Pesquisador de objetos do SQL Server](accconfirm/_static/ssox.png)

<span data-ttu-id="07ddc-140">Observe a tabela `EmailConfirmed` campo é `False`.</span><span class="sxs-lookup"><span data-stu-id="07ddc-140">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="07ddc-141">Você talvez queira usar este email novamente na próxima etapa quando o aplicativo envia um email de confirmação.</span><span class="sxs-lookup"><span data-stu-id="07ddc-141">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="07ddc-142">Clique com botão direito na linha e selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="07ddc-142">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="07ddc-143">Excluir o alias de email torna mais fácil nas etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="07ddc-143">Deleting the email alias makes it easier in the following steps.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="07ddc-144">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="07ddc-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="07ddc-145">Ver [trabalhar com SQLite em um projeto do ASP.NET Core MVC](xref:tutorials/first-mvc-app-xplat/working-with-sql) para obter instruções sobre como exibir o banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="07ddc-145">See [Work with SQLite in an ASP.NET Core MVC project](xref:tutorials/first-mvc-app-xplat/working-with-sql) for instructions on how to view the SQLite database.</span></span>

------

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a><span data-ttu-id="07ddc-146">Exigir email de confirmação</span><span class="sxs-lookup"><span data-stu-id="07ddc-146">Require email confirmation</span></span>

<span data-ttu-id="07ddc-147">É uma prática recomendada para confirmar o email de um novo registro de usuário.</span><span class="sxs-lookup"><span data-stu-id="07ddc-147">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="07ddc-148">Ajuda a confirmação para verificar se eles não estiver representando alguém de email (ou seja, eles ainda não registrados com o email de outra pessoa).</span><span class="sxs-lookup"><span data-stu-id="07ddc-148">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="07ddc-149">Suponha que você tinha um fórum de discussão e quisesse impedir "yli@example.com"de registrar-se como"nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="07ddc-149">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="07ddc-150">Sem email de confirmação "nolivetto@contoso.com" pode receber emails indesejados de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="07ddc-150">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="07ddc-151">Suponha que o usuário registrado acidentalmente como "ylo@example.com" e ainda não tenha notado o erro de ortografia de "yli".</span><span class="sxs-lookup"><span data-stu-id="07ddc-151">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="07ddc-152">Eles não seria capazes de usar a recuperação de senha porque o aplicativo não tiver seu email correto.</span><span class="sxs-lookup"><span data-stu-id="07ddc-152">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="07ddc-153">Email de confirmação oferece proteção limitada contra bots.</span><span class="sxs-lookup"><span data-stu-id="07ddc-153">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="07ddc-154">Email de confirmação não fornece proteção contra usuários mal-intencionados com muitas contas de email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-154">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="07ddc-155">Geralmente você deseja impedir que novos usuários incluam dados em seu site até que eles tenham um email confirmado.</span><span class="sxs-lookup"><span data-stu-id="07ddc-155">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="07ddc-156">Atualização *Areas/Identity/IdentityHostingStartup.cs* para exigir um email confirmado:</span><span class="sxs-lookup"><span data-stu-id="07ddc-156">Update *Areas/Identity/IdentityHostingStartup.cs*  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Areas/Identity/IdentityHostingStartup.cs?name=snippet1&highlight=10-13)]

<span data-ttu-id="07ddc-157">`config.SignIn.RequireConfirmedEmail = true;` impede que os usuários registrados entrar até que o email seja confirmado.</span><span class="sxs-lookup"><span data-stu-id="07ddc-157">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="07ddc-158">Configurar o provedor de email</span><span class="sxs-lookup"><span data-stu-id="07ddc-158">Configure email provider</span></span>

<span data-ttu-id="07ddc-159">Neste tutorial [SendGrid](https://sendgrid.com) é usado para enviar email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-159">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="07ddc-160">Você precisa de uma conta do SendGrid e uma chave para enviar email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-160">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="07ddc-161">Você pode usar outros provedores de email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-161">You can use other email providers.</span></span> <span data-ttu-id="07ddc-162">ASP.NET Core 2.x inclui `System.Net.Mail`, que permite que você enviar um email de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="07ddc-162">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="07ddc-163">É recomendável que usar o SendGrid ou outro serviço de email para enviar email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-163">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="07ddc-164">SMTP é difícil proteger e configurado corretamente.</span><span class="sxs-lookup"><span data-stu-id="07ddc-164">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="07ddc-165">O [padrão de opções](xref:fundamentals/configuration/options) é usado para acessar as configurações de conta e chave do usuário.</span><span class="sxs-lookup"><span data-stu-id="07ddc-165">The [Options pattern](xref:fundamentals/configuration/options) is used to access the user account and key settings.</span></span> <span data-ttu-id="07ddc-166">Para obter mais informações, consulte [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="07ddc-166">For more information, see [configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="07ddc-167">Crie uma classe para buscar a chave de email seguro.</span><span class="sxs-lookup"><span data-stu-id="07ddc-167">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="07ddc-168">Para este exemplo, crie *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="07ddc-168">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="07ddc-169">Configurar segredos de usuário do SendGrid</span><span class="sxs-lookup"><span data-stu-id="07ddc-169">Configure SendGrid user secrets</span></span>

<span data-ttu-id="07ddc-170">Adicionar um único `<UserSecretsId>` de valor para o `<PropertyGroup>` elemento do arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="07ddc-170">Add a unique `<UserSecretsId>` value to the `<PropertyGroup>` element of the project file:</span></span>

[!code-xml[](accconfirm/sample/WebPWrecover21/WebPWrecover.csproj?highlight=5)]

<span data-ttu-id="07ddc-171">Defina as `SendGridUser` e `SendGridKey` com o [ferramenta secret manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="07ddc-171">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="07ddc-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="07ddc-172">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="07ddc-173">No Windows, o Secret Manager armazena os pares de chaves/valor em uma *Secrets* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span><span class="sxs-lookup"><span data-stu-id="07ddc-173">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="07ddc-174">O conteúdo a *Secrets* arquivo não são criptografadas.</span><span class="sxs-lookup"><span data-stu-id="07ddc-174">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="07ddc-175">O *Secrets* arquivo é mostrado abaixo (o `SendGridKey` valor tiver sido removido.)</span><span class="sxs-lookup"><span data-stu-id="07ddc-175">The *secrets.json* file is shown below (the `SendGridKey` value has been removed.)</span></span>

 ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```

### <a name="install-sendgrid"></a><span data-ttu-id="07ddc-176">Instalar o SendGrid</span><span class="sxs-lookup"><span data-stu-id="07ddc-176">Install SendGrid</span></span>

<span data-ttu-id="07ddc-177">Este tutorial mostra como adicionar notificações de email por meio [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando SMTP e outros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="07ddc-177">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="07ddc-178">Instalar o `SendGrid` pacote do NuGet:</span><span class="sxs-lookup"><span data-stu-id="07ddc-178">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07ddc-179">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07ddc-179">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="07ddc-180">No Console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="07ddc-180">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="07ddc-181">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="07ddc-181">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="07ddc-182">No console, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="07ddc-182">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

------

<span data-ttu-id="07ddc-183">Ver [inicie gratuitamente com o SendGrid](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.</span><span class="sxs-lookup"><span data-stu-id="07ddc-183">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>
### <a name="implement-iemailsender"></a><span data-ttu-id="07ddc-184">Implementar IEmailSender</span><span class="sxs-lookup"><span data-stu-id="07ddc-184">Implement IEmailSender</span></span>

<span data-ttu-id="07ddc-185">Para implementar `IEmailSender`, crie *Services/EmailSender.cs* com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="07ddc-185">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="07ddc-186">Configurar a inicialização para dar suporte a email</span><span class="sxs-lookup"><span data-stu-id="07ddc-186">Configure startup to support email</span></span>

<span data-ttu-id="07ddc-187">Adicione o seguinte código para o `ConfigureServices` método na *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="07ddc-187">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="07ddc-188">Adicionar `EmailSender` como um serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="07ddc-188">Add `EmailSender` as a singleton service.</span></span>
* <span data-ttu-id="07ddc-189">Registrar o `AuthMessageSenderOptions` instância de configuração.</span><span class="sxs-lookup"><span data-stu-id="07ddc-189">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Startup.cs?name=snippet2&highlight=12-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="07ddc-190">Habilitar a recuperação de confirmação e a senha da conta</span><span class="sxs-lookup"><span data-stu-id="07ddc-190">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="07ddc-191">O modelo tem o código para recuperação de confirmação e a senha da conta.</span><span class="sxs-lookup"><span data-stu-id="07ddc-191">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="07ddc-192">Localizar o `OnPostAsync` método no *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="07ddc-192">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="07ddc-193">Evitar que usuários recém-registrados seja registrada automaticamente comentando a linha a seguir:</span><span class="sxs-lookup"><span data-stu-id="07ddc-193">Prevent newly registered users from being automatically logged on by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="07ddc-194">O método complete é mostrado com a linha alterada realçada:</span><span class="sxs-lookup"><span data-stu-id="07ddc-194">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="07ddc-195">Registrar, confirme se o email e redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="07ddc-195">Register, confirm email, and reset password</span></span>

<span data-ttu-id="07ddc-196">Executar o aplicativo web e testar o fluxo de recuperação de senha e confirmação de conta.</span><span class="sxs-lookup"><span data-stu-id="07ddc-196">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="07ddc-197">Execute o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="07ddc-197">Run the app and register a new user</span></span>

  ![Registrar conta exibição do aplicativo da Web](accconfirm/_static/loginaccconfirm1.png)

* <span data-ttu-id="07ddc-199">Verifique seu email para o link de confirmação de conta.</span><span class="sxs-lookup"><span data-stu-id="07ddc-199">Check your email for the account confirmation link.</span></span> <span data-ttu-id="07ddc-200">Ver [depurar email](#debug) se você não receber o email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-200">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="07ddc-201">Clique no link para confirmar seu email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-201">Click the link to confirm your email.</span></span>
* <span data-ttu-id="07ddc-202">Faça logon com seu email e senha.</span><span class="sxs-lookup"><span data-stu-id="07ddc-202">Log in with your email and password.</span></span>
* <span data-ttu-id="07ddc-203">Faça logoff.</span><span class="sxs-lookup"><span data-stu-id="07ddc-203">Log off.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="07ddc-204">Exibir a página Gerenciar</span><span class="sxs-lookup"><span data-stu-id="07ddc-204">View the manage page</span></span>

<span data-ttu-id="07ddc-205">Selecione seu nome de usuário no navegador: ![janela do navegador com o nome de usuário](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="07ddc-205">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="07ddc-206">Talvez seja necessário expandir a barra de navegação para ver o nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="07ddc-206">You might need to expand the navbar to see user name.</span></span>

![barra de navegação](accconfirm/_static/x.png)

<span data-ttu-id="07ddc-208">A página Gerenciar é exibida com o **perfil** guia selecionada.</span><span class="sxs-lookup"><span data-stu-id="07ddc-208">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="07ddc-209">O **Email** mostra uma caixa de seleção que indica o email foi confirmada.</span><span class="sxs-lookup"><span data-stu-id="07ddc-209">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="07ddc-210">Teste a redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="07ddc-210">Test password reset</span></span>

* <span data-ttu-id="07ddc-211">Se você estiver conectado, selecione **Logout**.</span><span class="sxs-lookup"><span data-stu-id="07ddc-211">If you're logged in, select **Logout**.</span></span>
* <span data-ttu-id="07ddc-212">Selecione o **login** link e selecione o **esqueceu sua senha?** link.</span><span class="sxs-lookup"><span data-stu-id="07ddc-212">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="07ddc-213">Insira o email usado para registrar a conta.</span><span class="sxs-lookup"><span data-stu-id="07ddc-213">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="07ddc-214">Um email com um link para redefinir sua senha é enviado.</span><span class="sxs-lookup"><span data-stu-id="07ddc-214">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="07ddc-215">Verifique seu email e clique no link para redefinir sua senha.</span><span class="sxs-lookup"><span data-stu-id="07ddc-215">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="07ddc-216">Depois que sua senha foi redefinida com êxito, você pode entrar com seu email e a nova senha.</span><span class="sxs-lookup"><span data-stu-id="07ddc-216">After your password has been successfully reset, you can log in with your email and new password.</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="07ddc-217">Depurar o email</span><span class="sxs-lookup"><span data-stu-id="07ddc-217">Debug email</span></span>

<span data-ttu-id="07ddc-218">Se você não é possível obter o trabalho de email:</span><span class="sxs-lookup"><span data-stu-id="07ddc-218">If you can't get email working:</span></span>

* <span data-ttu-id="07ddc-219">Defina um ponto de interrupção no `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="07ddc-219">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="07ddc-220">Criar uma [aplicativo de console para enviar email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando um código semelhante ao `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="07ddc-220">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="07ddc-221">Examine os [atividade de Email](https://sendgrid.com/docs/User_Guide/email_activity.html) página.</span><span class="sxs-lookup"><span data-stu-id="07ddc-221">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="07ddc-222">Verifique sua pasta de spam.</span><span class="sxs-lookup"><span data-stu-id="07ddc-222">Check your spam folder.</span></span>
* <span data-ttu-id="07ddc-223">Tente outro alias de email em outro provedor de email (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="07ddc-223">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="07ddc-224">Tente enviar para contas de email diferente.</span><span class="sxs-lookup"><span data-stu-id="07ddc-224">Try sending to different email accounts.</span></span>

<span data-ttu-id="07ddc-225">**Uma prática recomendada de segurança** é **não** use segredos de produção em desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="07ddc-225">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="07ddc-226">Se você publicar o aplicativo no Azure, você pode definir os segredos do SendGrid como configurações de aplicativo no portal do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="07ddc-226">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="07ddc-227">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="07ddc-227">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="07ddc-228">Combine as contas de logon social e local</span><span class="sxs-lookup"><span data-stu-id="07ddc-228">Combine social and local login accounts</span></span>

<span data-ttu-id="07ddc-229">Para concluir esta seção, você deve primeiro habilitar um provedor de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="07ddc-229">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="07ddc-230">Ver [Facebook, Google e a autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="07ddc-230">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="07ddc-231">Você pode combinar as contas locais e sociais clicando no link seu email.</span><span class="sxs-lookup"><span data-stu-id="07ddc-231">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="07ddc-232">Na sequência a seguir, "RickAndMSFT@gmail.com" é criado como um logon local; no entanto, você pode criar a conta como um logon social primeiro e adicionar um logon local.</span><span class="sxs-lookup"><span data-stu-id="07ddc-232">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="07ddc-234">Clique no **gerenciar** link.</span><span class="sxs-lookup"><span data-stu-id="07ddc-234">Click on the **Manage** link.</span></span> <span data-ttu-id="07ddc-235">Observe externo 0 (logons sociais) associado a essa conta.</span><span class="sxs-lookup"><span data-stu-id="07ddc-235">Note the 0 external (social logins) associated with this account.</span></span>

![Gerenciar o modo de exibição](accconfirm/_static/manage.png)

<span data-ttu-id="07ddc-237">Clique no link para outro serviço de logon e aceitar as solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="07ddc-237">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="07ddc-238">Na imagem a seguir, o Facebook é o provedor de autenticação externa:</span><span class="sxs-lookup"><span data-stu-id="07ddc-238">In the following image, Facebook is the external authentication provider:</span></span>

![Gerenciar seu modo de exibição de logons externos lista Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="07ddc-240">As duas contas foram combinadas.</span><span class="sxs-lookup"><span data-stu-id="07ddc-240">The two accounts have been combined.</span></span> <span data-ttu-id="07ddc-241">É possível fazer logon com qualquer uma das contas.</span><span class="sxs-lookup"><span data-stu-id="07ddc-241">You are able to log on with either account.</span></span> <span data-ttu-id="07ddc-242">Convém que os usuários adicionem contas locais no caso de seu serviço de autenticação de logon social está inoperante ou, mais provavelmente eles tiver perdido o acesso à sua conta social.</span><span class="sxs-lookup"><span data-stu-id="07ddc-242">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="07ddc-243">Habilitar confirmação de conta depois que um site tem usuários</span><span class="sxs-lookup"><span data-stu-id="07ddc-243">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="07ddc-244">Habilitando a confirmação de conta em um site com usuários bloqueia todos os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="07ddc-244">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="07ddc-245">Os usuários existentes estão bloqueados porque suas contas não são confirmadas.</span><span class="sxs-lookup"><span data-stu-id="07ddc-245">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="07ddc-246">Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="07ddc-246">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="07ddc-247">Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmada.</span><span class="sxs-lookup"><span data-stu-id="07ddc-247">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="07ddc-248">Confirme se os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="07ddc-248">Confirm exiting users.</span></span> <span data-ttu-id="07ddc-249">Por exemplo, envio em lote-emails com links de confirmação.</span><span class="sxs-lookup"><span data-stu-id="07ddc-249">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end