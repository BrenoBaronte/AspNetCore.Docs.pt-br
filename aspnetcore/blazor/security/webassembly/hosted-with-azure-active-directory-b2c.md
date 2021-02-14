---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory B2C
author: guardrex
description: Saiba como proteger um aplicativo ASP.NET Core Blazor WebAssembly hospedado com Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: c67f8e8d81fbdbd8a1f103dd1bd258212efe014f
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280954"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory B2C

Este artigo descreve como criar um [ Blazor WebAssembly aplicativo hospedado](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrar aplicativos em AAD B2C e criar solução

### <a name="create-a-tenant"></a>Criar um locatário

Siga as orientações em [tutorial: criar um locatário de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para criar um locatário de AAD B2C. Retorne a este artigo imediatamente após criar ou identificar um locatário a ser usado.

Registre a instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/` , que inclui a barra à direita). A instância é o esquema e o host de um registro de aplicativo B2C do Azure, que pode ser encontrado abrindo a janela **pontos de extremidade** na página **registros de aplicativo** no portal do Azure.

### <a name="register-a-server-api-app"></a>Registrar um aplicativo de API do servidor

Registrar um aplicativo de AAD B2C para o *aplicativo de API do servidor*:

1. Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, **Blazor Server AAD B2C**).
1. Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)**
1. O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.
1. Confirme se **as permissões**  >  **concedem consentimento de administrador ao OpenID e offline_access permissões** estão selecionadas.
1. Selecione **Registrar**.

Registre as seguintes informações:

* *Aplicativo de API do servidor* ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* Domínio principal/Publicador/locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.

No **expor uma API**:

1. Selecione **Adicionar um escopo**.
1. Selecione **Salvar e continuar**.
1. Forneça um **nome de escopo** (por exemplo, `API.Access` ).
1. Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).
1. Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).
1. Confirme se o **estado** está definido como **habilitado**.
1. Selecione **Adicionar escopo**.

Registre as seguintes informações:

* URI da ID do aplicativo (por exemplo,, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ou o valor personalizado que você forneceu)
* Nome do escopo (por exemplo, `API.Access` )

### <a name="register-a-client-app"></a>Registrar um aplicativo cliente

Registrar um aplicativo de AAD B2C para o *aplicativo cliente*:

::: moniker range=">= aspnetcore-5.0"

1. Em **Azure Active Directory** > **registros de aplicativo**, selecione **novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD B2C cliente**).
1. Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)**
1. Defina a lista suspensa **URI de redirecionamento** para o **aplicativo de página única (Spa)** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` . A porta padrão para um aplicativo em execução no Kestrel é 5001. Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo. Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas *`Server`* Propriedades do aplicativo no painel de **depuração** . Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento. Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.
1. Confirme se **as permissões** > **concedem consentimento de administrador ao OpenID e offline_access permissões** estão selecionadas.
1. Selecione **Registrar**.

1. Registre a ID do aplicativo (cliente) (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Em configurações de plataforma de **autenticação** >  > **, um aplicativo de página única (Spa)**:

1. Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.
1. Para **concessão implícita**, verifique se as caixas de seleção para **tokens de acesso** e **tokens de ID** **não** estão selecionadas.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Em **Azure Active Directory** > **registros de aplicativo**, selecione **novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD B2C cliente**).
1. Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)**
1. Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` . A porta padrão para um aplicativo em execução no Kestrel é 5001. Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo. Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas *`Server`* Propriedades do aplicativo no painel de **depuração** . Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento. Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.
1. Confirme se **as permissões** > **concedem consentimento de administrador ao OpenID e offline_access permissões** estão selecionadas.
1. Selecione **Registrar**.

Registre a ID do aplicativo (cliente) (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Em  > **configurações da plataforma** de autenticação > **Web**:

1. Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.
1. Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

::: moniker-end

Em **permissões de API**:

1. Selecione **Adicionar uma permissão** seguida por **minhas APIs**.
1. Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, **Blazor Server AAD B2C**).
1. Abra a lista de **APIs** .
1. Habilite o acesso à API (por exemplo, `API.Access` ).
1. Escolha **Adicionar permissões**.
1. Selecione o botão **conceder consentimento de administrador para {nome do locatário}** . Clique em **Sim** para confirmar.

Em **casa**  >  **Azure ad B2C**  >  **fluxos de usuário**:

[Criar um fluxo de usuário de inscrição e de entrada](/azure/active-directory-b2c/tutorial-create-user-flows)

No mínimo, selecione o atributo de usuário nome de exibição de **declarações do aplicativo**  >   para popular o `context.User.Identity.Name` no `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).

Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin` ).

### <a name="create-the-app"></a>Criar o aplicativo

Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Espaço reservado                   | Nome do portal do Azure                                     | Exemplo                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | Instância                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | ID do aplicativo (cliente) para o *`Client`* aplicativo        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | Nome do escopo                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | ID do aplicativo (cliente) para o *aplicativo de API do servidor*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | URI da ID de Aplicativo&dagger;                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | Fluxo de usuário de inscrição/entrada                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | Domínio primário/Publicador/locatário                       | `contoso.onmicrosoft.com`                      |

&dagger;O Blazor WebAssembly modelo adiciona automaticamente um esquema de `api://` para o argumento de URI da ID do aplicativo passado no `dotnet new` comando. Ao fornecer o URI da ID do aplicativo para o `{SERVER API APP ID URI}` espaço reservado e se o esquema for `api://` , remova o esquema ( `api://` ) do argumento, como mostra o valor de exemplo na tabela anterior. Se o URI da ID do aplicativo for um valor personalizado ou tiver algum outro esquema (por exemplo, `https://` para um domínio não confiável do editor semelhante a `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), você deverá atualizar manualmente o URI do escopo padrão e remover o `api://` esquema depois que o *`Client`* aplicativo for criado pelo modelo. Para obter mais informações, consulte a observação na seção [escopos de token de acesso](#access-token-scopes) . O Blazor WebAssembly modelo pode ser alterado em uma versão futura do ASP.NET Core para resolver esses cenários. Para obter mais informações, consulte [esquema duplo para URI de ID do aplicativo com Blazor modelo WASM (hospedado, organização única) (dotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.

> [!NOTE]
> O escopo configurado pelo Blazor modelo hospedado pode ter o host de URI de ID de aplicativo repetido. Confirme se o escopo configurado para a `DefaultAccessTokenScopes` coleção está correto no `Program.Main` ( `Program.cs` ) do *`Client`* aplicativo.

> [!NOTE]
> No portal do Azure, o *`Client`* **URI de redirecionamento** de configuração de plataforma do aplicativo é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.
>
> Se o *`Client`* aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades *do aplicativo de API do servidor* no painel de **depuração** .
>
> Se a porta não foi configurada anteriormente com a *`Client`* porta conhecida do aplicativo, retorne ao *`Client`* registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.

## <a name="server-app-configuration"></a>*`Server`* configuração do aplicativo

*Esta seção pertence ao aplicativo da solução **`Server`** .*

### <a name="authentication-package"></a>Pacote de autenticação

::: moniker range=">= aspnetcore-5.0"

O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web com a Identity plataforma Microsoft é fornecido pelos seguintes pacotes:

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em NuGet.org.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) pacote:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

::: moniker-end

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

::: moniker range=">= aspnetcore-5.0"

O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão. O <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> método configura os serviços para proteger a API Web com a Identity plataforma Microsoft v 2.0. Esse método espera uma `AzureAdB2C` seção na configuração do aplicativo com as configurações necessárias para inicializar as opções de autenticação.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão. O <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory B2C:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Verifique se:

* O aplicativo tenta analisar e validar tokens em solicitações de entrada.
* Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Usuário. Identity . Nomes

Por padrão, o `User.Identity.Name` não é preenchido.

Para configurar o aplicativo para receber o valor do `name` tipo de declaração:

* Adicione um namespace para <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> `Startup.cs` :

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* Configure o <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Configure o <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a>Configurações do aplicativo

O `appsettings.json` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Exemplo:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Controlador WeatherForecast

O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o [ `[Authorize]` atributo](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado ao controlador. É **importante** entender que:

* O [ `[Authorize]` atributo](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.
* O [ `[Authorize]` atributo](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) usado no Blazor WebAssembly aplicativo serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>*`Client`* configuração do aplicativo

*Esta seção pertence ao aplicativo da solução **`Client`** .*

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar uma conta individual do B2C ( `IndividualB2C` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote ao aplicativo.

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para <xref:System.Net.Http.HttpClient> instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.ServerAPI` ).

O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote. Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.

A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Exemplo:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a>Escopos de token de acesso

Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:

* Incluído por padrão na solicitação de entrada.
* Usado para provisionar um token de acesso imediatamente após a autenticação.

Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory. Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> O Blazor WebAssembly modelo adiciona automaticamente um esquema de `api://` para o argumento de URI da ID do aplicativo passado no `dotnet new` comando. Ao gerar um aplicativo a partir do Blazor modelo de projeto, confirme se o valor do escopo de token de acesso padrão usa o valor de URI de ID de aplicativo personalizado correto que você forneceu no portal do Azure ou um valor com **um** dos seguintes formatos:
>
> * Quando o domínio do Publicador do diretório é **confiável**, o escopo do token de acesso padrão normalmente é um valor semelhante ao exemplo a seguir, em que `API.Access` é o nome do escopo padrão:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Inspecione o valor de um esquema duplo ( `api://api://...` ). Se um esquema duplo estiver presente, remova o primeiro `api://` esquema do valor.
>
> * Quando o domínio do Publicador do diretório não é **confiável**, o escopo do token de acesso padrão normalmente é um valor semelhante ao exemplo a seguir, em que `API.Access` é o nome do escopo padrão:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Inspecione o valor de um `api://` esquema extra ( `api://https://contoso.onmicrosoft.com/...` ). Se um `api://` esquema extra estiver presente, remova o `api://` esquema do valor.
>
> O Blazor WebAssembly modelo pode ser alterado em uma versão futura do ASP.NET Core para resolver esses cenários. Para obter mais informações, consulte [esquema duplo para URI de ID do aplicativo com Blazor modelo WASM (hospedado, organização única) (dotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

Especifique escopos adicionais com `AdditionalScopesToConsent` :

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :

* [Solicitar tokens de acesso adicionais](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anexar tokens a solicitações de saída](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Modo de logon

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a>Execute o aplicativo

Execute o aplicativo no projeto do servidor. Ao usar o Visual Studio, seja:

* Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .
* Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/security/webassembly/additional-scenarios>
* [Criar uma versão personalizada da biblioteca de JavaScript de autenticação. MSAL](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Tutorial: Criar um locatário do Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Tutorial: Registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)
* [Documentação da plataforma de identidade da Microsoft](/azure/active-directory/develop/)
