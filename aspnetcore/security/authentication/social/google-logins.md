---
title: Configuração de logon externo do Google no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação do usuário da conta do Google em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
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
uid: security/authentication/google-logins
ms.openlocfilehash: a7a5260a2446ac3f3be00755ef051e56080a7485
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634287"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Configuração de logon externo do Google no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial mostra como permitir que os usuários entrem com sua conta do Google usando o projeto ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Criar um projeto do console do Google API e uma ID do cliente

* Instale [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).
* Navegue até [integrando o logon do Google ao seu aplicativo Web](https://developers.google.com/identity/sign-in/web/sign-in) e selecione **configurar um projeto**.
* Na caixa de diálogo **configurar seu cliente OAuth** , selecione **servidor Web**.
* Na caixa de entrada de texto **URIs de redirecionamento autorizados** , defina o URI de redirecionamento. Por exemplo, `https://localhost:44312/signin-google`
* Salve a **ID do cliente** e o **segredo do cliente**.
* Ao implantar o site, registre a nova URL pública do console do **Google**.

## <a name="store-the-google-client-id-and-secret"></a>Armazenar a ID e o segredo do cliente do Google

Armazene configurações confidenciais, como os valores de ID do cliente do Google e segredo com o [Gerenciador de segredo](xref:security/app-secrets). Para este exemplo, use as seguintes etapas:

1. Inicialize o projeto para o armazenamento secreto de acordo com as instruções em [habilitar armazenamento secreto](xref:security/app-secrets#enable-secret-storage).
1. Armazene as configurações confidenciais no repositório de segredo local com as chaves secretas `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Você pode gerenciar suas credenciais de API e uso no [console de API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Configurar a autenticação do Google

Adicione o serviço do Google a `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Entrar com o Google

* Execute o aplicativo e clique em **fazer logon**. É exibida uma opção para entrar com o Google.
* Clique no botão **Google** , que redireciona para o Google para autenticação.
* Depois de inserir suas credenciais do Google, você será Redirecionado de volta para o site da Web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> referência da API para obter mais informações sobre as opções de configuração com suporte pela autenticação do Google. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="change-the-default-callback-uri"></a>Alterar o URI de retorno de chamada padrão

O segmento URI `/signin-google` é definido como o retorno de chamada padrão do provedor de autenticação do Google. Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Google por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [googleoptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .

## <a name="troubleshooting"></a>Solução de problemas

* Se a entrada não funcionar e você não estiver recebendo erros, mude para o modo de desenvolvimento para facilitar a depuração do problema.
* Se Identity não estiver configurado chamando `services.AddIdentity` em `ConfigureServices` , tentando autenticar resultados em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* . Selecione **aplicar migrações** para criar o banco de dados e atualize a página para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Google. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).
* Depois de publicar o aplicativo no Azure, redefina o `ClientSecret` no console de API do Google.
* Defina `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` como configurações de aplicativo no portal do Azure. O sistema de configuração é configurado para ler chaves de variáveis de ambiente.
