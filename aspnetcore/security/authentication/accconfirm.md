---
title: Confirmação de conta e de recuperação de senha no ASP.NET Core
author: rick-anderson
description: Saiba como criar um aplicativo ASP.NET Core com confirmação de email e redefinição de senha.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: 7016c2c1997d961f4b3d3cf513fc1769bd65247b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021608"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Confirmação de conta e de recuperação de senha no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)e [Joe Audette](https://twitter.com/joeaudette)

Este tutorial mostra como criar um aplicativo ASP.NET Core com confirmação de email e redefinição de senha. Este tutorial **não** é um tópico inicial. Você deve estar familiarizado com:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Autenticação](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Pré-requisitos

[SDK do .NET Core 3,0 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Criar e testar um aplicativo Web com autenticação

Execute os comandos a seguir para criar um aplicativo Web com autenticação.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Execute o aplicativo, selecione o link **registrar** e registre um usuário. Depois de registrado, você será redirecionado para a `/Identity/Account/RegisterConfirmation` página para, que contém um link para simular a confirmação de email:

* Selecione o `Click here to confirm your account` link.
* Selecione o link de **logon** e entre com as mesmas credenciais.
* Selecione o `Hello YourEmail@provider.com!` link, que o redireciona para a `/Identity/Account/Manage/PersonalData` página.
* Selecione a guia **dados pessoais** à esquerda e, em seguida, selecione **excluir**.

### <a name="configure-an-email-provider"></a>Configurar um provedor de email

Neste tutorial, [SendGrid](https://sendgrid.com) é usado para enviar email. Você precisa de uma conta e chave do SendGrid para enviar email. Você pode usar outros provedores de email. Recomendamos que você use o SendGrid ou outro serviço de email para enviar email. O SMTP é difícil de proteger e configurar corretamente.

A conta SendGrid pode exigir a [adição de um remetente](https://sendgrid.com/docs/ui/sending-email/senders/).

Crie uma classe para buscar a chave de email seguro. Para este exemplo, crie *Services/AuthMessageSenderOptions. cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Configurar segredos de usuário do SendGrid

Defina `SendGridUser` e `SendGridKey` com a [ferramenta Secret-Manager](xref:security/app-secrets). Por exemplo:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

No Windows, o Gerenciador de segredo armazena pares de chaves/valores em um *secrets.jsno* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` diretório.

O conteúdo do *secrets.jsno* arquivo não está criptografado. A marcação a seguir mostra o *secrets.jsno* arquivo. O `SendGridKey` valor foi removido.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalar o SendGrid

Este tutorial mostra como adicionar notificações por email por meio do [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando o SMTP e outros mecanismos.

Instale o `SendGrid` pacote NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

No console do Gerenciador de pacotes, digite o seguinte comando:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

No console do, digite o seguinte comando:

```dotnetcli
dotnet add package SendGrid
```

---

Consulte Introdução ao [SendGrid gratuitamente](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.

### <a name="implement-iemailsender"></a>Implementar IEmailSender

Para implementar `IEmailSender` , crie os *Serviços/EmailSender. cs* com um código semelhante ao seguinte:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Configurar a inicialização para o email de suporte

Adicione o seguinte código ao `ConfigureServices` método no arquivo *Startup.cs* :

* Adicionar `EmailSender` como um serviço transitório.
* Registre a `AuthMessageSenderOptions` instância de configuração.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a>Scaffold RegisterConfirmation

Siga as instruções para [Scaffold Identity ](xref:security/authentication/scaffold-identity) e Scaffold `RegisterConfirmation` .

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a>Registrar, confirmar email e Redefinir senha

Execute o aplicativo Web e teste a confirmação da conta e o fluxo de recuperação de senha.

* Executar o aplicativo e registrar um novo usuário
* Verifique seu email para o link de confirmação da conta. Consulte [depurar email](#debug) se você não receber o email.
* Clique no link para confirmar seu email.
* Entre com seu email e senha.
* Saia.

### <a name="test-password-reset"></a>Testar redefinição de senha

* Se você estiver conectado, selecione **logout**.
* Selecione o link **fazer logon** e selecione o link **esqueceu sua senha?** .
* Insira o email que você usou para registrar a conta.
* Um email com um link para redefinir sua senha é enviado. Verifique seu email e clique no link para redefinir sua senha. Depois que a senha for redefinida com êxito, você poderá entrar com seu email e nova senha.

## <a name="change-email-and-activity-timeout"></a>Alterar o tempo limite de email e atividade

O tempo limite de inatividade padrão é de 14 dias. O código a seguir define o tempo limite de inatividade como 5 dias:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Alterar todas as vidas de token de proteção de dados

O código a seguir altera todos os tokens de proteção de dados período de tempo limite para 3 horas:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Os Identity tokens de usuário internos (consulte [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) têm um [tempo limite de um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Alterar o ciclo de vida do token de email

A vida útil do token padrão dos [ Identity tokens de usuário](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é de [um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). Esta seção mostra como alterar o ciclo de vida do token de email.

Adicione um [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Adicione o provedor personalizado ao contêiner de serviço:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a>Reenviar confirmação de email

Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Depurar email

Se você não puder obter emails funcionando:

* Defina um ponto de interrupção em `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.
* Crie um [aplicativo de console para enviar emails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando código semelhante para `EmailSender.Execute` .
* Examine a página [atividade de email](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Verifique sua pasta de spam.
* Experimente outro alias de email em um provedor de email diferente (Microsoft, Yahoo, Gmail, etc.)
* Tente enviar para contas de email diferentes.

**Uma prática recomendada de segurança** é **não** usar segredos de produção em teste e desenvolvimento. Se você publicar o aplicativo no Azure, defina os segredos do SendGrid como configurações de aplicativo no portal do aplicativo Web do Azure. O sistema de configuração é configurado para ler chaves de variáveis de ambiente.

## <a name="combine-social-and-local-login-accounts"></a>Combinar contas de logon sociais e locais

Para concluir esta seção, primeiro você deve habilitar um provedor de autenticação externa. Consulte [Facebook, Google e autenticação de provedor externo](xref:security/authentication/social/index).

Você pode combinar contas locais e sociais clicando no link de email. Na sequência a seguir, " RickAndMSFT@gmail.com " é criado primeiro como um logon local; no entanto, você pode criar a conta como um logon social primeiro e, em seguida, adicionar um logon local.

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

Clique no link **gerenciar** . Observe o 0 externo (logons sociais) associado a essa conta.

![Gerenciar modo de exibição](accconfirm/_static/manage.png)

Clique no link para outro serviço de logon e aceite as solicitações do aplicativo. Na imagem a seguir, o Facebook é o provedor de autenticação externa:

![Gerenciar seus logons externos exibir listando o Facebook](accconfirm/_static/fb.png)

As duas contas foram combinadas. Você pode entrar com qualquer uma das contas. Talvez você queira que os usuários adicionem contas locais caso o serviço de autenticação de logon social esteja inativo ou, provavelmente, tenha perdido o acesso à sua conta social.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Habilitar confirmação de conta depois que um site tiver usuários

Habilitar a confirmação de conta em um site com usuários bloqueia todos os usuários existentes. Os usuários existentes são bloqueados porque suas contas não são confirmadas. Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:

* Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmados.
* Confirme os usuários existentes. Por exemplo, enviar emails por lote com links de confirmação.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Pré-requisitos

[SDK do .NET Core 2,2 ou posterior](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a>Criar um aplicativo Web e ScaffoldIdentity

Execute os comandos a seguir para criar um aplicativo Web com autenticação.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> o estiver configurado no `Startup.ConfigureServices` , a configuração de [ `[StringLength]` atributo](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) poderá ser necessária para a `Password` propriedade em Identity páginas com Scaffold. Uma `InputModel` `Password` propriedade é encontrada no `Areas/Identity/Pages/Account/Register.cshtml.cs` arquivo após scaffolding Identity .

## <a name="test-new-user-registration"></a>Testar novo registro de usuário

Execute o aplicativo, selecione o link **registrar** e registre um usuário. Neste ponto, a única validação no email é com o [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo. Depois de enviar o registro, você está conectado ao aplicativo. Posteriormente no tutorial, o código será atualizado para que novos usuários não possam entrar até que seu email seja validado.

[!INCLUDE[](~/includes/view-identity-db.md)]

Observe que o campo da tabela `EmailConfirmed` é `False` .

Você pode querer usar esse email novamente na próxima etapa quando o aplicativo enviar um email de confirmação. Clique com o botão direito do mouse na linha e selecione **excluir**. A exclusão do alias de email torna mais fácil as etapas a seguir.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Exigir confirmação de email

É uma prática recomendada confirmar o email de um novo registro de usuário. A confirmação por email ajuda a verificar se eles não estão representando outra pessoa (ou seja, se eles não se registraram no email de outra pessoa). Suponha que você tenha um fórum de discussão e queira impedir " yli@example.com " de se registrar como " nolivetto@contoso.com ". Sem confirmação por email, " nolivetto@contoso.com " pode receber emails indesejados do seu aplicativo. Suponha que o usuário se registrou acidentalmente como " ylo@example.com " e não tenha notado a grafia incorreta de "Yli". Eles não poderão usar a recuperação de senha porque o aplicativo não tem seu email correto. A confirmação por email fornece proteção limitada de bots. A confirmação por email não fornece proteção contra usuários mal-intencionados com muitas contas de email.

Em geral, você deseja impedir que novos usuários enviem dados para seu site antes de terem um email confirmado.

Atualize `Startup.ConfigureServices` para exigir um email confirmado:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;`impede que os usuários registrados façam logon até que seu email seja confirmado.

### <a name="configure-email-provider"></a>Configurar provedor de email

Neste tutorial, [SendGrid](https://sendgrid.com) é usado para enviar email. Você precisa de uma conta e chave do SendGrid para enviar email. Você pode usar outros provedores de email. O ASP.NET Core 2. x inclui `System.Net.Mail` , que permite enviar emails do seu aplicativo. Recomendamos que você use o SendGrid ou outro serviço de email para enviar email. O SMTP é difícil de proteger e configurar corretamente.

Crie uma classe para buscar a chave de email seguro. Para este exemplo, crie *Services/AuthMessageSenderOptions. cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Configurar segredos de usuário do SendGrid

Defina `SendGridUser` e `SendGridKey` com a [ferramenta Secret-Manager](xref:security/app-secrets). Por exemplo:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

No Windows, o Gerenciador de segredo armazena pares de chaves/valores em um *secrets.jsno* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` diretório.

O conteúdo do *secrets.jsno* arquivo não está criptografado. A marcação a seguir mostra o *secrets.jsno* arquivo. O `SendGridKey` valor foi removido.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalar o SendGrid

Este tutorial mostra como adicionar notificações por email por meio do [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando o SMTP e outros mecanismos.

Instale o `SendGrid` pacote NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

No console do Gerenciador de pacotes, digite o seguinte comando:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

No console do, digite o seguinte comando:

```dotnetcli
dotnet add package SendGrid
```

---

Consulte Introdução ao [SendGrid gratuitamente](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.

### <a name="implement-iemailsender"></a>Implementar IEmailSender

Para implementar `IEmailSender` , crie os *Serviços/EmailSender. cs* com um código semelhante ao seguinte:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Configurar a inicialização para o email de suporte

Adicione o seguinte código ao `ConfigureServices` método no arquivo *Startup.cs* :

* Adicionar `EmailSender` como um serviço transitório.
* Registre a `AuthMessageSenderOptions` instância de configuração.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Habilitar confirmação de conta e recuperação de senha

O modelo tem o código para confirmação de conta e recuperação de senha. Localize o `OnPostAsync` método em *áreas/ Identity /pages/Account/Register.cshtml.cs*.

Impedir que usuários registrados recentemente façam logon automaticamente comentando a seguinte linha:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

O método Complete é mostrado com a linha alterada realçada:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Registrar, confirmar email e Redefinir senha

Execute o aplicativo Web e teste a confirmação da conta e o fluxo de recuperação de senha.

* Executar o aplicativo e registrar um novo usuário
* Verifique seu email para o link de confirmação da conta. Consulte [depurar email](#debug) se você não receber o email.
* Clique no link para confirmar seu email.
* Entre com seu email e senha.
* Saia.

### <a name="view-the-manage-page"></a>Exibir a página Gerenciar

Selecione seu nome de usuário no navegador: ![ janela do navegador com nome de usuário](accconfirm/_static/un.png)

A página Gerenciar é exibida com a guia **perfil** selecionada. O **email** mostra uma caixa de seleção indicando que o email foi confirmado.

### <a name="test-password-reset"></a>Testar redefinição de senha

* Se você estiver conectado, selecione **logout**.
* Selecione o link **fazer logon** e selecione o link **esqueceu sua senha?** .
* Insira o email que você usou para registrar a conta.
* Um email com um link para redefinir sua senha é enviado. Verifique seu email e clique no link para redefinir sua senha. Depois que a senha for redefinida com êxito, você poderá entrar com seu email e nova senha.

## <a name="change-email-and-activity-timeout"></a>Alterar o tempo limite de email e atividade

O tempo limite de inatividade padrão é de 14 dias. O código a seguir define o tempo limite de inatividade como 5 dias:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Alterar todas as vidas de token de proteção de dados

O código a seguir altera todos os tokens de proteção de dados período de tempo limite para 3 horas:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Os Identity tokens de usuário internos (consulte [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) têm um [tempo limite de um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Alterar o ciclo de vida do token de email

A vida útil do token padrão dos [ Identity tokens de usuário](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é de [um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). Esta seção mostra como alterar o ciclo de vida do token de email.

Adicione um [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Adicione o provedor personalizado ao contêiner de serviço:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Reenviar confirmação de email

Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Depurar email

Se você não puder obter emails funcionando:

* Defina um ponto de interrupção em `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.
* Crie um [aplicativo de console para enviar emails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando código semelhante para `EmailSender.Execute` .
* Examine a página [atividade de email](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Verifique sua pasta de spam.
* Experimente outro alias de email em um provedor de email diferente (Microsoft, Yahoo, Gmail, etc.)
* Tente enviar para contas de email diferentes.

**Uma prática recomendada de segurança** é **não** usar segredos de produção em teste e desenvolvimento. Se você publicar o aplicativo no Azure, poderá definir os segredos do SendGrid como configurações do aplicativo no portal do aplicativo Web do Azure. O sistema de configuração é configurado para ler chaves de variáveis de ambiente.

## <a name="combine-social-and-local-login-accounts"></a>Combinar contas de logon sociais e locais

Para concluir esta seção, primeiro você deve habilitar um provedor de autenticação externa. Consulte [Facebook, Google e autenticação de provedor externo](xref:security/authentication/social/index).

Você pode combinar contas locais e sociais clicando no link de email. Na sequência a seguir, " RickAndMSFT@gmail.com " é criado primeiro como um logon local; no entanto, você pode criar a conta como um logon social primeiro e, em seguida, adicionar um logon local.

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

Clique no link **gerenciar** . Observe o 0 externo (logons sociais) associado a essa conta.

![Gerenciar modo de exibição](accconfirm/_static/manage.png)

Clique no link para outro serviço de logon e aceite as solicitações do aplicativo. Na imagem a seguir, o Facebook é o provedor de autenticação externa:

![Gerenciar seus logons externos exibir listando o Facebook](accconfirm/_static/fb.png)

As duas contas foram combinadas. Você pode entrar com qualquer uma das contas. Talvez você queira que os usuários adicionem contas locais caso o serviço de autenticação de logon social esteja inativo ou, provavelmente, tenha perdido o acesso à sua conta social.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Habilitar confirmação de conta depois que um site tiver usuários

Habilitar a confirmação de conta em um site com usuários bloqueia todos os usuários existentes. Os usuários existentes são bloqueados porque suas contas não são confirmadas. Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:

* Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmados.
* Confirme os usuários existentes. Por exemplo, enviar emails por lote com links de confirmação.

::: moniker-end
