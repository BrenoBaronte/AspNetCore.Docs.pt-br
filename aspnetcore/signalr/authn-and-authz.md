---
title: Autenticação e autorização no SignalR do ASP.NET Core
author: rachelappel
description: Saiba como usar a autenticação e autorização no SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 06/29/2018
uid: signalr/authn-and-authz
ms.openlocfilehash: 32e5fcf2fd3f888e0e131fa47bd9a74eede3c26d
ms.sourcegitcommit: 32626efaa7316c9b283c96be6516e637d548c5e5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39028458"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Autenticação e autorização no SignalR do ASP.NET Core

Por [Andrew Stanton-Nurse](https://twitter.com/anurse)

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(como fazer o download)](xref:tutorials/index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Autenticar usuários que se conectam a um hub SignalR

O SignalR pode ser usado com [autenticação do ASP.NET Core](xref:security/authentication/index) para associar um usuário a cada conexão. Em um hub, os dados de autenticação podem ser acessados do [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) propriedade. A autenticação permite que o hub para chamar métodos em todas as conexões associadas a um usuário (consulte [gerenciar usuários e grupos no SignalR](xref:signalr/groups) para obter mais informações). Várias conexões podem estar associadas um único usuário.

### <a name="cookie-authentication"></a>Autenticação de cookie

Em um aplicativo baseado em navegador, a autenticação de cookie permite que suas credenciais de usuário existentes fluir automaticamente para conexões do SignalR. Ao usar o cliente de navegador, nenhuma configuração adicional é necessária. Se o usuário está conectado ao seu aplicativo, a conexão do SignalR herda automaticamente essa autenticação.

Autenticação de cookie não é recomendada, a menos que o aplicativo precisa apenas para autenticar usuários de cliente de navegador. Ao usar o [cliente .NET](xref:signalr/dotnet-client), o `Cookies` propriedade pode ser configurada no `.WithUrl` chamada para fornecer um cookie. No entanto, usando a autenticação de cookie do cliente .NET requer que o aplicativo para fornecer uma API para trocar dados de autenticação para um cookie.

### <a name="bearer-token-authentication"></a>Autenticação de token de portador

Autenticação de token de portador é a abordagem recomendada ao usar os clientes que não seja o cliente do navegador. Nessa abordagem, o cliente fornece um token de acesso que o servidor valida e usa para identificar o usuário. Os detalhes de autenticação de token de portador estão além do escopo deste documento. No servidor de autenticação de token de portador é configurada usando o [middleware de portador de JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

No cliente JavaScript, o token pode ser fornecido usando o [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) opção.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

No cliente do .NET, há um semelhante [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) propriedade que pode ser usada para configurar o token:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => _myAccessToken;
    })
    .Build();
```

> [!NOTE]
> A função de token de acesso que você fornecer é chamada antes de **cada** solicitação HTTP feita pelo SignalR. Se você precisa renovar o token para manter a conexão ativa (porque ele pode expirar durante a conexão), faça isso dentro dessa função e retornar o token atualizado.

APIs da web padrão, os tokens de portador são enviados em um cabeçalho HTTP. No entanto, o SignalR é não é possível definir esses cabeçalhos em navegadores quando usando alguns transportes. Ao usar WebSockets e eventos do Server-Sent, o token é transmitido como um parâmetro de cadeia de caracteres de consulta. Para suportar isso no servidor, a configuração adicional é necessária:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?range=33-34,42-80,90)]

### <a name="windows-authentication"></a>Autenticação do Windows

Se [autenticação do Windows](xref:security/authentication/windowsauth) é configurado em seu aplicativo, o SignalR pode usar essa identidade para proteger hubs. No entanto, para enviar mensagens para usuários individuais, você precisará adicionar um provedor de ID de usuário personalizado. Isso ocorre porque o sistema de autenticação do Windows não fornece a declaração "Identificador de nome" que usa o SignalR para determinar o nome de usuário.

Adicione uma nova classe que implementa `IUserIdProvider` e recuperar uma das declarações de usuário a ser usado como o identificador. Por exemplo, para usar a declaração de "Nome" (que é o nome de usuário do Windows na forma `[Domain]\[Username]`), crie a seguinte classe:

```csharp
public class NameUserIdProvider : IUserIdProvider
{
    public string GetUserId(HubConnectionContext connection)
    {
        return connection.User?.FindFirst(ClaimTypes.Name)?.Value;
    }
}
```

Em vez de `ClaimTypes.Name`, você pode usar qualquer valor entre o `User` (como o identificador do SID do Windows, etc.).

> [!NOTE]
> O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema. Caso contrário, uma mensagem para um usuário poderia acabar indo para um usuário diferente.

Registrar esse componente em seu `Startup.ConfigureServices` método **depois** a chamada para `.AddSignalR`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorizar usuários para acesso hubs e métodos de hub

Por padrão, todos os métodos em um hub podem ser chamados por um usuário não autenticado. Para exigir autenticação, se aplicam a [autorizar](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) de atributo para o hub:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Você pode usar os argumentos de construtor e propriedades do `[Authorize]` atributo para restringir o acesso somente para usuários específicos de correspondência [políticas de autorização](xref:security/authorization/policies). Por exemplo, se você tiver uma política de autorização personalizada chamada `MyAuthorizationPolicy` pode garantir que somente os usuários dessa política de correspondência podem acessar o hub usando o seguinte código:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

Métodos de hub individuais podem ter o `[Authorize]` atributo aplicado também. Se o usuário atual não corresponder a política aplicada ao método, um erro será retornado ao chamador:

```csharp
[Authorize]
public class ChatHub: Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```
