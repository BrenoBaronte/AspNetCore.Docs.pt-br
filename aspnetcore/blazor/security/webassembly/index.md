---
title: ASP.NET Core seguro Blazor WebAssembly
author: guardrex
description: Saiba como proteger Blazor aplicativos WebAssemlby como spas (aplicativos de página única).
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: 0b555ad7befe882c4ffd06e2505a9edc1263eee2
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057077"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core seguro Blazor WebAssembly

Por [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor WebAssembly os aplicativos são protegidos da mesma maneira que os aplicativos de página única (SPAs). Há várias abordagens para autenticar usuários no SPAs, mas a abordagem mais comum e abrangente é usar uma implementação baseada no [protocolo OAuth 2,0](https://oauth.net/), como o [OPENID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteca de autenticação

Blazor WebAssembly dá suporte à autenticação e à autorização de aplicativos usando o OIDC por meio da [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) biblioteca. A biblioteca fornece um conjunto de primitivos para autenticação direta contra back-ends ASP.NET Core. A biblioteca integra-se ASP.NET Core Identity com o suporte à autorização de API criado com base no [ Identity servidor](https://identityserver.io/). A biblioteca pode se autenticar em qualquer provedor de terceiros Identity (IP) que ofereça suporte a OIDC, que são chamadas de provedores de OpenID (op).

O suporte de autenticação no Blazor WebAssembly é criado sobre a `oidc-client.js` biblioteca, que é usada para lidar com os detalhes do protocolo de autenticação subjacente.

Existem outras opções para autenticar SPAs, como o uso de SameSite cookie s. No entanto, o design de engenharia do Blazor WebAssembly é liquidado em OAuth e OIDC como a melhor opção para autenticação em Blazor WebAssembly aplicativos. [A autenticação baseada em token](xref:security/anti-request-forgery#token-based-authentication) baseada em [tokens Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) foi escolhida para [ cookie autenticação baseada](xref:security/anti-request-forgery#cookie-based-authentication) em excesso por motivos funcionais e de segurança:

* O uso de um protocolo baseado em token oferece uma área de superfície de ataque menor, pois os tokens não são enviados em todas as solicitações.
* Os pontos de extremidade do servidor não exigem proteção contra [falsificação de solicitação entre sites (CSRF)](xref:security/anti-request-forgery) porque os tokens são enviados explicitamente. Isso permite que você hospede Blazor WebAssembly aplicativos junto a aplicativos MVC ou Razor páginas.
* Tokens têm permissões mais estreitas do que cookie s. Por exemplo, os tokens não podem ser usados para gerenciar a conta de usuário ou alterar a senha de um usuário, a menos que essa funcionalidade seja explicitamente implementada.
* Os tokens têm um tempo de vida curto, uma hora por padrão, o que limita a janela de ataque. Os tokens também podem ser revogados a qualquer momento.
* As JWTs independentes oferecem garantias ao cliente e ao servidor sobre o processo de autenticação. Por exemplo, um cliente tem os meios para detectar e validar que os tokens recebidos são legítimos e foram emitidos como parte de um determinado processo de autenticação. Se um terceiro tentar alternar um token no meio do processo de autenticação, o cliente poderá detectar o token comutado e evitar usá-lo.
* Tokens com OAuth e OIDC não dependem do agente do usuário se comportarem corretamente para garantir que o aplicativo seja seguro.
* Protocolos baseados em token, como OAuth e OIDC, permitem autenticar e autorizar aplicativos hospedados e autônomos com o mesmo conjunto de características de segurança.

## <a name="authentication-process-with-oidc"></a>Processo de autenticação com OIDC

A [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) biblioteca oferece vários primitivos para implementar a autenticação e a autorização usando o OIDC. Em termos gerais, a autenticação funciona da seguinte maneira:

* Quando um usuário anônimo seleciona o botão de logon ou solicita uma página com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo aplicado, o usuário é redirecionado para a página de logon do aplicativo ( `/authentication/login` ).
* Na página de logon, a biblioteca de autenticação se prepara para um redirecionamento para o ponto de extremidade de autorização. O ponto de extremidade de autorização está fora do Blazor WebAssembly aplicativo e pode ser hospedado em uma origem separada. O ponto de extremidade é responsável por determinar se o usuário está autenticado e para emitir um ou mais tokens em resposta. A biblioteca de autenticação fornece um retorno de chamada de logon para receber a resposta de autenticação.
  * Se o usuário não for autenticado, o usuário será redirecionado para o sistema de autenticação subjacente, que geralmente é ASP.NET Core Identity .
  * Se o usuário já foi autenticado, o ponto de extremidade de autorização gera os tokens apropriados e redireciona o navegador de volta para o ponto de extremidade de retorno de chamada de logon ( `/authentication/login-callback` ).
* Quando o Blazor WebAssembly aplicativo carrega o ponto de extremidade de retorno de chamada de logon ( `/authentication/login-callback` ), a resposta de autenticação é processada.
  * Se o processo de autenticação for concluído com êxito, o usuário será autenticado e, opcionalmente, enviado de volta para a URL protegida original solicitada pelo usuário.
  * Se o processo de autenticação falhar por algum motivo, o usuário será enviado à página de falha de logon ( `/authentication/login-failed` ) e um erro será exibido.

## <a name="authentication-component"></a>componente `Authentication`

O `Authentication` componente ( `Pages/Authentication.razor` ) lida com operações de autenticação remota e permite que o aplicativo:

* Configurar rotas de aplicativo para Estados de autenticação.
* Defina o conteúdo da interface do usuário para Estados de autenticação.
* Gerenciar o estado de autenticação.

As ações de autenticação, como registrar ou assinar um usuário, são passadas para o Blazor componente da estrutura <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> , que persiste e controla o estado nas operações de autenticação.

Para obter mais informações e exemplos, consulte <xref:blazor/security/webassembly/additional-scenarios>.

## <a name="authorization"></a>Autorização

Em Blazor WebAssembly aplicativos, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**

## <a name="require-authorization-for-the-entire-app"></a>Exigir autorização para o aplicativo inteiro

Aplique o [ `[Authorize]` atributo](xref:blazor/security/index#authorize-attribute) ([documentação da API](xref:System.Web.Mvc.AuthorizeAttribute)) a cada Razor componente do aplicativo usando uma das seguintes abordagens:

* Use a [`@attribute`](xref:mvc/views/razor#attribute) diretiva no `_Imports.razor` arquivo:

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* Adicione o atributo a cada Razor componente na `Pages` pasta.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> **Não** há suporte para a definição de um para uma política com.

## <a name="refresh-tokens"></a>Tokens de atualização

Os tokens de atualização não podem ser protegidos no lado do cliente em Blazor WebAssembly aplicativos. Portanto, os tokens de atualização não devem ser enviados para o aplicativo para uso direto.

Os tokens de atualização podem ser mantidos e usados pelo aplicativo do lado do servidor em uma solução hospedada Blazor WebAssembly para acessar APIs de terceiros. Para obter mais informações, consulte <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="establish-claims-for-users"></a>Estabelecer declarações para usuários

Aplicativos geralmente exigem declarações para usuários com base em uma chamada à API da Web para um servidor. Por exemplo, as declarações são usadas frequentemente para [estabelecer a autorização](xref:blazor/security/index#authorization) em um aplicativo. Nesses cenários, o aplicativo solicita um token de acesso para acessar o serviço e usa o token para obter os dados do usuário para as declarações. Para obter exemplos, consulte os seguintes recursos:

* [Cenários adicionais: personalizar o usuário](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a>Serviço de Azure App no Linux com o Identity servidor

Especifique o emissor explicitamente ao implantar no serviço de Azure App no Linux com o Identity servidor. Para obter mais informações, consulte <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="windows-authentication"></a>Autenticação do Windows

Não é recomendável usar a autenticação do Windows com Blazor Webassembly ou com qualquer outra estrutura de spa. É recomendável usar protocolos baseados em token em vez de autenticação do Windows, como OIDC com Serviços de Federação do Active Directory (AD FS) (ADFS).

Se a autenticação do Windows for usada com o Blazor Webassembly ou com qualquer outra estrutura de spa, serão necessárias medidas adicionais para proteger o aplicativo de tokens CSRF (falsificação de solicitação entre sites). As mesmas preocupações que se aplicam a cookie s se aplicam à autenticação do Windows com a adição de que a autenticação do Windows não oferece nenhum mecanismo para impedir o compartilhamento do contexto de autenticação entre origens. Os aplicativos que usam a autenticação do Windows sem proteção adicional do CSRF devem, pelo menos, ser restritos à intranet de uma organização e não ser usados na Internet.

Para obter informações, consulte <xref:security/anti-request-forgery> .

## <a name="implementation-guidance"></a>Diretrizes de implementação

Os artigos nesta *visão geral* fornecem informações sobre como autenticar usuários em Blazor WebAssembly aplicativos em relação a provedores específicos.

Aplicativos autônomos Blazor WebAssembly :

* [Diretrizes gerais para provedores de OIDC e a biblioteca de autenticação Webassembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Contas da Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD (Azure Active Directory)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

Aplicativos hospedados Blazor WebAssembly :

* [AAD (Azure Active Directory)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [Identity Servidor](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

As diretrizes de configuração adicionais são encontradas nos seguintes artigos:

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Para obter mais diretrizes de configuração, consulte <xref:blazor/security/webassembly/additional-scenarios> .

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:host-and-deploy/proxy-load-balancer>: Inclui diretrizes sobre:
  * Usando o middleware de cabeçalhos encaminhados para preservar as informações do esquema HTTPS entre servidores proxy e redes internas.
  * Cenários adicionais e casos de uso, incluindo configuração de esquema manual, alterações de caminho de solicitação para roteamento de solicitação correto e encaminhamento do esquema de solicitação para proxies inversos do Linux e não do IIS.
