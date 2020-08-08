---
title: Autenticação de dois fatores com SMS no ASP.NET Core
author: rick-anderson
description: Saiba como configurar a autenticação de dois fatores (2FA) com um aplicativo ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: 28aef65234eaf162ba6e18a2594feb575c93b02f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019479"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="bcde2-103">Autenticação de dois fatores com SMS no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bcde2-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="bcde2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [suíço-desenvolvedores](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="bcde2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="bcde2-105">Os aplicativos autenticadores de autenticação de dois fatores (2FA), usando um algoritmo de senha única (TOTP) baseado em tempo, são a abordagem recomendada do setor para 2FA.</span><span class="sxs-lookup"><span data-stu-id="bcde2-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="bcde2-106">2FA usando TOTP é preferível ao SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="bcde2-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="bcde2-107">Para obter mais informações, consulte [habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) para ASP.NET Core 2,0 e posterior.</span><span class="sxs-lookup"><span data-stu-id="bcde2-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="bcde2-108">Este tutorial mostra como configurar a autenticação de dois fatores (2FA) usando o SMS.</span><span class="sxs-lookup"><span data-stu-id="bcde2-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="bcde2-109">Instruções são fornecidas para [twilio](https://www.twilio.com/) e [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), mas você pode usar qualquer outro provedor de SMS.</span><span class="sxs-lookup"><span data-stu-id="bcde2-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="bcde2-110">Recomendamos que você conclua a [confirmação de conta e a recuperação de senha](xref:security/authentication/accconfirm) antes de iniciar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="bcde2-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="bcde2-111">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="bcde2-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="bcde2-112">[Como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="bcde2-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="bcde2-113">Criar um projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bcde2-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="bcde2-114">Crie um novo aplicativo Web ASP.NET Core chamado `Web2FA` com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="bcde2-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="bcde2-115">Siga as instruções em <xref:security/enforcing-ssl> para configurar e exigir HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bcde2-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="bcde2-116">Criar uma conta SMS</span><span class="sxs-lookup"><span data-stu-id="bcde2-116">Create an SMS account</span></span>

<span data-ttu-id="bcde2-117">Crie uma conta SMS, por exemplo, de [twilio](https://www.twilio.com/) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="bcde2-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="bcde2-118">Registre as credenciais de autenticação (para twilio: AccountId e authToken, para ASPSMS: userKey e password).</span><span class="sxs-lookup"><span data-stu-id="bcde2-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="bcde2-119">Descobrindo credenciais do provedor de SMS</span><span class="sxs-lookup"><span data-stu-id="bcde2-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="bcde2-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="bcde2-120">**Twilio:**</span></span>

<span data-ttu-id="bcde2-121">Na guia painel da sua conta do twilio, copie o **SID da conta** e o **token de autenticação**.</span><span class="sxs-lookup"><span data-stu-id="bcde2-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="bcde2-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="bcde2-122">**ASPSMS:**</span></span>

<span data-ttu-id="bcde2-123">Em suas configurações de conta, navegue até **userKey** e copie-o junto com sua **senha**.</span><span class="sxs-lookup"><span data-stu-id="bcde2-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="bcde2-124">Mais tarde, armazenaremos esses valores no com a ferramenta Secret-Manager dentro das chaves `SMSAccountIdentification` e `SMSAccountPassword` .</span><span class="sxs-lookup"><span data-stu-id="bcde2-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="bcde2-125">Especificando SenderId/originador</span><span class="sxs-lookup"><span data-stu-id="bcde2-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="bcde2-126">**Twilio:** Na guia números, copie o número de **telefone**twilio.</span><span class="sxs-lookup"><span data-stu-id="bcde2-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="bcde2-127">**ASPSMS:** No menu desbloquear originadores, desbloqueie um ou mais originadores ou escolha um originador alfanumérico (sem suporte em todas as redes).</span><span class="sxs-lookup"><span data-stu-id="bcde2-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="bcde2-128">Posteriormente, esse valor será armazenado com a ferramenta Secret-Manager dentro da chave `SMSAccountFrom` .</span><span class="sxs-lookup"><span data-stu-id="bcde2-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="bcde2-129">Fornecer credenciais para o serviço SMS</span><span class="sxs-lookup"><span data-stu-id="bcde2-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="bcde2-130">Usaremos o padrão de [Opções](xref:fundamentals/configuration/options) para acessar a conta de usuário e as configurações de chave.</span><span class="sxs-lookup"><span data-stu-id="bcde2-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="bcde2-131">Crie uma classe para buscar a chave de SMS segura.</span><span class="sxs-lookup"><span data-stu-id="bcde2-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="bcde2-132">Para este exemplo, a `SMSoptions` classe é criada no arquivo *Services/SMSoptions. cs* .</span><span class="sxs-lookup"><span data-stu-id="bcde2-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="bcde2-133">Defina o `SMSAccountIdentification` `SMSAccountPassword` e `SMSAccountFrom` com a [ferramenta Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="bcde2-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="bcde2-134">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bcde2-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="bcde2-135">Adicione o pacote NuGet para o provedor de SMS.</span><span class="sxs-lookup"><span data-stu-id="bcde2-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="bcde2-136">No console do Gerenciador de pacotes (PMC), execute:</span><span class="sxs-lookup"><span data-stu-id="bcde2-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="bcde2-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="bcde2-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="bcde2-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="bcde2-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="bcde2-139">Adicione código no arquivo *Services/messageservices. cs* para habilitar o SMS.</span><span class="sxs-lookup"><span data-stu-id="bcde2-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="bcde2-140">Use a seção twilio ou ASPSMS:</span><span class="sxs-lookup"><span data-stu-id="bcde2-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="bcde2-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="bcde2-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="bcde2-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="bcde2-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="bcde2-143">Configurar a inicialização para usar`SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="bcde2-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="bcde2-144">Adicione `SMSoptions` ao contêiner de serviço no `ConfigureServices` método no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bcde2-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="bcde2-145">Habilitar a autenticação de dois fatores</span><span class="sxs-lookup"><span data-stu-id="bcde2-145">Enable two-factor authentication</span></span>

<span data-ttu-id="bcde2-146">Abra o arquivo de exibição *views/Manage/index. cshtml* Razor e remova os caracteres de comentário (portanto, nenhuma marcação é comentada).</span><span class="sxs-lookup"><span data-stu-id="bcde2-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="bcde2-147">Fazer logon com a autenticação de dois fatores</span><span class="sxs-lookup"><span data-stu-id="bcde2-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="bcde2-148">Executar o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="bcde2-148">Run the app and register a new user</span></span>

![Exibição de registro de aplicativo Web aberta no Microsoft Edge](2fa/_static/login2fa1.png)

* <span data-ttu-id="bcde2-150">Toque em seu nome de usuário, que ativa o `Index` método de ação em gerenciar controlador.</span><span class="sxs-lookup"><span data-stu-id="bcde2-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="bcde2-151">Em seguida, toque no link número de telefone **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="bcde2-151">Then tap the phone number **Add** link.</span></span>

![Gerenciar exibição-toque no link "Adicionar"](2fa/_static/login2fa2.png)

* <span data-ttu-id="bcde2-153">Adicione um número de telefone que receberá o código de verificação e toque em **enviar código de verificação**.</span><span class="sxs-lookup"><span data-stu-id="bcde2-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Página Adicionar número de telefone](2fa/_static/login2fa3.png)

* <span data-ttu-id="bcde2-155">Você receberá uma mensagem de texto com o código de verificação.</span><span class="sxs-lookup"><span data-stu-id="bcde2-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="bcde2-156">Insira-o e toque em **Enviar**</span><span class="sxs-lookup"><span data-stu-id="bcde2-156">Enter it and tap **Submit**</span></span>

![Página verificar número de telefone](2fa/_static/login2fa4.png)

<span data-ttu-id="bcde2-158">Se você não receber uma mensagem de texto, consulte a página de log do twilio.</span><span class="sxs-lookup"><span data-stu-id="bcde2-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="bcde2-159">O modo de exibição gerenciar mostra que seu número de telefone foi adicionado com êxito.</span><span class="sxs-lookup"><span data-stu-id="bcde2-159">The Manage view shows your phone number was added successfully.</span></span>

![Gerenciar exibição-número de telefone adicionado com êxito](2fa/_static/login2fa5.png)

* <span data-ttu-id="bcde2-161">Toque em **habilitar** para habilitar a autenticação de dois fatores.</span><span class="sxs-lookup"><span data-stu-id="bcde2-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Gerenciar exibição-habilitar a autenticação de dois fatores](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="bcde2-163">Testar a autenticação de dois fatores</span><span class="sxs-lookup"><span data-stu-id="bcde2-163">Test two-factor authentication</span></span>

* <span data-ttu-id="bcde2-164">Dê logoff.</span><span class="sxs-lookup"><span data-stu-id="bcde2-164">Log off.</span></span>

* <span data-ttu-id="bcde2-165">Fazer logon.</span><span class="sxs-lookup"><span data-stu-id="bcde2-165">Log in.</span></span>

* <span data-ttu-id="bcde2-166">A conta de usuário habilitou a autenticação de dois fatores, portanto, você precisa fornecer o segundo fator de autenticação.</span><span class="sxs-lookup"><span data-stu-id="bcde2-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="bcde2-167">Neste tutorial, você habilitou a verificação de telefone.</span><span class="sxs-lookup"><span data-stu-id="bcde2-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="bcde2-168">Os modelos internos também permitem que você configure o email como o segundo fator.</span><span class="sxs-lookup"><span data-stu-id="bcde2-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="bcde2-169">Você pode configurar um segundo fator adicional para autenticação, como códigos QR.</span><span class="sxs-lookup"><span data-stu-id="bcde2-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="bcde2-170">Toque em **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="bcde2-170">Tap **Submit**.</span></span>

![Enviar exibição de código de verificação](2fa/_static/login2fa7.png)

* <span data-ttu-id="bcde2-172">Insira o código obtido na mensagem SMS.</span><span class="sxs-lookup"><span data-stu-id="bcde2-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="bcde2-173">Clicar na caixa de seleção **lembrar este navegador** lhe isentará de precisar usar o 2FA para fazer logon ao usar o mesmo dispositivo e navegador.</span><span class="sxs-lookup"><span data-stu-id="bcde2-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="bcde2-174">Habilitar o 2FA e clicar em **lembrar este navegador** fornecerá uma forte proteção 2FA de usuários mal-intencionados tentando acessar sua conta, desde que eles não tenham acesso ao seu dispositivo.</span><span class="sxs-lookup"><span data-stu-id="bcde2-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="bcde2-175">Você pode fazer isso em qualquer dispositivo privado que usa regularmente.</span><span class="sxs-lookup"><span data-stu-id="bcde2-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="bcde2-176">Ao configurar **lembrar esse navegador**, você obtém a segurança adicional do 2FA de dispositivos que você não usa regularmente, e você tem a conveniência de não ter de passar pelo 2FA em seus próprios dispositivos.</span><span class="sxs-lookup"><span data-stu-id="bcde2-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Verificar exibição](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="bcde2-178">Bloqueio de conta para proteção contra ataques de força bruta</span><span class="sxs-lookup"><span data-stu-id="bcde2-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="bcde2-179">O bloqueio de conta é recomendado com 2FA.</span><span class="sxs-lookup"><span data-stu-id="bcde2-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="bcde2-180">Depois que um usuário entrar por meio de uma conta local ou de uma conta social, cada tentativa com falha em 2FA será armazenada.</span><span class="sxs-lookup"><span data-stu-id="bcde2-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="bcde2-181">Se o número máximo de tentativas de acesso com falha for atingido, o usuário será bloqueado (padrão: bloqueio de 5 minutos após 5 tentativas de acesso com falha).</span><span class="sxs-lookup"><span data-stu-id="bcde2-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="bcde2-182">Uma autenticação bem-sucedida redefine a contagem de tentativas de acesso com falha e redefine o relógio.</span><span class="sxs-lookup"><span data-stu-id="bcde2-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="bcde2-183">O tempo máximo de bloqueio e tentativas de acesso com falha podem ser definidos com [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) e [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="bcde2-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="bcde2-184">O seguinte configura o bloqueio de conta por 10 minutos após 10 tentativas de acesso com falha:</span><span class="sxs-lookup"><span data-stu-id="bcde2-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="bcde2-185">Confirme se o [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) define `lockoutOnFailure` como `true` :</span><span class="sxs-lookup"><span data-stu-id="bcde2-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
