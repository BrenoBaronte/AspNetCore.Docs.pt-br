---
title: ASP.NET Core Blazor WebAssembly com grupos e funções de Azure Active Directory
author: guardrex
description: Saiba como configurar o Blazor WebAssembly para usar grupos de Azure Active Directory e funções.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 8d18a8f9282e83c3b3ef5b9c7c6651c9b525a21f
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751577"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Grupos do Azure Active Directory (AAD), funções de administrador e funções de aplicativo

De [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)

> [!NOTE]
> Este artigo se aplica a Blazor ASP.NET Core aplicativos versão 3,1 com o Microsoft Identity 1,0 e será atualizado para 5,0 com Identity 2,0 em breve. Para obter mais informações, consulte o seguinte problema do GitHub e solicitação de pull. A guia **arquivos alterados** da solicitação pull contém o texto de rascunho e exemplos para as atualizações do artigo. Após a revisão e as atualizações finais, a solicitação de pull será mesclada no conjunto de documentação ao vivo.
>
> * Problema: [ Blazor WASM com grupos e funções do AAD (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)
> * Solicitação de pull: [ Blazor tópico sobre grupos e funções do AAD 5,0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)

Azure Active Directory (AAD) fornece várias abordagens de autorização que podem ser combinadas com ASP.NET Core Identity :

* Grupos
  * Segurança
  * Microsoft 365
  * Distribuição
* Funções
  * Funções de administrador do AAD
  * Funções de aplicativo

As diretrizes neste artigo se aplicam aos Blazor WebAssembly cenários de implantação do AAD descritos nos tópicos a seguir:

* [Aplicativo autônomo com contas Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Aplicativo autônomo com o AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Aplicativo hospedado com o AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

As diretrizes do artigo fornecem instruções para aplicativos cliente e servidor:

* **Cliente**: aplicativos autônomos Blazor WebAssembly ou o *`Client`* aplicativo de uma Blazor solução hospedada.
* **Servidor**: aplicativos de API Web/API de servidor ASP.NET Core autônomo ou o *`Server`* aplicativo de uma solução hospedada Blazor .

## <a name="scopes"></a>Escopos

Para permitir chamadas de [API de Microsoft Graph](/graph/use-the-api) para o perfil do usuário, atribuição de função e dados de associação de grupo, o **cliente** é configurado com a `https://graph.microsoft.com/User.Read` permissão () [API do Graph (escopo)](/graph/permissions-reference) no portal do Azure.

Um aplicativo de **servidor** que chama API do Graph para dados de associação de grupo e função é fornecido `GroupMember.Read.All` ( `https://graph.microsoft.com/GroupMember.Read.All` ) [API do Graph permissão (escopo)](/graph/permissions-reference) no portal do Azure.

Esses escopos são necessários além dos escopos necessários nos cenários de implantação do AAD descritos pelos tópicos listados na primeira seção deste artigo.

> [!NOTE]
> As palavras "Permission" e "Scope" são usadas de forma intercambiável no portal do Azure e em vários conjuntos de documentação da Microsoft e externos. Este artigo usa a palavra "Scope" para as permissões atribuídas a um aplicativo no portal do Azure.

## <a name="group-membership-claims-attribute"></a>Atributo de declarações de associação de grupo

No manifesto do aplicativo no portal do Azure para aplicativos **cliente** e **servidor** , defina o [ `groupMembershipClaims` atributo](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) como `All` . Um valor `All` resulta na obtenção de todos os grupos de segurança, grupos de distribuição e funções dos quais o usuário conectado é membro.

1. Abra o registro de portal do Azure do aplicativo.
1. Selecione **gerenciar**  >  **manifesto** na barra lateral.
1. Localize o `groupMembershipClaims` atributo.
1. Defina o valor como `All`.
1. Selecione o botão **Salvar**.

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>Conta de usuário personalizada

Atribua usuários a grupos de segurança do AAD e funções de administrador do AAD no portal do Azure.

Os exemplos neste artigo:

* Suponha que um usuário seja atribuído à função de *administrador de cobrança* do AAD no locatário portal do Azure AAD para autorização para acessar os dados da API do servidor.
* Use [políticas de autorização](xref:security/authorization/policies) para controlar o acesso nos aplicativos **cliente** e **servidor** .

No aplicativo **cliente** , estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir propriedades para:

* `Roles`: Matriz de funções de aplicativo do AAD (abordada na seção [funções de aplicativo](#app-roles) )
* `Wids`: Funções de administrador do AAD em [declarações de IDs conhecidas ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims)
* `Oid`: [Declaração de identificador de objeto imutável ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (identifica exclusivamente um usuário dentro e entre locatários)

Atribua uma matriz vazia a cada propriedade de matriz para que a verificação de `null` não seja necessária quando essas propriedades forem usadas em `foreach` loops.

`CustomUserAccount.cs`:

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

Adicione uma referência de pacote ao arquivo de projeto do aplicativo **cliente** para o [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .

Adicione as classes e a configuração do utilitário SDK do Graph na seção *SDK do Graph* do <xref:blazor/security/webassembly/graph-api#graph-sdk> artigo. Na `GraphClientExtensions` classe, especifique o `User.Read` escopo para o token de acesso no `AuthenticateRequestAsync` método:

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

Adicione a seguinte fábrica de conta de usuário personalizada ao aplicativo **cliente** . A fábrica de usuário personalizada é usada para estabelecer:

* Declarações de função de aplicativo ( `appRole` ) (cobertas na seção [funções de aplicativo](#app-roles) )
* Declarações de função de administrador do AAD ( `directoryRole` )
* Uma declaração de dados de perfil de usuário de exemplo para o número de telefone celular do usuário ( `mobilePhone` )
* Declarações de grupo do AAD ( `directoryGroup` )

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

O código anterior não inclui associações transitivas. Se o aplicativo exigir declarações de associação de grupo diretas e transitivas, substitua a `MemberOf` Propriedade ( `IUserMemberOfCollectionWithReferencesRequestBuilder` ) por `TransitiveMemberOf` ( `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` ).

O código anterior ignora as declarações de associação de grupo ( `groups` ) que são funções de administrador do AAD ( `#microsoft.graph.directoryRole` tipo) porque os valores de GUID retornados pela Identity plataforma Microsoft 2,0 são **IDs de entidade** da função de administrador do AAD e não IDs de modelo de [**função**](/azure/active-directory/roles/permissions-reference#role-template-ids). As IDs de entidade não são estáveis entre locatários no Microsoft Identity Platform 2,0 e não devem ser usadas para criar políticas de autorização para usuários em aplicativos. Sempre use **IDs de modelo de função** para funções de administrador do AAD **fornecidas por `wids` declarações**.

No `Program.Main` aplicativo **cliente** , configure a autenticação MSAL para usar o alocador de conta de usuário personalizado.

`Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a>Configuração de autorização

No aplicativo **cliente** , crie uma [política](xref:security/authorization/policies) para cada [função de aplicativo](#app-roles), função de administrador do AAD ou grupo de segurança no `Program.Main` . O exemplo a seguir cria uma política para a função de *administrador de cobrança* do AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Para obter a lista completa de IDs para funções de administrador do AAD, consulte [IDs de modelo de função](/azure/active-directory/roles/permissions-reference#role-template-ids) na documentação do Azure. Para obter mais informações sobre políticas de autorização, consulte <xref:security/authorization/policies> .

Nos exemplos a seguir, o aplicativo **cliente** usa a política anterior para autorizar o usuário.

O [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funciona com a política:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

O acesso a um componente inteiro pode ser baseado na política usando uma [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Se o usuário não estiver conectado, ele será redirecionado para a página de entrada do AAD e, em seguida, de volta para o componente depois de entrar.

Uma verificação de política também pode ser [executada no código com lógica de procedimento](xref:blazor/security/index#procedural-logic).

`Pages/CheckPolicy.razor`:

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a>Autorizar acesso de API de servidor/API Web

Um aplicativo de API de **servidor** pode autorizar os usuários a acessar pontos de extremidade de API segura com [políticas de autorização](xref:security/authorization/policies) para grupos de segurança, funções de administrador do AAD e funções de aplicativo quando um token de acesso contém `groups` `wids` declarações, e `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` . O exemplo a seguir cria uma política para a função de *administrador de cobrança* do AAD no `Startup.ConfigureServices` usando as `wids` declarações (IDs conhecidas do modelo de função/IDs):

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Para obter a lista completa de IDs para funções de administrador do AAD, consulte [IDs de modelo de função](/azure/active-directory/roles/permissions-reference#role-template-ids) na documentação do Azure. Para obter mais informações sobre políticas de autorização, consulte <xref:security/authorization/policies> .

O acesso a um controlador no aplicativo do **servidor** pode ser baseado no uso de um [ `[Authorize]` atributo](xref:security/authorization/simple) com o nome da política (documentação da API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).

O exemplo a seguir limita o acesso a dados de cobrança do `BillingDataController` para administradores de cobrança do Azure com um nome de política `BillingAdministrator` :

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

Para obter mais informações, consulte <xref:security/authorization/policies>.

## <a name="app-roles"></a>Funções de aplicativo

Para configurar o aplicativo no portal do Azure para fornecer declarações de associação de funções de aplicativo, consulte [como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) na documentação do Azure.

O exemplo a seguir pressupõe que os aplicativos **cliente** e **servidor** estejam configurados com duas funções e que as funções sejam atribuídas a um usuário de teste:

* `admin`
* `developer`

> [!NOTE]
> Ao desenvolver um Blazor WebAssembly aplicativo hospedado ou um par cliente-servidor de aplicativos autônomos (um Blazor WebAssembly aplicativo autônomo e um aplicativo de API Web/API de servidor ASP.NET Core autônomo), a `appRoles` propriedade de manifesto do cliente e do servidor portal do Azure registros de aplicativo deve incluir as mesmas funções configuradas. Depois de estabelecer as funções no manifesto do aplicativo cliente, copie-as em sua totalidade para o manifesto do aplicativo do servidor. Se você não espelhar o manifesto `appRoles` entre os registros do aplicativo cliente e do servidor, as declarações de função não serão estabelecidas para usuários autenticados da API da Web/API do servidor, mesmo que seu token de acesso tenha as declarações de funções corretas.

> [!NOTE]
> Embora não seja possível atribuir funções a grupos sem uma conta de Azure AD Premium, você pode atribuir funções a usuários e receber uma declaração de funções para usuários com uma conta padrão do Azure. As diretrizes nesta seção não exigem uma conta do AAD Premium.
>
> Várias funções são atribuídas no portal do Azure ao **_adicionar novamente um usuário_** para cada atribuição de função adicional.

O `CustomAccountFactory` mostrado na seção [conta de usuário personalizada](#custom-user-account) é configurado para agir em uma `roles` declaração com um valor de matriz JSON. Adicione e registre o `CustomAccountFactory` no aplicativo **cliente** , conforme mostrado na seção [conta de usuário personalizada](#custom-user-account) . Não é necessário fornecer código para remover a `roles` declaração original porque ela é automaticamente removida pela estrutura.

Em `Program.Main` um aplicativo **cliente** , especifique a declaração denominada " `appRole` " como a declaração de função para <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> verificações:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> Se você preferir usar a `directoryRoles` declaração (adicionar funções de administrador), atribua " `directoryRoles` " ao <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .

Em `Startup.ConfigureServices` de um aplicativo de **servidor** , especifique a declaração denominada " `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` " como a declaração de função para <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> verificações:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> Se você preferir usar a `wids` declaração (adicionar funções de administrador), atribua " `wids` " ao <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .

As abordagens de autorização de componente são funcionais neste ponto. Qualquer um dos mecanismos de autorização nos componentes do aplicativo **cliente** pode usar a `admin` função para autorizar o usuário:

* [`AuthorizeView` componente](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [Lógica de procedimento](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

Há suporte para vários testes de função:

* Exigir **que o usuário esteja na** `admin` função **ou** `developer` com o `AuthorizeView` componente:

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* Exigir que o usuário **esteja nas** `admin`  `developer` funções e com o `AuthorizeView` componente:

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* Exija **que o usuário esteja na** `admin` função **ou** `developer` com o `[Authorize]` atributo:

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* Exija que o usuário **esteja nas** `admin`  `developer` funções e com o `[Authorize]` atributo:

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* Exigir **que o usuário esteja na** `admin` função **ou** `developer` com o código de procedimento:

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* Exija que o usuário **esteja nas** `admin` funções **e** `developer` com código de procedimento alterando o [condicional ou ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) para uma [condicional e ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) no exemplo anterior:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

Qualquer um dos mecanismos de autorização nos controladores do aplicativo de **servidor** pode usar a `admin` função para autorizar o usuário:

* [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [Lógica de procedimento](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

Há suporte para vários testes de função:

* Exija **que o usuário esteja na** `admin` função **ou** `developer` com o `[Authorize]` atributo:

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* Exija que o usuário **esteja nas** `admin`  `developer` funções e com o `[Authorize]` atributo:

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* Exigir **que o usuário esteja na** `admin` função **ou** `developer` com o código de procedimento:

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* Exija que o usuário **esteja nas** `admin` funções **e** `developer` com código de procedimento alterando o [condicional ou ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) para uma [condicional e ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) no exemplo anterior:

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>Recursos adicionais

* [IDs de modelo de função (documentação do Azure)](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [`groupMembershipClaims` atributo (documentação do Azure)](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [Como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token (documentação do Azure)](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [Funções de aplicativo (documentação do Azure)](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
