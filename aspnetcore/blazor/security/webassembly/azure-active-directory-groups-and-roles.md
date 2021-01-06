---
title: ASP.NET Core Blazor WebAssembly com grupos e funções de Azure Active Directory
author: guardrex
description: Saiba como configurar o Blazor WebAssembly para usar grupos de Azure Active Directory e funções.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: ded70f028b3021574ba260838837d9b23abd72f1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94981876"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="aedee-103">Azure Active Directory (AAD) grupos, funções de administrador e funções definidas pelo usuário</span><span class="sxs-lookup"><span data-stu-id="aedee-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="aedee-104">De [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="aedee-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="aedee-105">Este artigo se aplica a Blazor ASP.NET Core aplicativos versão 3,1 com o Microsoft Identity v 1.0 e está agendado para atualização para 5,0 com Identity v 2.0.</span><span class="sxs-lookup"><span data-stu-id="aedee-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity v1.0 and is scheduled for update to 5.0 with Identity v2.0.</span></span> <span data-ttu-id="aedee-106">Para obter mais informações, consulte [ Blazor WASM com grupos e funções do AAD/B2C (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span><span class="sxs-lookup"><span data-stu-id="aedee-106">For more information, see [Blazor WASM with AAD/B2C groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span></span>

<span data-ttu-id="aedee-107">Azure Active Directory (AAD) fornece várias abordagens de autorização que podem ser combinadas com ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="aedee-107">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="aedee-108">Grupos definidos pelo usuário</span><span class="sxs-lookup"><span data-stu-id="aedee-108">User-defined groups</span></span>
  * <span data-ttu-id="aedee-109">Segurança</span><span class="sxs-lookup"><span data-stu-id="aedee-109">Security</span></span>
  * <span data-ttu-id="aedee-110">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="aedee-110">Microsoft 365</span></span>
  * <span data-ttu-id="aedee-111">Distribuição</span><span class="sxs-lookup"><span data-stu-id="aedee-111">Distribution</span></span>
* <span data-ttu-id="aedee-112">Funções</span><span class="sxs-lookup"><span data-stu-id="aedee-112">Roles</span></span>
  * <span data-ttu-id="aedee-113">Funções de administrador do AAD</span><span class="sxs-lookup"><span data-stu-id="aedee-113">AAD Administrator Roles</span></span>
  * <span data-ttu-id="aedee-114">Funções definidas pelo usuário</span><span class="sxs-lookup"><span data-stu-id="aedee-114">User-defined roles</span></span>

<span data-ttu-id="aedee-115">As diretrizes neste artigo se aplicam aos Blazor WebAssembly cenários de implantação do AAD descritos nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="aedee-115">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="aedee-116">Aplicativo autônomo com contas Microsoft</span><span class="sxs-lookup"><span data-stu-id="aedee-116">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="aedee-117">Aplicativo autônomo com o AAD</span><span class="sxs-lookup"><span data-stu-id="aedee-117">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="aedee-118">Aplicativo hospedado com o AAD</span><span class="sxs-lookup"><span data-stu-id="aedee-118">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="aedee-119">Escopos</span><span class="sxs-lookup"><span data-stu-id="aedee-119">Scopes</span></span>

<span data-ttu-id="aedee-120">Uma chamada à [API Microsoft Graph](/graph/use-the-api) é necessária para qualquer usuário de aplicativo com mais de cinco associações de grupo de segurança e função de administrador do AAD.</span><span class="sxs-lookup"><span data-stu-id="aedee-120">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="aedee-121">Para permitir chamadas de API do Graph, conceda a *`Client`* um aplicativo autônomo ou de uma solução hospedada Blazor qualquer um dos seguintes [API do Graph permissões (escopos)](/graph/permissions-reference) no portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="aedee-121">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="aedee-122">`Directory.Read.All` é o escopo com privilégios mínimos e é o escopo usado para o exemplo descrito neste artigo.</span><span class="sxs-lookup"><span data-stu-id="aedee-122">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="aedee-123">Grupos definidos pelo usuário e funções de administrador</span><span class="sxs-lookup"><span data-stu-id="aedee-123">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="aedee-124">Para configurar o aplicativo no portal do Azure para fornecer uma `groups` declaração de associação, consulte os seguintes artigos do Azure.</span><span class="sxs-lookup"><span data-stu-id="aedee-124">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="aedee-125">Atribuir usuários a grupos do AAD definidos pelo usuário e funções de administrador do AAD.</span><span class="sxs-lookup"><span data-stu-id="aedee-125">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="aedee-126">As funções que usam grupos de segurança do Azure AD</span><span class="sxs-lookup"><span data-stu-id="aedee-126">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="aedee-127">`groupMembershipClaims` Attribute</span><span class="sxs-lookup"><span data-stu-id="aedee-127">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="aedee-128">Os exemplos a seguir pressupõem que um usuário é atribuído à função de *administrador de cobrança* do AAD.</span><span class="sxs-lookup"><span data-stu-id="aedee-128">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="aedee-129">A única `groups` declaração enviada pelo AAD apresenta os grupos e as funções do usuário como IDs de objeto (GUIDs) em uma matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="aedee-129">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="aedee-130">O aplicativo deve converter a matriz JSON de grupos e funções em declarações individuais nas `group` quais o aplicativo pode criar [políticas](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="aedee-130">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="aedee-131">Quando o número de funções de administrador do AAD atribuído e grupos definidos pelo usuário exceder cinco, o AAD enviará uma `hasgroups` declaração com um `true` valor em vez de enviar uma `groups` declaração.</span><span class="sxs-lookup"><span data-stu-id="aedee-131">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="aedee-132">Qualquer aplicativo que possa ter mais de cinco funções e grupos atribuídos a seus usuários deve fazer uma chamada API do Graph separada para obter funções e grupos de um usuário.</span><span class="sxs-lookup"><span data-stu-id="aedee-132">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="aedee-133">A implementação de exemplo fornecida neste artigo aborda esse cenário.</span><span class="sxs-lookup"><span data-stu-id="aedee-133">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="aedee-134">Para obter mais informações, consulte o `groups` e `hasgroups` informações de declarações no artigo [tokens de acesso da plataforma de identidade da Microsoft: declarações de carga](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="aedee-134">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="aedee-135">Estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir propriedades de matriz para grupos e funções.</span><span class="sxs-lookup"><span data-stu-id="aedee-135">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="aedee-136">Atribua uma matriz vazia a cada propriedade para que a verificação de `null` não seja necessária quando essas propriedades forem usadas em `foreach` loops posteriormente.</span><span class="sxs-lookup"><span data-stu-id="aedee-136">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="aedee-137">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="aedee-137">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="aedee-138">Use **uma** das abordagens a seguir para criar declarações para grupos e funções do AAD:</span><span class="sxs-lookup"><span data-stu-id="aedee-138">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="aedee-139">Usar o SDK do Graph</span><span class="sxs-lookup"><span data-stu-id="aedee-139">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="aedee-140">Usar um nome `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="aedee-140">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="aedee-141">Usar o SDK do Graph</span><span class="sxs-lookup"><span data-stu-id="aedee-141">Use the Graph SDK</span></span>

<span data-ttu-id="aedee-142">Adicione uma referência de pacote ao aplicativo autônomo ou ao *`Client`* aplicativo de uma Blazor solução hospedada para o [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="aedee-142">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="aedee-143">Adicione as classes e a configuração do utilitário SDK do Graph na seção *SDK do Graph* do <xref:blazor/security/webassembly/graph-api#graph-sdk> artigo.</span><span class="sxs-lookup"><span data-stu-id="aedee-143">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="aedee-144">Adicione a seguinte fábrica de conta de usuário personalizada ao APPO ou *`Client`* aplicativo autônomo de uma Blazor solução hospedada ( `CustomAccountFactory.cs` ).</span><span class="sxs-lookup"><span data-stu-id="aedee-144">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="aedee-145">A fábrica de usuário personalizada é usada para processar declarações de funções e grupos.</span><span class="sxs-lookup"><span data-stu-id="aedee-145">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="aedee-146">A `roles` matriz de declaração é abordada na seção [funções definidas pelo usuário](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="aedee-146">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="aedee-147">Se a `hasgroups` declaração estiver presente, o SDK do Graph será usado para fazer uma solicitação autorizada para API do Graph obter as funções e os grupos do usuário:</span><span class="sxs-lookup"><span data-stu-id="aedee-147">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="aedee-148">O código anterior não inclui associações transitivas.</span><span class="sxs-lookup"><span data-stu-id="aedee-148">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="aedee-149">Se o aplicativo exigir declarações de associação de grupo diretas e transitivas:</span><span class="sxs-lookup"><span data-stu-id="aedee-149">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="aedee-150">Altere o `IUserMemberOfCollectionWithReferencesPage` tipo para `groupsAndAzureRoles` para `IUserTransitiveMemberOfCollectionWithReferencesPage` .</span><span class="sxs-lookup"><span data-stu-id="aedee-150">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="aedee-151">Ao solicitar grupos e funções do usuário, substitua a `MemberOf` Propriedade por `TransitiveMemberOf` .</span><span class="sxs-lookup"><span data-stu-id="aedee-151">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="aedee-152">Em `Program.Main` ( `Program.cs` ), configure a autenticação MSAL para usar o alocador de conta de usuário personalizada: se o aplicativo usar uma classe de conta de usuário personalizada que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , troque a classe de conta de usuário personalizada para <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="aedee-152">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a><span data-ttu-id="aedee-153">Usar um nome `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="aedee-153">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="aedee-154">No aplicativo autônomo ou no *`Client`* aplicativo de uma solução hospedada Blazor , crie uma <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizada.</span><span class="sxs-lookup"><span data-stu-id="aedee-154">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="aedee-155">Use o escopo correto para chamadas de API do Graph que obtenham informações de função e grupo.</span><span class="sxs-lookup"><span data-stu-id="aedee-155">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="aedee-156">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="aedee-156">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="aedee-157">Em `Program.Main` ( `Program.cs` ), adicione o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> serviço de implementação e adicione um nome <xref:System.Net.Http.HttpClient> para fazer API do Graph solicitações.</span><span class="sxs-lookup"><span data-stu-id="aedee-157">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="aedee-158">O exemplo a seguir nomeia o cliente `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="aedee-158">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="aedee-159">Crie classes de objetos de diretório do AAD para receber as funções e grupos de Protocolo Open Data (OData) de uma chamada de API do Graph.</span><span class="sxs-lookup"><span data-stu-id="aedee-159">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="aedee-160">O OData chega no formato JSON e uma chamada para <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> popula uma instância da `DirectoryObjects` classe.</span><span class="sxs-lookup"><span data-stu-id="aedee-160">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="aedee-161">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="aedee-161">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="aedee-162">Crie uma fábrica de usuário personalizada para processar declarações de funções e grupos.</span><span class="sxs-lookup"><span data-stu-id="aedee-162">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="aedee-163">A implementação de exemplo a seguir também manipula a `roles` matriz de declaração, que é abordada na seção [funções definidas pelo usuário](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="aedee-163">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="aedee-164">Se a `hasgroups` declaração estiver presente, o nome <xref:System.Net.Http.HttpClient> será usado para fazer uma solicitação autorizada para API do Graph obter as funções e os grupos do usuário.</span><span class="sxs-lookup"><span data-stu-id="aedee-164">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="aedee-165">Essa implementação usa o ponto de extremidade do Microsoft Identity Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentação da API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="aedee-165">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="aedee-166">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="aedee-166">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="aedee-167">Não há necessidade de fornecer código para remover a declaração original `groups` , se presente, porque ela é automaticamente removida pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="aedee-167">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="aedee-168">A abordagem neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="aedee-168">The approach in this example:</span></span>
>
> * <span data-ttu-id="aedee-169">Adiciona uma <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizada para anexar tokens de acesso a solicitações de saída.</span><span class="sxs-lookup"><span data-stu-id="aedee-169">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="aedee-170">Adiciona um nome <xref:System.Net.Http.HttpClient> para fazer solicitações da API Web a um ponto de extremidade de API Web externo seguro.</span><span class="sxs-lookup"><span data-stu-id="aedee-170">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="aedee-171">Usa o nome <xref:System.Net.Http.HttpClient> para fazer solicitações autorizadas.</span><span class="sxs-lookup"><span data-stu-id="aedee-171">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="aedee-172">A cobertura geral para essa abordagem é encontrada no <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> artigo.</span><span class="sxs-lookup"><span data-stu-id="aedee-172">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="aedee-173">Registre a fábrica no `Program.Main` ( `Program.cs` ) do aplicativo autônomo ou *`Client`* aplicativo de uma solução hospedada Blazor .</span><span class="sxs-lookup"><span data-stu-id="aedee-173">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="aedee-174">Consentimento para o `Directory.Read.All` escopo como um escopo adicional para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="aedee-174">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a><span data-ttu-id="aedee-175">Configuração de autorização</span><span class="sxs-lookup"><span data-stu-id="aedee-175">Authorization configuration</span></span>

<span data-ttu-id="aedee-176">Crie uma [política](xref:security/authorization/policies) para cada grupo ou função no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="aedee-176">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="aedee-177">O exemplo a seguir cria uma política para a função de *administrador de cobrança* do AAD:</span><span class="sxs-lookup"><span data-stu-id="aedee-177">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="aedee-178">Para obter a lista completa de IDs de objeto de função do AAD, consulte a seção [IDs de objeto da função de administrador do AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="aedee-178">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="aedee-179">Nos exemplos a seguir, o aplicativo usa a política anterior para autorizar o usuário.</span><span class="sxs-lookup"><span data-stu-id="aedee-179">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="aedee-180">O [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funciona com a política:</span><span class="sxs-lookup"><span data-stu-id="aedee-180">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="aedee-181">O acesso a um componente inteiro pode ser baseado na política usando a [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="aedee-181">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="aedee-182">Se o usuário não estiver conectado, ele será redirecionado para a página de entrada do AAD e, em seguida, de volta para o componente depois de entrar.</span><span class="sxs-lookup"><span data-stu-id="aedee-182">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="aedee-183">Uma verificação de política também pode ser [executada no código com lógica de procedimento](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="aedee-183">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="aedee-184">Autorizar o acesso à API do servidor para grupos definidos pelo usuário e funções de administrador</span><span class="sxs-lookup"><span data-stu-id="aedee-184">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="aedee-185">Além de autorizar usuários no aplicativo Webassembly do lado do cliente para acessar páginas e recursos, a API do servidor pode autorizar os usuários a acessar pontos de extremidade de API seguros.</span><span class="sxs-lookup"><span data-stu-id="aedee-185">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="aedee-186">Depois que o aplicativo de *servidor* valida o token de acesso do usuário:</span><span class="sxs-lookup"><span data-stu-id="aedee-186">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="aedee-187">O aplicativo de API do servidor usa a declaração de [identificador de objeto imutável ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) do usuário de seu token de acesso para obter um token de acesso para API do Graph.</span><span class="sxs-lookup"><span data-stu-id="aedee-187">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="aedee-188">Uma chamada API do Graph Obtém o grupo de segurança definido pelo usuário do Azure do usuário e as associações de função de administrador chamando [`memberOf`](/graph/api/user-list-memberof) o usuário.</span><span class="sxs-lookup"><span data-stu-id="aedee-188">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="aedee-189">As associações são usadas para estabelecer `group` declarações.</span><span class="sxs-lookup"><span data-stu-id="aedee-189">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="aedee-190">[As políticas de autorização](xref:security/authorization/policies) podem ser usadas para limitar o acesso do usuário aos pontos de extremidade da API do servidor em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aedee-190">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="aedee-191">Atualmente, essas diretrizes não incluem a autorização de usuários com base em suas [funções do AAD definidas pelo usuário](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="aedee-191">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="aedee-192">As diretrizes nesta seção configuram o aplicativo de API do servidor como um [*aplicativo de daemon*](/azure/active-directory/develop/scenario-daemon-overview) para a chamada à API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="aedee-192">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="aedee-193">Essa abordagem **não**:</span><span class="sxs-lookup"><span data-stu-id="aedee-193">This approach does **not**:</span></span>

* <span data-ttu-id="aedee-194">Exigir o `access_as_user` escopo.</span><span class="sxs-lookup"><span data-stu-id="aedee-194">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="aedee-195">Acesse API do Graph em nome do usuário/cliente que faz a solicitação de API.</span><span class="sxs-lookup"><span data-stu-id="aedee-195">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="aedee-196">A chamada para API do Graph pelo aplicativo de API de servidor requer apenas um escopo de API do Graph **aplicativo** de API de servidor para `Directory.Read.All` o no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="aedee-196">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="aedee-197">Essa abordagem impede absolutamente que o aplicativo cliente acesse dados de diretório que a API do servidor não permite explicitamente.</span><span class="sxs-lookup"><span data-stu-id="aedee-197">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="aedee-198">O aplicativo cliente só pode acessar os pontos de extremidade do controlador do aplicativo de API do servidor.</span><span class="sxs-lookup"><span data-stu-id="aedee-198">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="aedee-199">Configuração do Azure</span><span class="sxs-lookup"><span data-stu-id="aedee-199">Azure configuration</span></span>

* <span data-ttu-id="aedee-200">Confirme se o registro do aplicativo do *servidor* recebeu o escopo de API do Graph de **aplicativo** (não **delegado**) para `Directory.Read.All` , que é o nível de acesso com privilégios mínimos para grupos de segurança.</span><span class="sxs-lookup"><span data-stu-id="aedee-200">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="aedee-201">Confirme se o consentimento do administrador é aplicado ao escopo depois de fazer a atribuição de escopo.</span><span class="sxs-lookup"><span data-stu-id="aedee-201">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="aedee-202">Atribua um novo segredo do cliente ao aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="aedee-202">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="aedee-203">Observe o segredo para a configuração do aplicativo na seção [configurações do aplicativo](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="aedee-203">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="aedee-204">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="aedee-204">App settings</span></span>

<span data-ttu-id="aedee-205">No arquivo de configurações do aplicativo ( `appsettings.json` ou `appsettings.Production.json` ), crie uma `ClientSecret` entrada com o segredo do cliente do aplicativo do *servidor* do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="aedee-205">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="aedee-206">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="aedee-206">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="aedee-207">Se o domínio do Publicador do locatário não for verificado, o escopo da API do servidor para acesso de usuário/cliente usará um `https://` URI baseado em.</span><span class="sxs-lookup"><span data-stu-id="aedee-207">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="aedee-208">Nesse cenário, o aplicativo de API do servidor requer `Audience` configuração no `appsettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="aedee-208">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="aedee-209">Na configuração a seguir, o final do `Audience` valor não inclui  o escopo padrão `/{DEFAULT SCOPE}` , em que o espaço reservado `{DEFAULT SCOPE}` é o escopo padrão:</span><span class="sxs-lookup"><span data-stu-id="aedee-209">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="aedee-210">Na configuração de exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="aedee-210">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="aedee-211">O domínio do locatário é `contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="aedee-211">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="aedee-212">O aplicativo de API do servidor `ClientId` é `41451fa7-82d9-4673-8fa5-69eff5a761fd` .</span><span class="sxs-lookup"><span data-stu-id="aedee-212">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="aedee-213">A configuração de um `Audience` normalmente **não** é necessária para o aplicativo com um domínio do Publicador verificado que tenha um `api://` escopo de API baseado em.</span><span class="sxs-lookup"><span data-stu-id="aedee-213">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="aedee-214">Para obter mais informações, consulte <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="aedee-214">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="aedee-215">Políticas de autorização</span><span class="sxs-lookup"><span data-stu-id="aedee-215">Authorization policies</span></span>

<span data-ttu-id="aedee-216">Crie [políticas de autorização](xref:security/authorization/policies) para grupos de segurança do AAD e funções de administrador do AAD no aplicativo do *servidor* `Startup.ConfigureServices` ( `Startup.cs` ) com base em IDs de objeto de grupo e [IDs de objeto de função de administrador do AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="aedee-216">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="aedee-217">Por exemplo, uma política de função de administrador de cobrança do Azure tem a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="aedee-217">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="aedee-218">Para obter mais informações, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="aedee-218">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="aedee-219">Acesso ao controlador</span><span class="sxs-lookup"><span data-stu-id="aedee-219">Controller access</span></span>

<span data-ttu-id="aedee-220">Exigir políticas nos controladores do aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="aedee-220">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="aedee-221">O exemplo a seguir limita o acesso a dados de cobrança do `BillingDataController` para administradores de cobrança do Azure com um nome de política de `BillingAdmin` , conforme configurado na seção [políticas de autorização](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="aedee-221">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="aedee-222">Pacotes</span><span class="sxs-lookup"><span data-stu-id="aedee-222">Packages</span></span>

<span data-ttu-id="aedee-223">Adicione referências de pacote ao aplicativo de *servidor* para os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="aedee-223">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="aedee-224">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="aedee-224">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="aedee-225">[Microsoft. Identity . Cliente](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="aedee-225">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="aedee-226">Serviços</span><span class="sxs-lookup"><span data-stu-id="aedee-226">Services</span></span>

<span data-ttu-id="aedee-227">No método do aplicativo do *servidor* `Startup.ConfigureServices` , namespaces adicionais são necessários para o código na `Startup` classe do aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="aedee-227">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="aedee-228">Adicione os seguintes namespaces a `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="aedee-228">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="aedee-229">Ao configurar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="aedee-229">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="aedee-230">Opcionalmente, inclua o processamento para <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="aedee-230">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="aedee-231">Por exemplo, o aplicativo pode registrar em log a autenticação com falha.</span><span class="sxs-lookup"><span data-stu-id="aedee-231">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="aedee-232">No <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , faça uma chamada de API do Graph para obter os grupos e as funções do usuário.</span><span class="sxs-lookup"><span data-stu-id="aedee-232">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="aedee-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fornece informações de identificação pessoal (PII) em mensagens de log.</span><span class="sxs-lookup"><span data-stu-id="aedee-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="aedee-234">Ative somente PII para depuração com contas de usuário de teste.</span><span class="sxs-lookup"><span data-stu-id="aedee-234">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="aedee-235">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="aedee-235">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="aedee-236">No código anterior, manipular os seguintes erros de token é opcional:</span><span class="sxs-lookup"><span data-stu-id="aedee-236">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="aedee-237">`MsalUiRequiredException`: O aplicativo não tem permissões suficientes (escopos).</span><span class="sxs-lookup"><span data-stu-id="aedee-237">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="aedee-238">Determine se os escopos de aplicativo de API do servidor no portal do Azure incluem uma permissão de **aplicativo** para `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="aedee-238">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="aedee-239">Confirme se o administrador de locatários concedeu permissões para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aedee-239">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="aedee-240">`MsalServiceException` ( `AADSTS70011` ): Confirme se o escopo é `https://graph.microsoft.com/.default` .</span><span class="sxs-lookup"><span data-stu-id="aedee-240">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="aedee-241">Pacotes</span><span class="sxs-lookup"><span data-stu-id="aedee-241">Packages</span></span>

<span data-ttu-id="aedee-242">Adicione referências de pacote ao aplicativo de *servidor* para os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="aedee-242">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="aedee-243">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="aedee-243">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="aedee-244">[Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="aedee-244">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="aedee-245">Configuração de serviço</span><span class="sxs-lookup"><span data-stu-id="aedee-245">Service configuration</span></span>

<span data-ttu-id="aedee-246">No método do aplicativo do *servidor* `Startup.ConfigureServices` , adicione lógica para fazer com que o API do Graph chame e estabeleça `group` declarações de usuário para grupos de segurança e funções do usuário.</span><span class="sxs-lookup"><span data-stu-id="aedee-246">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="aedee-247">O código de exemplo nesta seção usa o Biblioteca de Autenticação do Active Directory (ADAL), que é baseado na Identity plataforma Microsoft v 1.0.</span><span class="sxs-lookup"><span data-stu-id="aedee-247">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="aedee-248">Namespaces adicionais são necessários para o código na `Startup` classe do aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="aedee-248">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="aedee-249">O conjunto de instruções a seguir `using` inclui os namespaces necessários para o código a seguir nesta seção:</span><span class="sxs-lookup"><span data-stu-id="aedee-249">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="aedee-250">Ao configurar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="aedee-250">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="aedee-251">Opcionalmente, inclua o processamento para <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="aedee-251">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="aedee-252">Por exemplo, o aplicativo pode registrar em log a autenticação com falha.</span><span class="sxs-lookup"><span data-stu-id="aedee-252">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="aedee-253">No <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , faça uma chamada de API do Graph para obter os grupos e as funções do usuário.</span><span class="sxs-lookup"><span data-stu-id="aedee-253">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="aedee-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fornece informações de identificação pessoal (PII) em mensagens de log.</span><span class="sxs-lookup"><span data-stu-id="aedee-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="aedee-255">Ative somente PII para depuração com contas de usuário de teste.</span><span class="sxs-lookup"><span data-stu-id="aedee-255">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="aedee-256">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="aedee-256">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="aedee-257">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="aedee-257">In the preceding example:</span></span>

* <span data-ttu-id="aedee-258">A tentativa de aquisição de token silenciosa ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ) é tentada primeiro porque o token de acesso pode já ter sido armazenado no cache de token do Adal.</span><span class="sxs-lookup"><span data-stu-id="aedee-258">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="aedee-259">É mais rápido obter o token do cache do que solicitar um novo token.</span><span class="sxs-lookup"><span data-stu-id="aedee-259">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="aedee-260">Se o token de acesso não for adquirido do cache ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> ou <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> for gerado), uma declaração de usuário ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) será feita com a credencial do cliente ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) para obter o token em nome do usuário ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="aedee-260">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="aedee-261">Em seguida, o `Microsoft.Graph.GraphServiceClient` pode continuar a usar o token para fazer a chamada de API do Graph.</span><span class="sxs-lookup"><span data-stu-id="aedee-261">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="aedee-262">O token é colocado no cache de token do ADAL.</span><span class="sxs-lookup"><span data-stu-id="aedee-262">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="aedee-263">Para chamadas de API do Graph futuras para o mesmo usuário, o token é adquirido do cache silenciosamente com <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="aedee-263">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="aedee-264">O código em <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> não obtém associações transitivas.</span><span class="sxs-lookup"><span data-stu-id="aedee-264">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="aedee-265">Para alterar o código para obter associações diretas e transitivas:</span><span class="sxs-lookup"><span data-stu-id="aedee-265">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="aedee-266">Para a linha de código:</span><span class="sxs-lookup"><span data-stu-id="aedee-266">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="aedee-267">Substituir a linha anterior por:</span><span class="sxs-lookup"><span data-stu-id="aedee-267">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="aedee-268">Para a linha de código:</span><span class="sxs-lookup"><span data-stu-id="aedee-268">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="aedee-269">Substituir a linha anterior por:</span><span class="sxs-lookup"><span data-stu-id="aedee-269">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="aedee-270">O código em <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> não distingue entre os grupos de segurança do AAD e as funções de administrador do AAD ao criar declarações.</span><span class="sxs-lookup"><span data-stu-id="aedee-270">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="aedee-271">Para o aplicativo distinguir entre grupos e funções, marque a `entry.ODataType` iteração por meio dos grupos e das funções.</span><span class="sxs-lookup"><span data-stu-id="aedee-271">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="aedee-272">Para criar declarações de função e grupo de segurança separados, use um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="aedee-272">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="aedee-273">Funções definidas pelo usuário</span><span class="sxs-lookup"><span data-stu-id="aedee-273">User-defined roles</span></span>

<span data-ttu-id="aedee-274">Um aplicativo registrado no AAD também pode ser configurado para usar funções definidas pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="aedee-274">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="aedee-275">Para configurar o aplicativo no portal do Azure para fornecer uma `roles` declaração de associação, consulte [como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="aedee-275">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="aedee-276">O exemplo a seguir pressupõe que um aplicativo está configurado com duas funções:</span><span class="sxs-lookup"><span data-stu-id="aedee-276">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="aedee-277">Embora não seja possível atribuir funções a grupos de segurança sem uma conta de Azure AD Premium, você pode atribuir usuários a funções e receber uma `roles` declaração para usuários com uma conta padrão do Azure.</span><span class="sxs-lookup"><span data-stu-id="aedee-277">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="aedee-278">As diretrizes nesta seção não exigem uma conta de Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="aedee-278">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="aedee-279">Várias funções são atribuídas no portal do Azure ao **_adicionar novamente um usuário_** para cada atribuição de função adicional.</span><span class="sxs-lookup"><span data-stu-id="aedee-279">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="aedee-280">A única `roles` declaração enviada pelo AAD apresenta as funções definidas pelo usuário como `appRoles` `value` s em uma matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="aedee-280">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="aedee-281">O aplicativo deve converter a matriz JSON de funções em `role` declarações individuais.</span><span class="sxs-lookup"><span data-stu-id="aedee-281">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="aedee-282">O `CustomUserFactory` mostrado na seção [grupos definidos pelo usuário e funções de administrador do AAD](#user-defined-groups-and-administrator-roles) é configurado para agir em uma `roles` declaração com um valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="aedee-282">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="aedee-283">Adicione e registre o `CustomUserFactory` no aplicativo autônomo ou *`Client`* aplicativo de uma solução hospedada Blazor , conforme mostrado na seção [grupos definidos pelo usuário e funções de administrador do AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="aedee-283">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="aedee-284">Não é necessário fornecer código para remover a `roles` declaração original porque ela é automaticamente removida pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="aedee-284">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="aedee-285">No `Program.Main` aplicativo autônomo ou *`Client`* aplicativo de uma solução hospedada Blazor , especifique a declaração denominada " `role` " como a declaração de função:</span><span class="sxs-lookup"><span data-stu-id="aedee-285">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="aedee-286">As abordagens de autorização de componente são funcionais neste ponto.</span><span class="sxs-lookup"><span data-stu-id="aedee-286">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="aedee-287">Qualquer um dos mecanismos de autorização nos componentes pode usar a `admin` função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="aedee-287">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="aedee-288">[ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="aedee-288">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="aedee-289">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="aedee-289">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="aedee-290">[Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="aedee-290">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="aedee-291">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="aedee-291">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="aedee-292">IDs de objeto de função de administrador do AAD</span><span class="sxs-lookup"><span data-stu-id="aedee-292">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="aedee-293">As IDs de objeto apresentadas na tabela a seguir são usadas para criar [políticas](xref:security/authorization/policies) para `group` declarações.</span><span class="sxs-lookup"><span data-stu-id="aedee-293">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="aedee-294">As políticas permitem que um aplicativo autorize usuários para várias atividades em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aedee-294">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="aedee-295">Para obter mais informações, consulte a seção [grupos definidos pelo usuário e funções de administrador do AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="aedee-295">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="aedee-296">Função de administrador do AAD</span><span class="sxs-lookup"><span data-stu-id="aedee-296">AAD Administrator Role</span></span> | <span data-ttu-id="aedee-297">ID de objeto</span><span class="sxs-lookup"><span data-stu-id="aedee-297">Object ID</span></span>
--- | ---
<span data-ttu-id="aedee-298">Administrador de aplicativos</span><span class="sxs-lookup"><span data-stu-id="aedee-298">Application administrator</span></span> | <span data-ttu-id="aedee-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="aedee-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="aedee-300">Desenvolvedor de aplicativos</span><span class="sxs-lookup"><span data-stu-id="aedee-300">Application developer</span></span> | <span data-ttu-id="aedee-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="aedee-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="aedee-302">Administrador de autenticação</span><span class="sxs-lookup"><span data-stu-id="aedee-302">Authentication administrator</span></span> | <span data-ttu-id="aedee-303">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="aedee-303">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="aedee-304">Administrador do Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="aedee-304">Azure DevOps administrator</span></span> | <span data-ttu-id="aedee-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="aedee-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="aedee-306">Administrador da Proteção de Informações do Azure</span><span class="sxs-lookup"><span data-stu-id="aedee-306">Azure Information Protection administrator</span></span> | <span data-ttu-id="aedee-307">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="aedee-307">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="aedee-308">Administrador do conjunto de chaves B2C IEF</span><span class="sxs-lookup"><span data-stu-id="aedee-308">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="aedee-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="aedee-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="aedee-310">Administrador da política IEF B2C</span><span class="sxs-lookup"><span data-stu-id="aedee-310">B2C IEF Policy administrator</span></span> | <span data-ttu-id="aedee-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="aedee-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="aedee-312">Administrador de fluxo de usuário B2C</span><span class="sxs-lookup"><span data-stu-id="aedee-312">B2C user flow administrator</span></span> | <span data-ttu-id="aedee-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="aedee-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="aedee-314">Administrador de atributos de fluxo de usuário B2C</span><span class="sxs-lookup"><span data-stu-id="aedee-314">B2C user flow attribute administrator</span></span> | <span data-ttu-id="aedee-315">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="aedee-315">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="aedee-316">Administrador de cobrança</span><span class="sxs-lookup"><span data-stu-id="aedee-316">Billing administrator</span></span> | <span data-ttu-id="aedee-317">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="aedee-317">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="aedee-318">Administrador de aplicativos de nuvem</span><span class="sxs-lookup"><span data-stu-id="aedee-318">Cloud application administrator</span></span> | <span data-ttu-id="aedee-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="aedee-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="aedee-320">Administrador de dispositivo em nuvem</span><span class="sxs-lookup"><span data-stu-id="aedee-320">Cloud device administrator</span></span> | <span data-ttu-id="aedee-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="aedee-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="aedee-322">Administrador de conformidade</span><span class="sxs-lookup"><span data-stu-id="aedee-322">Compliance administrator</span></span> | <span data-ttu-id="aedee-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="aedee-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="aedee-324">Administrador de dados de conformidade</span><span class="sxs-lookup"><span data-stu-id="aedee-324">Compliance data administrator</span></span> | <span data-ttu-id="aedee-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="aedee-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="aedee-326">Administrador de acesso condicional</span><span class="sxs-lookup"><span data-stu-id="aedee-326">Conditional Access administrator</span></span> | <span data-ttu-id="aedee-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="aedee-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="aedee-328">Aprovador de acesso do Sistema de Proteção de Dados do Cliente</span><span class="sxs-lookup"><span data-stu-id="aedee-328">Customer LockBox access approver</span></span> | <span data-ttu-id="aedee-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="aedee-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="aedee-330">Administrador do desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="aedee-330">Desktop Analytics administrator</span></span> | <span data-ttu-id="aedee-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="aedee-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="aedee-332">Leitores de diretórios</span><span class="sxs-lookup"><span data-stu-id="aedee-332">Directory readers</span></span> | <span data-ttu-id="aedee-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="aedee-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="aedee-334">Administrador do Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="aedee-334">Dynamics 365 administrator</span></span> | <span data-ttu-id="aedee-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="aedee-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="aedee-336">Administradores do Exchange</span><span class="sxs-lookup"><span data-stu-id="aedee-336">Exchange administrator</span></span> | <span data-ttu-id="aedee-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="aedee-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="aedee-338">IdentityAdministrador de provedor externo</span><span class="sxs-lookup"><span data-stu-id="aedee-338">External Identity Provider administrator</span></span> | <span data-ttu-id="aedee-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="aedee-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="aedee-340">Administrador global</span><span class="sxs-lookup"><span data-stu-id="aedee-340">Global administrator</span></span> | <span data-ttu-id="aedee-341">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="aedee-341">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="aedee-342">Leitor global</span><span class="sxs-lookup"><span data-stu-id="aedee-342">Global reader</span></span> | <span data-ttu-id="aedee-343">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="aedee-343">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="aedee-344">Administrador de grupos</span><span class="sxs-lookup"><span data-stu-id="aedee-344">Groups administrator</span></span> | <span data-ttu-id="aedee-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="aedee-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="aedee-346">Emissor do convite ao convidado</span><span class="sxs-lookup"><span data-stu-id="aedee-346">Guest inviter</span></span> | <span data-ttu-id="aedee-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="aedee-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="aedee-348">Administrador de assistência técnica</span><span class="sxs-lookup"><span data-stu-id="aedee-348">Helpdesk administrator</span></span> | <span data-ttu-id="aedee-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="aedee-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="aedee-350">Administrador do Intune</span><span class="sxs-lookup"><span data-stu-id="aedee-350">Intune administrator</span></span> | <span data-ttu-id="aedee-351">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="aedee-351">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="aedee-352">Administrador do Kaizala</span><span class="sxs-lookup"><span data-stu-id="aedee-352">Kaizala administrator</span></span> | <span data-ttu-id="aedee-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="aedee-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="aedee-354">Administrador de licenças</span><span class="sxs-lookup"><span data-stu-id="aedee-354">License administrator</span></span> | <span data-ttu-id="aedee-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="aedee-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="aedee-356">Leitor de privacidade do centro de mensagens</span><span class="sxs-lookup"><span data-stu-id="aedee-356">Message center privacy reader</span></span> | <span data-ttu-id="aedee-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="aedee-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="aedee-358">Leitor do Centro de Mensagens</span><span class="sxs-lookup"><span data-stu-id="aedee-358">Message center reader</span></span> | <span data-ttu-id="aedee-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="aedee-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="aedee-360">Administrador de aplicativos do Office</span><span class="sxs-lookup"><span data-stu-id="aedee-360">Office apps administrator</span></span> | <span data-ttu-id="aedee-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="aedee-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="aedee-362">Administrador de senha</span><span class="sxs-lookup"><span data-stu-id="aedee-362">Password administrator</span></span> | <span data-ttu-id="aedee-363">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="aedee-363">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="aedee-364">Administrador do Power BI</span><span class="sxs-lookup"><span data-stu-id="aedee-364">Power BI administrator</span></span> | <span data-ttu-id="aedee-365">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="aedee-365">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="aedee-366">Administrador do Power Platform</span><span class="sxs-lookup"><span data-stu-id="aedee-366">Power platform administrator</span></span> | <span data-ttu-id="aedee-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="aedee-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="aedee-368">Administrador de autenticação privilegiada</span><span class="sxs-lookup"><span data-stu-id="aedee-368">Privileged authentication administrator</span></span> | <span data-ttu-id="aedee-369">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="aedee-369">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="aedee-370">Administrador de função com privilégios</span><span class="sxs-lookup"><span data-stu-id="aedee-370">Privileged role administrator</span></span> | <span data-ttu-id="aedee-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="aedee-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="aedee-372">Leitor de relatórios</span><span class="sxs-lookup"><span data-stu-id="aedee-372">Reports reader</span></span> | <span data-ttu-id="aedee-373">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="aedee-373">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="aedee-374">Administrador de pesquisas</span><span class="sxs-lookup"><span data-stu-id="aedee-374">Search administrator</span></span> | <span data-ttu-id="aedee-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="aedee-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="aedee-376">Editor de pesquisa</span><span class="sxs-lookup"><span data-stu-id="aedee-376">Search editor</span></span> | <span data-ttu-id="aedee-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="aedee-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="aedee-378">Administrador de segurança</span><span class="sxs-lookup"><span data-stu-id="aedee-378">Security administrator</span></span> | <span data-ttu-id="aedee-379">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="aedee-379">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="aedee-380">Operador de segurança</span><span class="sxs-lookup"><span data-stu-id="aedee-380">Security operator</span></span> | <span data-ttu-id="aedee-381">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="aedee-381">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="aedee-382">Leitor de segurança</span><span class="sxs-lookup"><span data-stu-id="aedee-382">Security reader</span></span> | <span data-ttu-id="aedee-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="aedee-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="aedee-384">Administrador de suporte a serviço</span><span class="sxs-lookup"><span data-stu-id="aedee-384">Service support administrator</span></span> | <span data-ttu-id="aedee-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="aedee-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="aedee-386">Administrador do SharePoint</span><span class="sxs-lookup"><span data-stu-id="aedee-386">SharePoint administrator</span></span> | <span data-ttu-id="aedee-387">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="aedee-387">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="aedee-388">Administrador do Skype for Business</span><span class="sxs-lookup"><span data-stu-id="aedee-388">Skype for Business administrator</span></span> | <span data-ttu-id="aedee-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="aedee-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="aedee-390">Administrador de Comunicações do Teams</span><span class="sxs-lookup"><span data-stu-id="aedee-390">Teams Communications Administrator</span></span> | <span data-ttu-id="aedee-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="aedee-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="aedee-392">Engenheiro de Suporte de Comunicações do Teams</span><span class="sxs-lookup"><span data-stu-id="aedee-392">Teams Communications Support Engineer</span></span> | <span data-ttu-id="aedee-393">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="aedee-393">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="aedee-394">Especialista em comunicações de equipes</span><span class="sxs-lookup"><span data-stu-id="aedee-394">Teams Communications Specialist</span></span> | <span data-ttu-id="aedee-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="aedee-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="aedee-396">Administrador de Serviços do Teams</span><span class="sxs-lookup"><span data-stu-id="aedee-396">Teams Service Administrator</span></span> | <span data-ttu-id="aedee-397">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="aedee-397">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="aedee-398">Administrador de usuários</span><span class="sxs-lookup"><span data-stu-id="aedee-398">User administrator</span></span> | <span data-ttu-id="aedee-399">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="aedee-399">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aedee-400">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="aedee-400">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
