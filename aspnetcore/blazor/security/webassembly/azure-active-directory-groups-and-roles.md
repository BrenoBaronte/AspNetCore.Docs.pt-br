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
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="c30f4-103">Grupos do Azure Active Directory (AAD), funções de administrador e funções de aplicativo</span><span class="sxs-lookup"><span data-stu-id="c30f4-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="c30f4-104">De [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="c30f4-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="c30f4-105">Este artigo se aplica a Blazor ASP.NET Core aplicativos versão 3,1 com o Microsoft Identity 1,0 e será atualizado para 5,0 com Identity 2,0 em breve.</span><span class="sxs-lookup"><span data-stu-id="c30f4-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="c30f4-106">Para obter mais informações, consulte o seguinte problema do GitHub e solicitação de pull.</span><span class="sxs-lookup"><span data-stu-id="c30f4-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="c30f4-107">A guia **arquivos alterados** da solicitação pull contém o texto de rascunho e exemplos para as atualizações do artigo.</span><span class="sxs-lookup"><span data-stu-id="c30f4-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="c30f4-108">Após a revisão e as atualizações finais, a solicitação de pull será mesclada no conjunto de documentação ao vivo.</span><span class="sxs-lookup"><span data-stu-id="c30f4-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="c30f4-109">Problema: [ Blazor WASM com grupos e funções do AAD (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="c30f4-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="c30f4-110">Solicitação de pull: [ Blazor tópico sobre grupos e funções do AAD 5,0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="c30f4-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="c30f4-111">Azure Active Directory (AAD) fornece várias abordagens de autorização que podem ser combinadas com ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="c30f4-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="c30f4-112">Grupos</span><span class="sxs-lookup"><span data-stu-id="c30f4-112">Groups</span></span>
  * <span data-ttu-id="c30f4-113">Segurança</span><span class="sxs-lookup"><span data-stu-id="c30f4-113">Security</span></span>
  * <span data-ttu-id="c30f4-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="c30f4-114">Microsoft 365</span></span>
  * <span data-ttu-id="c30f4-115">Distribuição</span><span class="sxs-lookup"><span data-stu-id="c30f4-115">Distribution</span></span>
* <span data-ttu-id="c30f4-116">Funções</span><span class="sxs-lookup"><span data-stu-id="c30f4-116">Roles</span></span>
  * <span data-ttu-id="c30f4-117">Funções de administrador do AAD</span><span class="sxs-lookup"><span data-stu-id="c30f4-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="c30f4-118">Funções de aplicativo</span><span class="sxs-lookup"><span data-stu-id="c30f4-118">App Roles</span></span>

<span data-ttu-id="c30f4-119">As diretrizes neste artigo se aplicam aos Blazor WebAssembly cenários de implantação do AAD descritos nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="c30f4-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="c30f4-120">Aplicativo autônomo com contas Microsoft</span><span class="sxs-lookup"><span data-stu-id="c30f4-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="c30f4-121">Aplicativo autônomo com o AAD</span><span class="sxs-lookup"><span data-stu-id="c30f4-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="c30f4-122">Aplicativo hospedado com o AAD</span><span class="sxs-lookup"><span data-stu-id="c30f4-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="c30f4-123">As diretrizes do artigo fornecem instruções para aplicativos cliente e servidor:</span><span class="sxs-lookup"><span data-stu-id="c30f4-123">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="c30f4-124">**Cliente**: aplicativos autônomos Blazor WebAssembly ou o *`Client`* aplicativo de uma Blazor solução hospedada.</span><span class="sxs-lookup"><span data-stu-id="c30f4-124">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="c30f4-125">**Servidor**: aplicativos de API Web/API de servidor ASP.NET Core autônomo ou o *`Server`* aplicativo de uma solução hospedada Blazor .</span><span class="sxs-lookup"><span data-stu-id="c30f4-125">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="c30f4-126">Escopos</span><span class="sxs-lookup"><span data-stu-id="c30f4-126">Scopes</span></span>

<span data-ttu-id="c30f4-127">Para permitir chamadas de [API de Microsoft Graph](/graph/use-the-api) para o perfil do usuário, atribuição de função e dados de associação de grupo, o **cliente** é configurado com a `https://graph.microsoft.com/User.Read` permissão () [API do Graph (escopo)](/graph/permissions-reference) no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="c30f4-127">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="c30f4-128">Um aplicativo de **servidor** que chama API do Graph para dados de associação de grupo e função é fornecido `GroupMember.Read.All` ( `https://graph.microsoft.com/GroupMember.Read.All` ) [API do Graph permissão (escopo)](/graph/permissions-reference) no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="c30f4-128">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="c30f4-129">Esses escopos são necessários além dos escopos necessários nos cenários de implantação do AAD descritos pelos tópicos listados na primeira seção deste artigo.</span><span class="sxs-lookup"><span data-stu-id="c30f4-129">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="c30f4-130">As palavras "Permission" e "Scope" são usadas de forma intercambiável no portal do Azure e em vários conjuntos de documentação da Microsoft e externos.</span><span class="sxs-lookup"><span data-stu-id="c30f4-130">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="c30f4-131">Este artigo usa a palavra "Scope" para as permissões atribuídas a um aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="c30f4-131">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="c30f4-132">Atributo de declarações de associação de grupo</span><span class="sxs-lookup"><span data-stu-id="c30f4-132">Group Membership Claims attribute</span></span>

<span data-ttu-id="c30f4-133">No manifesto do aplicativo no portal do Azure para aplicativos **cliente** e **servidor** , defina o [ `groupMembershipClaims` atributo](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) como `All` .</span><span class="sxs-lookup"><span data-stu-id="c30f4-133">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="c30f4-134">Um valor `All` resulta na obtenção de todos os grupos de segurança, grupos de distribuição e funções dos quais o usuário conectado é membro.</span><span class="sxs-lookup"><span data-stu-id="c30f4-134">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="c30f4-135">Abra o registro de portal do Azure do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c30f4-135">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="c30f4-136">Selecione **gerenciar**  >  **manifesto** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="c30f4-136">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="c30f4-137">Localize o `groupMembershipClaims` atributo.</span><span class="sxs-lookup"><span data-stu-id="c30f4-137">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="c30f4-138">Defina o valor como `All`.</span><span class="sxs-lookup"><span data-stu-id="c30f4-138">Set the value to `All`.</span></span>
1. <span data-ttu-id="c30f4-139">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="c30f4-139">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="c30f4-140">Conta de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="c30f4-140">Custom user account</span></span>

<span data-ttu-id="c30f4-141">Atribua usuários a grupos de segurança do AAD e funções de administrador do AAD no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="c30f4-141">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="c30f4-142">Os exemplos neste artigo:</span><span class="sxs-lookup"><span data-stu-id="c30f4-142">The examples in this article:</span></span>

* <span data-ttu-id="c30f4-143">Suponha que um usuário seja atribuído à função de *administrador de cobrança* do AAD no locatário portal do Azure AAD para autorização para acessar os dados da API do servidor.</span><span class="sxs-lookup"><span data-stu-id="c30f4-143">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="c30f4-144">Use [políticas de autorização](xref:security/authorization/policies) para controlar o acesso nos aplicativos **cliente** e **servidor** .</span><span class="sxs-lookup"><span data-stu-id="c30f4-144">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="c30f4-145">No aplicativo **cliente** , estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir propriedades para:</span><span class="sxs-lookup"><span data-stu-id="c30f4-145">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="c30f4-146">`Roles`: Matriz de funções de aplicativo do AAD (abordada na seção [funções de aplicativo](#app-roles) )</span><span class="sxs-lookup"><span data-stu-id="c30f4-146">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="c30f4-147">`Wids`: Funções de administrador do AAD em [declarações de IDs conhecidas ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="c30f4-147">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="c30f4-148">`Oid`: [Declaração de identificador de objeto imutável ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (identifica exclusivamente um usuário dentro e entre locatários)</span><span class="sxs-lookup"><span data-stu-id="c30f4-148">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="c30f4-149">Atribua uma matriz vazia a cada propriedade de matriz para que a verificação de `null` não seja necessária quando essas propriedades forem usadas em `foreach` loops.</span><span class="sxs-lookup"><span data-stu-id="c30f4-149">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="c30f4-150">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="c30f4-150">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="c30f4-151">Adicione uma referência de pacote ao arquivo de projeto do aplicativo **cliente** para o [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="c30f4-151">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="c30f4-152">Adicione as classes e a configuração do utilitário SDK do Graph na seção *SDK do Graph* do <xref:blazor/security/webassembly/graph-api#graph-sdk> artigo.</span><span class="sxs-lookup"><span data-stu-id="c30f4-152">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="c30f4-153">Na `GraphClientExtensions` classe, especifique o `User.Read` escopo para o token de acesso no `AuthenticateRequestAsync` método:</span><span class="sxs-lookup"><span data-stu-id="c30f4-153">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="c30f4-154">Adicione a seguinte fábrica de conta de usuário personalizada ao aplicativo **cliente** .</span><span class="sxs-lookup"><span data-stu-id="c30f4-154">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="c30f4-155">A fábrica de usuário personalizada é usada para estabelecer:</span><span class="sxs-lookup"><span data-stu-id="c30f4-155">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="c30f4-156">Declarações de função de aplicativo ( `appRole` ) (cobertas na seção [funções de aplicativo](#app-roles) )</span><span class="sxs-lookup"><span data-stu-id="c30f4-156">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="c30f4-157">Declarações de função de administrador do AAD ( `directoryRole` )</span><span class="sxs-lookup"><span data-stu-id="c30f4-157">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="c30f4-158">Uma declaração de dados de perfil de usuário de exemplo para o número de telefone celular do usuário ( `mobilePhone` )</span><span class="sxs-lookup"><span data-stu-id="c30f4-158">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="c30f4-159">Declarações de grupo do AAD ( `directoryGroup` )</span><span class="sxs-lookup"><span data-stu-id="c30f4-159">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="c30f4-160">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="c30f4-160">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="c30f4-161">O código anterior não inclui associações transitivas.</span><span class="sxs-lookup"><span data-stu-id="c30f4-161">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="c30f4-162">Se o aplicativo exigir declarações de associação de grupo diretas e transitivas, substitua a `MemberOf` Propriedade ( `IUserMemberOfCollectionWithReferencesRequestBuilder` ) por `TransitiveMemberOf` ( `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` ).</span><span class="sxs-lookup"><span data-stu-id="c30f4-162">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="c30f4-163">O código anterior ignora as declarações de associação de grupo ( `groups` ) que são funções de administrador do AAD ( `#microsoft.graph.directoryRole` tipo) porque os valores de GUID retornados pela Identity plataforma Microsoft 2,0 são **IDs de entidade** da função de administrador do AAD e não IDs de modelo de [**função**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span><span class="sxs-lookup"><span data-stu-id="c30f4-163">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="c30f4-164">As IDs de entidade não são estáveis entre locatários no Microsoft Identity Platform 2,0 e não devem ser usadas para criar políticas de autorização para usuários em aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c30f4-164">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="c30f4-165">Sempre use **IDs de modelo de função** para funções de administrador do AAD **fornecidas por `wids` declarações**.</span><span class="sxs-lookup"><span data-stu-id="c30f4-165">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="c30f4-166">No `Program.Main` aplicativo **cliente** , configure a autenticação MSAL para usar o alocador de conta de usuário personalizado.</span><span class="sxs-lookup"><span data-stu-id="c30f4-166">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="c30f4-167">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="c30f4-167">`Program.cs`:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="c30f4-168">Configuração de autorização</span><span class="sxs-lookup"><span data-stu-id="c30f4-168">Authorization configuration</span></span>

<span data-ttu-id="c30f4-169">No aplicativo **cliente** , crie uma [política](xref:security/authorization/policies) para cada [função de aplicativo](#app-roles), função de administrador do AAD ou grupo de segurança no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="c30f4-169">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="c30f4-170">O exemplo a seguir cria uma política para a função de *administrador de cobrança* do AAD:</span><span class="sxs-lookup"><span data-stu-id="c30f4-170">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="c30f4-171">Para obter a lista completa de IDs para funções de administrador do AAD, consulte [IDs de modelo de função](/azure/active-directory/roles/permissions-reference#role-template-ids) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="c30f4-171">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="c30f4-172">Para obter mais informações sobre políticas de autorização, consulte <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="c30f4-172">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="c30f4-173">Nos exemplos a seguir, o aplicativo **cliente** usa a política anterior para autorizar o usuário.</span><span class="sxs-lookup"><span data-stu-id="c30f4-173">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="c30f4-174">O [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funciona com a política:</span><span class="sxs-lookup"><span data-stu-id="c30f4-174">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="c30f4-175">O acesso a um componente inteiro pode ser baseado na política usando uma [ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="c30f4-175">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="c30f4-176">Se o usuário não estiver conectado, ele será redirecionado para a página de entrada do AAD e, em seguida, de volta para o componente depois de entrar.</span><span class="sxs-lookup"><span data-stu-id="c30f4-176">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="c30f4-177">Uma verificação de política também pode ser [executada no código com lógica de procedimento](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="c30f4-177">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="c30f4-178">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="c30f4-178">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="c30f4-179">Autorizar acesso de API de servidor/API Web</span><span class="sxs-lookup"><span data-stu-id="c30f4-179">Authorize server API/web API access</span></span>

<span data-ttu-id="c30f4-180">Um aplicativo de API de **servidor** pode autorizar os usuários a acessar pontos de extremidade de API segura com [políticas de autorização](xref:security/authorization/policies) para grupos de segurança, funções de administrador do AAD e funções de aplicativo quando um token de acesso contém `groups` `wids` declarações, e `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` .</span><span class="sxs-lookup"><span data-stu-id="c30f4-180">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="c30f4-181">O exemplo a seguir cria uma política para a função de *administrador de cobrança* do AAD no `Startup.ConfigureServices` usando as `wids` declarações (IDs conhecidas do modelo de função/IDs):</span><span class="sxs-lookup"><span data-stu-id="c30f4-181">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="c30f4-182">Para obter a lista completa de IDs para funções de administrador do AAD, consulte [IDs de modelo de função](/azure/active-directory/roles/permissions-reference#role-template-ids) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="c30f4-182">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="c30f4-183">Para obter mais informações sobre políticas de autorização, consulte <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="c30f4-183">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="c30f4-184">O acesso a um controlador no aplicativo do **servidor** pode ser baseado no uso de um [ `[Authorize]` atributo](xref:security/authorization/simple) com o nome da política (documentação da API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).</span><span class="sxs-lookup"><span data-stu-id="c30f4-184">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="c30f4-185">O exemplo a seguir limita o acesso a dados de cobrança do `BillingDataController` para administradores de cobrança do Azure com um nome de política `BillingAdministrator` :</span><span class="sxs-lookup"><span data-stu-id="c30f4-185">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

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

<span data-ttu-id="c30f4-186">Para obter mais informações, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="c30f4-186">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="c30f4-187">Funções de aplicativo</span><span class="sxs-lookup"><span data-stu-id="c30f4-187">App Roles</span></span>

<span data-ttu-id="c30f4-188">Para configurar o aplicativo no portal do Azure para fornecer declarações de associação de funções de aplicativo, consulte [como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="c30f4-188">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="c30f4-189">O exemplo a seguir pressupõe que os aplicativos **cliente** e **servidor** estejam configurados com duas funções e que as funções sejam atribuídas a um usuário de teste:</span><span class="sxs-lookup"><span data-stu-id="c30f4-189">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="c30f4-190">Ao desenvolver um Blazor WebAssembly aplicativo hospedado ou um par cliente-servidor de aplicativos autônomos (um Blazor WebAssembly aplicativo autônomo e um aplicativo de API Web/API de servidor ASP.NET Core autônomo), a `appRoles` propriedade de manifesto do cliente e do servidor portal do Azure registros de aplicativo deve incluir as mesmas funções configuradas.</span><span class="sxs-lookup"><span data-stu-id="c30f4-190">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="c30f4-191">Depois de estabelecer as funções no manifesto do aplicativo cliente, copie-as em sua totalidade para o manifesto do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="c30f4-191">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="c30f4-192">Se você não espelhar o manifesto `appRoles` entre os registros do aplicativo cliente e do servidor, as declarações de função não serão estabelecidas para usuários autenticados da API da Web/API do servidor, mesmo que seu token de acesso tenha as declarações de funções corretas.</span><span class="sxs-lookup"><span data-stu-id="c30f4-192">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="c30f4-193">Embora não seja possível atribuir funções a grupos sem uma conta de Azure AD Premium, você pode atribuir funções a usuários e receber uma declaração de funções para usuários com uma conta padrão do Azure.</span><span class="sxs-lookup"><span data-stu-id="c30f4-193">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="c30f4-194">As diretrizes nesta seção não exigem uma conta do AAD Premium.</span><span class="sxs-lookup"><span data-stu-id="c30f4-194">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="c30f4-195">Várias funções são atribuídas no portal do Azure ao **_adicionar novamente um usuário_** para cada atribuição de função adicional.</span><span class="sxs-lookup"><span data-stu-id="c30f4-195">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="c30f4-196">O `CustomAccountFactory` mostrado na seção [conta de usuário personalizada](#custom-user-account) é configurado para agir em uma `roles` declaração com um valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="c30f4-196">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="c30f4-197">Adicione e registre o `CustomAccountFactory` no aplicativo **cliente** , conforme mostrado na seção [conta de usuário personalizada](#custom-user-account) .</span><span class="sxs-lookup"><span data-stu-id="c30f4-197">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="c30f4-198">Não é necessário fornecer código para remover a `roles` declaração original porque ela é automaticamente removida pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="c30f4-198">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="c30f4-199">Em `Program.Main` um aplicativo **cliente** , especifique a declaração denominada " `appRole` " como a declaração de função para <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> verificações:</span><span class="sxs-lookup"><span data-stu-id="c30f4-199">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="c30f4-200">Se você preferir usar a `directoryRoles` declaração (adicionar funções de administrador), atribua " `directoryRoles` " ao <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c30f4-200">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="c30f4-201">Em `Startup.ConfigureServices` de um aplicativo de **servidor** , especifique a declaração denominada " `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` " como a declaração de função para <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> verificações:</span><span class="sxs-lookup"><span data-stu-id="c30f4-201">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

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
> <span data-ttu-id="c30f4-202">Se você preferir usar a `wids` declaração (adicionar funções de administrador), atribua " `wids` " ao <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="c30f4-202">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="c30f4-203">As abordagens de autorização de componente são funcionais neste ponto.</span><span class="sxs-lookup"><span data-stu-id="c30f4-203">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="c30f4-204">Qualquer um dos mecanismos de autorização nos componentes do aplicativo **cliente** pode usar a `admin` função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="c30f4-204">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="c30f4-205">`AuthorizeView` componente</span><span class="sxs-lookup"><span data-stu-id="c30f4-205">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="c30f4-206">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="c30f4-206">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="c30f4-207">Lógica de procedimento</span><span class="sxs-lookup"><span data-stu-id="c30f4-207">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="c30f4-208">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="c30f4-208">Multiple role tests are supported:</span></span>

* <span data-ttu-id="c30f4-209">Exigir **que o usuário esteja na** `admin` função **ou** `developer` com o `AuthorizeView` componente:</span><span class="sxs-lookup"><span data-stu-id="c30f4-209">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="c30f4-210">Exigir que o usuário **esteja nas** `admin`  `developer` funções e com o `AuthorizeView` componente:</span><span class="sxs-lookup"><span data-stu-id="c30f4-210">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="c30f4-211">Exija **que o usuário esteja na** `admin` função **ou** `developer` com o `[Authorize]` atributo:</span><span class="sxs-lookup"><span data-stu-id="c30f4-211">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="c30f4-212">Exija que o usuário **esteja nas** `admin`  `developer` funções e com o `[Authorize]` atributo:</span><span class="sxs-lookup"><span data-stu-id="c30f4-212">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="c30f4-213">Exigir **que o usuário esteja na** `admin` função **ou** `developer` com o código de procedimento:</span><span class="sxs-lookup"><span data-stu-id="c30f4-213">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="c30f4-214">Exija que o usuário **esteja nas** `admin` funções **e** `developer` com código de procedimento alterando o [condicional ou ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) para uma [condicional e ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="c30f4-214">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="c30f4-215">Qualquer um dos mecanismos de autorização nos controladores do aplicativo de **servidor** pode usar a `admin` função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="c30f4-215">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="c30f4-216">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="c30f4-216">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="c30f4-217">Lógica de procedimento</span><span class="sxs-lookup"><span data-stu-id="c30f4-217">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="c30f4-218">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="c30f4-218">Multiple role tests are supported:</span></span>

* <span data-ttu-id="c30f4-219">Exija **que o usuário esteja na** `admin` função **ou** `developer` com o `[Authorize]` atributo:</span><span class="sxs-lookup"><span data-stu-id="c30f4-219">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="c30f4-220">Exija que o usuário **esteja nas** `admin`  `developer` funções e com o `[Authorize]` atributo:</span><span class="sxs-lookup"><span data-stu-id="c30f4-220">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="c30f4-221">Exigir **que o usuário esteja na** `admin` função **ou** `developer` com o código de procedimento:</span><span class="sxs-lookup"><span data-stu-id="c30f4-221">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="c30f4-222">Exija que o usuário **esteja nas** `admin` funções **e** `developer` com código de procedimento alterando o [condicional ou ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) para uma [condicional e ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="c30f4-222">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="c30f4-223">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c30f4-223">Additional resources</span></span>

* [<span data-ttu-id="c30f4-224">IDs de modelo de função (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="c30f4-224">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="c30f4-225">`groupMembershipClaims` atributo (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="c30f4-225">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="c30f4-226">Como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="c30f4-226">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="c30f4-227">Funções de aplicativo (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="c30f4-227">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
