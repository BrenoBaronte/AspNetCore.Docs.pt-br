---
title: Proteger um Blazor aplicativo hospedado Webassembly ASP.NET Core com o Identity servidor
author: guardrex
description: Para criar um novo Blazor aplicativo hospedado com autenticação de dentro do Visual Studio que usa um back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 8251658a2bb99ec17424ec26b8a44171082fab05
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243456"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Proteger um Blazor aplicativo hospedado Webassembly ASP.NET Core com o Identity servidor

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

Este artigo explica como criar um novo Blazor aplicativo hospedado que usa [IdentityServer](https://identityserver.io/) para autenticar usuários e chamadas de API.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

No Visual Studio:

1. Crie um novo aplicativo ** Blazor Webassembly** . Para obter mais informações, consulte <xref:blazor/get-started>.
1. Na caixa de diálogo **criar um novo Blazor aplicativo** , selecione **alterar** na seção **autenticação** .
1. Selecione **contas de usuário individuais** seguidas por **OK**.
1. Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .
1. Selecione o botão **Criar**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Para criar o aplicativo em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ). O nome da pasta também se torna parte do nome do projeto.

---

## <a name="server-app-configuration"></a>Configuração de aplicativo do servidor

As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.

### <a name="startup-class"></a>Classe de inicialização

A `Startup` classe tem as seguintes adições.

* Em `Startup.ConfigureServices`:

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer com um <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura as convenções de ASP.NET Core padrão na parte superior do IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticação com um <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Em `Startup.Configure`:

  * O middleware IdentityServer expõe os pontos de extremidade do Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * O middleware de autenticação é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:

    ```csharp
    app.UseAuthentication();
    ```

  * O middleware de autorização habilita os recursos de autorização:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura [IdentityServer](https://identityserver.io/) para cenários de ASP.NET Core. O IdentityServer é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo. O IdentityServer expõe complexidade desnecessária para os cenários mais comuns. Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida. Depois que a autenticação precisar ser alterada, todo o poder do IdentityServer estará disponível para personalizar a autenticação de acordo com os requisitos do aplicativo.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão. A política está configurada para permitir que o Identity manipule todas as solicitações roteadas para qualquer subcaminho no Identity espaço de URL `/Identity` . O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações. Além disso, esse método:

* Registra um `{APPLICATION NAME}API` recurso de API com IdentityServer com um escopo padrão de `{APPLICATION NAME}API` .
* Configura o middleware do token de portador JWT para validar tokens emitidos por IdentityServer para o aplicativo.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

No `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo é aplicado à classe. O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso. A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . O método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> é configurado como o manipulador padrão para solicitações ao aplicativo.

### <a name="applicationdbcontext"></a>ApplicationDbContext

No `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende- <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se para incluir o esquema para IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>é derivado de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

Para obter controle total do esquema de banco de dados, herde de uma das Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponíveis e configure o contexto para incluir o Identity esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> método.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

No `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.

### <a name="app-settings-files"></a>Arquivos de configurações do aplicativo

No arquivo de configurações do aplicativo ( `appsettings.json` ) na raiz do projeto, a `IdentityServer` seção descreve a lista de clientes configurados. No exemplo a seguir, há um único cliente. O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth. O perfil indica o tipo de aplicativo que está sendo configurado. O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.Client` ).

## <a name="client-app-configuration"></a>Configuração do aplicativo cliente

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas de usuário individuais ( `Individual` ), o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote no arquivo de projeto do aplicativo. O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>Suporte à autorização de API

O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote. Esse método configura os serviços exigidos pelo aplicativo para interagir com o sistema de autorização existente.

```csharp
builder.Services.AddApiAuthorization();
```

Por padrão, a configuração para o aplicativo é carregada por convenção de `_configuration/{client-id}` . Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo. Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.

### <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:

* Para usuários autenticados:
  * Exibe o nome de usuário atual.
  * Oferece um link para a página de perfil do usuário no ASP.NET Core Identity .
  * Oferece um botão para fazer logoff do aplicativo.
* Para usuários anônimos:
  * Oferece a opção de se registrar.
  * Oferece a opção de fazer logon.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
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

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Executar o aplicativo

Execute o aplicativo no projeto do servidor. Ao usar o Visual Studio, seja:

* Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .
* Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .

## <a name="name-and-role-claim-with-api-authorization"></a>Nome e declaração de função com autorização de API

### <a name="custom-user-factory"></a>Fábrica de usuário personalizada

No aplicativo cliente, crie uma fábrica de usuário personalizada. IdentityO servidor envia várias funções como uma matriz JSON em uma única `role` declaração. Uma única função é enviada como um valor de cadeia de caracteres na declaração. A fábrica cria uma `role` declaração individual para cada uma das funções do usuário.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

No aplicativo cliente, registre a fábrica em `Program.Main` ( `Program.cs` ):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

No aplicativo de servidor, chame <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> no Identity Construtor, que adiciona serviços relacionados a funções:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Configurar Identity servidor

Use **uma** das seguintes abordagens:

* [Opções de autorização de API](#api-authorization-options)
* [Serviço de perfil](#profile-service)

#### <a name="api-authorization-options"></a>Opções de autorização de API

No aplicativo do servidor:

* Configure Identity o servidor para colocar o `name` e as `role` declarações no token de ID e no token de acesso.
* Impedir o mapeamento padrão para funções no manipulador de tokens JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Serviço de perfil

No aplicativo de servidor, crie uma `ProfileService` implementação.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

No aplicativo de servidor, registre o serviço de perfil em `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Usar mecanismos de autorização

No aplicativo cliente, as abordagens de autorização de componente são funcionais neste ponto. Qualquer um dos mecanismos de autorização nos componentes pode usar uma função para autorizar o usuário:

* [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )
* [Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )

  Há suporte para vários testes de função:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`é preenchido no aplicativo cliente com o nome de usuário do usuário, que geralmente é seu endereço de email de entrada.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Implantação no serviço Azure App](xref:security/authentication/identity/spa#deploy-to-production)
* [Importar um certificado do Key Vault (documentação do Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)