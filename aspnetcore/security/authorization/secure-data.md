---
title: Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização
author: rick-anderson
description: Saiba como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização. Inclui HTTPS, autenticação, segurança, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689299"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="093e6-104">Criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="093e6-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="093e6-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="093e6-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="093e6-106">Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="093e6-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="093e6-107">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="093e6-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="093e6-108">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="093e6-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="093e6-109">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="093e6-109">There are three security groups:</span></span>

* <span data-ttu-id="093e6-110">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="093e6-111">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="093e6-112">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="093e6-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="093e6-113">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="093e6-114">As imagens neste documento não correspondem exatamente aos modelos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="093e6-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="093e6-115">Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado.</span><span class="sxs-lookup"><span data-stu-id="093e6-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="093e6-116">Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="093e6-117">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="093e6-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="093e6-118">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="093e6-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="093e6-120">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="093e6-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

<span data-ttu-id="093e6-122">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="093e6-122">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="093e6-124">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="093e6-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="093e6-125">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="093e6-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

<span data-ttu-id="093e6-127">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="093e6-127">The administrator has all privileges.</span></span> <span data-ttu-id="093e6-128">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="093e6-129">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="093e6-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="093e6-130">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="093e6-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="093e6-131">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="093e6-132">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="093e6-133">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="093e6-134">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="093e6-134">Prerequisites</span></span>

<span data-ttu-id="093e6-135">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="093e6-135">This tutorial is advanced.</span></span> <span data-ttu-id="093e6-136">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="093e6-136">You should be familiar with:</span></span>

* [<span data-ttu-id="093e6-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="093e6-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="093e6-138">Autenticação</span><span class="sxs-lookup"><span data-stu-id="093e6-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="093e6-139">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="093e6-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="093e6-140">Autorização</span><span class="sxs-lookup"><span data-stu-id="093e6-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="093e6-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="093e6-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="093e6-142">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="093e6-142">The starter and completed app</span></span>

<span data-ttu-id="093e6-143">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="093e6-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="093e6-144">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="093e6-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="093e6-145">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="093e6-145">The starter app</span></span>

<span data-ttu-id="093e6-146">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="093e6-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="093e6-147">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="093e6-148">Para criar o aplicativo inicial, consulte [criar o aplicativo inicial](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="093e6-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="093e6-149">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="093e6-149">Secure user data</span></span>

<span data-ttu-id="093e6-150">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="093e6-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="093e6-151">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="093e6-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="093e6-152">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="093e6-152">Tie the contact data to the user</span></span>

<span data-ttu-id="093e6-153">Use a [Identity](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="093e6-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="093e6-154">Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="093e6-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="093e6-155">`OwnerID` é a ID do usuário da `AspNetUser` tabela no banco de [Identity](xref:security/authentication/identity) dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="093e6-156">O `Status` campo determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="093e6-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="093e6-157">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="093e6-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="093e6-158">Adicionar serviços de função a Identity</span><span class="sxs-lookup"><span data-stu-id="093e6-158">Add Role services to Identity</span></span>

<span data-ttu-id="093e6-159">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="093e6-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="093e6-160">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="093e6-160">Require authenticated users</span></span>

<span data-ttu-id="093e6-161">Defina a política de autenticação de fallback para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="093e6-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="093e6-162">O código realçado anterior define a [política de autenticação de fallback](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="093e6-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="093e6-163">A política de autenticação de fallback exige que \**_todos_* os usuários sejam autenticados, exceto por Razor páginas, controladores ou métodos de ação com um atributo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="093e6-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="093e6-164">Por exemplo, Razor páginas, controladores ou métodos de ação com `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` usam o atributo de autenticação aplicado em vez da política de autenticação de fallback.</span><span class="sxs-lookup"><span data-stu-id="093e6-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="093e6-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> Adiciona <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> à instância atual, que impõe que o usuário atual seja autenticado.</span><span class="sxs-lookup"><span data-stu-id="093e6-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="093e6-166">A política de autenticação de fallback:</span><span class="sxs-lookup"><span data-stu-id="093e6-166">The fallback authentication policy:</span></span>

<span data-ttu-id="093e6-167">_ É aplicado a todas as solicitações que não especificam explicitamente uma política de autenticação.</span><span class="sxs-lookup"><span data-stu-id="093e6-167">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="093e6-168">Para solicitações servidas pelo roteamento de ponto de extremidade, isso inclui qualquer ponto de extremidade que não especifica um atributo de autorização.</span><span class="sxs-lookup"><span data-stu-id="093e6-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="093e6-169">Para solicitações servidas por outro middleware após o middleware de autorização, como [arquivos estáticos](xref:fundamentals/static-files), isso aplicaria a política a todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="093e6-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="093e6-170">Definir a política de autenticação de fallback para exigir que os usuários sejam autenticados protege Razor páginas e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="093e6-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="093e6-171">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor páginas para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="093e6-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="093e6-172">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe também contém <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="093e6-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="093e6-173">O `DefaultPolicy` é a política usada com o `[Authorize]` atributo quando nenhuma política é especificada.</span><span class="sxs-lookup"><span data-stu-id="093e6-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="093e6-174">`[Authorize]` Não contém uma política nomeada, ao contrário de `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="093e6-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="093e6-175">Para obter mais informações sobre políticas, consulte <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="093e6-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="093e6-176">Uma maneira alternativa para os controladores e Razor páginas do MVC exigir que todos os usuários sejam autenticados é adicionar um filtro de autorização:</span><span class="sxs-lookup"><span data-stu-id="093e6-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="093e6-177">O código anterior usa um filtro de autorização, a definição da política de fallback usa o roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="093e6-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="093e6-178">Definir a política de fallback é a maneira preferida de exigir que todos os usuários sejam autenticados.</span><span class="sxs-lookup"><span data-stu-id="093e6-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="093e6-179">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às `Index` páginas e `Privacy` para que os usuários anônimos possam obter informações sobre o site antes de se registrarem:</span><span class="sxs-lookup"><span data-stu-id="093e6-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="093e6-180">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="093e6-180">Configure the test account</span></span>

<span data-ttu-id="093e6-181">A `SeedData` classe cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="093e6-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="093e6-182">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="093e6-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="093e6-183">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="093e6-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="093e6-184">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="093e6-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="093e6-185">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="093e6-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="093e6-186">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="093e6-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="093e6-187">Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="093e6-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="093e6-188">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="093e6-189">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="093e6-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="093e6-190">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="093e6-191">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="093e6-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="093e6-192">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="093e6-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="093e6-193">Crie uma `ContactIsOwnerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="093e6-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="093e6-194">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="093e6-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="093e6-195">O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="093e6-196">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="093e6-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="093e6-197">Retornar `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="093e6-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="093e6-198">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="093e6-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="093e6-199">`Task.CompletedTask` Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="093e6-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="093e6-200">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="093e6-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="093e6-201">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="093e6-202">`ContactIsOwnerAuthorizationHandler` Não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="093e6-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="093e6-203">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="093e6-203">Create a manager authorization handler</span></span>

<span data-ttu-id="093e6-204">Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="093e6-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="093e6-205">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="093e6-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="093e6-206">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="093e6-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="093e6-207">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="093e6-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="093e6-208">Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="093e6-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="093e6-209">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="093e6-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="093e6-210">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="093e6-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="093e6-211">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="093e6-211">Register the authorization handlers</span></span>

<span data-ttu-id="093e6-212">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="093e6-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="093e6-213">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [Identity](xref:security/authentication/identity) , que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="093e6-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="093e6-214">Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="093e6-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="093e6-215">Adicione o seguinte código ao final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="093e6-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="093e6-216">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="093e6-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="093e6-217">Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="093e6-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="093e6-218">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="093e6-218">Support authorization</span></span>

<span data-ttu-id="093e6-219">Nesta seção, você atualizará as Razor páginas e adicionará uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="093e6-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="093e6-220">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="093e6-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="093e6-221">Examine a `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="093e6-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="093e6-222">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="093e6-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="093e6-223">Criar uma classe base para as páginas de contatos Razor</span><span class="sxs-lookup"><span data-stu-id="093e6-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="093e6-224">Crie uma classe base que contenha os serviços usados nas páginas de contatos Razor .</span><span class="sxs-lookup"><span data-stu-id="093e6-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="093e6-225">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="093e6-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="093e6-226">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="093e6-226">The preceding code:</span></span>

* <span data-ttu-id="093e6-227">Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="093e6-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="093e6-228">Adiciona o Identity `UserManager` serviço.</span><span class="sxs-lookup"><span data-stu-id="093e6-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="093e6-229">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="093e6-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="093e6-230">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="093e6-230">Update the CreateModel</span></span>

<span data-ttu-id="093e6-231">Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:</span><span class="sxs-lookup"><span data-stu-id="093e6-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="093e6-232">Atualize o `CreateModel.OnPostAsync` método para:</span><span class="sxs-lookup"><span data-stu-id="093e6-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="093e6-233">Adicione a ID de usuário ao `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="093e6-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="093e6-234">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="093e6-235">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="093e6-235">Update the IndexModel</span></span>

<span data-ttu-id="093e6-236">Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="093e6-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="093e6-237">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="093e6-237">Update the EditModel</span></span>

<span data-ttu-id="093e6-238">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="093e6-239">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="093e6-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="093e6-240">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="093e6-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="093e6-241">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="093e6-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="093e6-242">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="093e6-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="093e6-243">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="093e6-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="093e6-244">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="093e6-244">Update the DeleteModel</span></span>

<span data-ttu-id="093e6-245">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="093e6-246">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="093e6-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="093e6-247">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="093e6-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="093e6-248">Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="093e6-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="093e6-249">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="093e6-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="093e6-250">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="093e6-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="093e6-251">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="093e6-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="093e6-252">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="093e6-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="093e6-253">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="093e6-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="093e6-254">A Razor página ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="093e6-255">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="093e6-255">Update Details</span></span>

<span data-ttu-id="093e6-256">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="093e6-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="093e6-257">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="093e6-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="093e6-258">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="093e6-258">Add or remove a user to a role</span></span>

<span data-ttu-id="093e6-259">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="093e6-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="093e6-260">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="093e6-260">Removing privileges from a user.</span></span> <span data-ttu-id="093e6-261">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="093e6-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="093e6-262">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="093e6-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="093e6-263">Diferenças entre desafio e proíba</span><span class="sxs-lookup"><span data-stu-id="093e6-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="093e6-264">Esse aplicativo define a política padrão para [exigir usuários autenticados](#rau).</span><span class="sxs-lookup"><span data-stu-id="093e6-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="093e6-265">O código a seguir permite usuários anônimos.</span><span class="sxs-lookup"><span data-stu-id="093e6-265">The following code allows anonymous users.</span></span> <span data-ttu-id="093e6-266">Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.</span><span class="sxs-lookup"><span data-stu-id="093e6-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="093e6-267">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="093e6-267">In the preceding code:</span></span>

* <span data-ttu-id="093e6-268">Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="093e6-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="093e6-269">Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.</span><span class="sxs-lookup"><span data-stu-id="093e6-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="093e6-270">Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="093e6-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="093e6-271">Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.</span><span class="sxs-lookup"><span data-stu-id="093e6-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="093e6-272">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="093e6-272">Test the completed app</span></span>

<span data-ttu-id="093e6-273">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="093e6-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="093e6-274">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="093e6-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="093e6-275">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="093e6-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="093e6-276">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="093e6-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="093e6-277">Se o aplicativo tiver contatos:</span><span class="sxs-lookup"><span data-stu-id="093e6-277">If the app has contacts:</span></span>

* <span data-ttu-id="093e6-278">Exclua todos os registros na `Contact` tabela.</span><span class="sxs-lookup"><span data-stu-id="093e6-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="093e6-279">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="093e6-280">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="093e6-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="093e6-281">Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="093e6-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="093e6-282">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="093e6-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="093e6-283">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="093e6-283">Verify the following operations:</span></span>

* <span data-ttu-id="093e6-284">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="093e6-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="093e6-285">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="093e6-286">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="093e6-287">A `Details` exibição mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="093e6-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="093e6-288">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="093e6-289">User</span><span class="sxs-lookup"><span data-stu-id="093e6-289">User</span></span>                | <span data-ttu-id="093e6-290">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="093e6-290">Seeded by the app</span></span> | <span data-ttu-id="093e6-291">Opções</span><span class="sxs-lookup"><span data-stu-id="093e6-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="093e6-292">Não</span><span class="sxs-lookup"><span data-stu-id="093e6-292">No</span></span>                | <span data-ttu-id="093e6-293">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="093e6-294">Sim</span><span class="sxs-lookup"><span data-stu-id="093e6-294">Yes</span></span>               | <span data-ttu-id="093e6-295">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="093e6-296">Sim</span><span class="sxs-lookup"><span data-stu-id="093e6-296">Yes</span></span>               | <span data-ttu-id="093e6-297">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="093e6-298">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="093e6-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="093e6-299">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="093e6-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="093e6-300">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="093e6-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="093e6-301">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="093e6-301">Create the starter app</span></span>

* <span data-ttu-id="093e6-302">Criar um Razor aplicativo de páginas chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="093e6-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="093e6-303">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="093e6-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="093e6-304">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="093e6-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="093e6-305">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="093e6-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="093e6-306">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="093e6-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="093e6-307">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="093e6-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="093e6-308">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="093e6-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="093e6-309">Se você tiver um bug com o `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="093e6-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="093e6-310">Atualize a âncora **ContactManager** no arquivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="093e6-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="093e6-311">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="093e6-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="093e6-312">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="093e6-312">Seed the database</span></span>

<span data-ttu-id="093e6-313">Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :</span><span class="sxs-lookup"><span data-stu-id="093e6-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="093e6-314">Chamada `SeedData.Initialize` de `Main` :</span><span class="sxs-lookup"><span data-stu-id="093e6-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="093e6-315">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-315">Test that the app seeded the database.</span></span> <span data-ttu-id="093e6-316">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="093e6-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="093e6-317">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="093e6-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="093e6-318">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="093e6-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="093e6-319">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="093e6-319">There are three security groups:</span></span>

* <span data-ttu-id="093e6-320">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="093e6-321">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="093e6-322">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="093e6-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="093e6-323">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="093e6-324">Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado.</span><span class="sxs-lookup"><span data-stu-id="093e6-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="093e6-325">Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="093e6-326">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="093e6-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="093e6-327">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="093e6-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="093e6-329">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="093e6-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

<span data-ttu-id="093e6-331">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="093e6-331">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="093e6-333">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="093e6-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="093e6-334">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="093e6-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

<span data-ttu-id="093e6-336">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="093e6-336">The administrator has all privileges.</span></span> <span data-ttu-id="093e6-337">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="093e6-338">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="093e6-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="093e6-339">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="093e6-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="093e6-340">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="093e6-341">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="093e6-342">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="093e6-343">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="093e6-343">Prerequisites</span></span>

<span data-ttu-id="093e6-344">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="093e6-344">This tutorial is advanced.</span></span> <span data-ttu-id="093e6-345">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="093e6-345">You should be familiar with:</span></span>

* [<span data-ttu-id="093e6-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="093e6-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="093e6-347">Autenticação</span><span class="sxs-lookup"><span data-stu-id="093e6-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="093e6-348">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="093e6-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="093e6-349">Autorização</span><span class="sxs-lookup"><span data-stu-id="093e6-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="093e6-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="093e6-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="093e6-351">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="093e6-351">The starter and completed app</span></span>

<span data-ttu-id="093e6-352">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="093e6-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="093e6-353">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="093e6-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="093e6-354">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="093e6-354">The starter app</span></span>

<span data-ttu-id="093e6-355">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="093e6-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="093e6-356">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="093e6-357">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="093e6-357">Secure user data</span></span>

<span data-ttu-id="093e6-358">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="093e6-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="093e6-359">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="093e6-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="093e6-360">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="093e6-360">Tie the contact data to the user</span></span>

<span data-ttu-id="093e6-361">Use a [Identity](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="093e6-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="093e6-362">Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="093e6-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="093e6-363">`OwnerID` é a ID do usuário da `AspNetUser` tabela no banco de [Identity](xref:security/authentication/identity) dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="093e6-364">O `Status` campo determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="093e6-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="093e6-365">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="093e6-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="093e6-366">Adicionar serviços de função a Identity</span><span class="sxs-lookup"><span data-stu-id="093e6-366">Add Role services to Identity</span></span>

<span data-ttu-id="093e6-367">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="093e6-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="093e6-368">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="093e6-368">Require authenticated users</span></span>

<span data-ttu-id="093e6-369">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="093e6-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="093e6-370">Você pode recusar a autenticação na Razor página, controlador ou nível de método de ação com o `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="093e6-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="093e6-371">Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege Razor páginas e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="093e6-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="093e6-372">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor páginas para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="093e6-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="093e6-373">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice, sobre e de contato para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="093e6-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="093e6-374">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="093e6-374">Configure the test account</span></span>

<span data-ttu-id="093e6-375">A `SeedData` classe cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="093e6-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="093e6-376">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="093e6-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="093e6-377">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="093e6-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="093e6-378">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="093e6-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="093e6-379">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="093e6-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="093e6-380">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="093e6-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="093e6-381">Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="093e6-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="093e6-382">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="093e6-383">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="093e6-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="093e6-384">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="093e6-385">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="093e6-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="093e6-386">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="093e6-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="093e6-387">Crie uma pasta de *autorização* e crie uma `ContactIsOwnerAuthorizationHandler` classe nela.</span><span class="sxs-lookup"><span data-stu-id="093e6-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="093e6-388">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="093e6-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="093e6-389">O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="093e6-390">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="093e6-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="093e6-391">Retornar `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="093e6-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="093e6-392">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="093e6-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="093e6-393">`Task.CompletedTask` Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="093e6-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="093e6-394">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="093e6-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="093e6-395">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="093e6-396">`ContactIsOwnerAuthorizationHandler` Não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="093e6-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="093e6-397">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="093e6-397">Create a manager authorization handler</span></span>

<span data-ttu-id="093e6-398">Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="093e6-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="093e6-399">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="093e6-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="093e6-400">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="093e6-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="093e6-401">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="093e6-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="093e6-402">Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="093e6-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="093e6-403">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="093e6-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="093e6-404">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="093e6-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="093e6-405">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="093e6-405">Register the authorization handlers</span></span>

<span data-ttu-id="093e6-406">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="093e6-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="093e6-407">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [Identity](xref:security/authentication/identity) , que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="093e6-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="093e6-408">Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="093e6-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="093e6-409">Adicione o seguinte código ao final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="093e6-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="093e6-410">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="093e6-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="093e6-411">Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="093e6-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="093e6-412">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="093e6-412">Support authorization</span></span>

<span data-ttu-id="093e6-413">Nesta seção, você atualizará as Razor páginas e adicionará uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="093e6-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="093e6-414">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="093e6-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="093e6-415">Examine a `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="093e6-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="093e6-416">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="093e6-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="093e6-417">Criar uma classe base para as páginas de contatos Razor</span><span class="sxs-lookup"><span data-stu-id="093e6-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="093e6-418">Crie uma classe base que contenha os serviços usados nas páginas de contatos Razor .</span><span class="sxs-lookup"><span data-stu-id="093e6-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="093e6-419">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="093e6-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="093e6-420">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="093e6-420">The preceding code:</span></span>

* <span data-ttu-id="093e6-421">Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="093e6-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="093e6-422">Adiciona o Identity `UserManager` serviço.</span><span class="sxs-lookup"><span data-stu-id="093e6-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="093e6-423">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="093e6-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="093e6-424">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="093e6-424">Update the CreateModel</span></span>

<span data-ttu-id="093e6-425">Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:</span><span class="sxs-lookup"><span data-stu-id="093e6-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="093e6-426">Atualize o `CreateModel.OnPostAsync` método para:</span><span class="sxs-lookup"><span data-stu-id="093e6-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="093e6-427">Adicione a ID de usuário ao `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="093e6-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="093e6-428">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="093e6-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="093e6-429">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="093e6-429">Update the IndexModel</span></span>

<span data-ttu-id="093e6-430">Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="093e6-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="093e6-431">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="093e6-431">Update the EditModel</span></span>

<span data-ttu-id="093e6-432">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="093e6-433">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="093e6-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="093e6-434">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="093e6-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="093e6-435">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="093e6-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="093e6-436">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="093e6-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="093e6-437">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="093e6-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="093e6-438">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="093e6-438">Update the DeleteModel</span></span>

<span data-ttu-id="093e6-439">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="093e6-440">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="093e6-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="093e6-441">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="093e6-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="093e6-442">Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="093e6-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="093e6-443">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="093e6-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="093e6-444">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="093e6-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="093e6-445">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="093e6-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="093e6-446">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="093e6-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="093e6-447">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="093e6-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="093e6-448">A Razor página ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="093e6-449">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="093e6-449">Update Details</span></span>

<span data-ttu-id="093e6-450">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="093e6-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="093e6-451">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="093e6-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="093e6-452">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="093e6-452">Add or remove a user to a role</span></span>

<span data-ttu-id="093e6-453">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="093e6-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="093e6-454">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="093e6-454">Removing privileges from a user.</span></span> <span data-ttu-id="093e6-455">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="093e6-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="093e6-456">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="093e6-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="093e6-457">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="093e6-457">Test the completed app</span></span>

<span data-ttu-id="093e6-458">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="093e6-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="093e6-459">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="093e6-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="093e6-460">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="093e6-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="093e6-461">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="093e6-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="093e6-462">Remover e atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="093e6-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="093e6-463">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="093e6-464">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="093e6-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="093e6-465">Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="093e6-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="093e6-466">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="093e6-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="093e6-467">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="093e6-467">Verify the following operations:</span></span>

* <span data-ttu-id="093e6-468">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="093e6-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="093e6-469">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="093e6-470">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="093e6-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="093e6-471">A `Details` exibição mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="093e6-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="093e6-472">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="093e6-473">User</span><span class="sxs-lookup"><span data-stu-id="093e6-473">User</span></span>                | <span data-ttu-id="093e6-474">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="093e6-474">Seeded by the app</span></span> | <span data-ttu-id="093e6-475">Opções</span><span class="sxs-lookup"><span data-stu-id="093e6-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="093e6-476">Não</span><span class="sxs-lookup"><span data-stu-id="093e6-476">No</span></span>                | <span data-ttu-id="093e6-477">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="093e6-478">Sim</span><span class="sxs-lookup"><span data-stu-id="093e6-478">Yes</span></span>               | <span data-ttu-id="093e6-479">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="093e6-480">Sim</span><span class="sxs-lookup"><span data-stu-id="093e6-480">Yes</span></span>               | <span data-ttu-id="093e6-481">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="093e6-482">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="093e6-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="093e6-483">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="093e6-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="093e6-484">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="093e6-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="093e6-485">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="093e6-485">Create the starter app</span></span>

* <span data-ttu-id="093e6-486">Criar um Razor aplicativo de páginas chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="093e6-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="093e6-487">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="093e6-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="093e6-488">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="093e6-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="093e6-489">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="093e6-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="093e6-490">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="093e6-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="093e6-491">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="093e6-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="093e6-492">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="093e6-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="093e6-493">Atualize a âncora **ContactManager** no arquivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="093e6-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="093e6-494">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="093e6-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="093e6-495">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="093e6-495">Seed the database</span></span>

<span data-ttu-id="093e6-496">Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .</span><span class="sxs-lookup"><span data-stu-id="093e6-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="093e6-497">Chamada `SeedData.Initialize` de `Main` :</span><span class="sxs-lookup"><span data-stu-id="093e6-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="093e6-498">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="093e6-498">Test that the app seeded the database.</span></span> <span data-ttu-id="093e6-499">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="093e6-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="093e6-500">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="093e6-500">Additional resources</span></span>

* [<span data-ttu-id="093e6-501">Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="093e6-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="093e6-502">[ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="093e6-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="093e6-503">Este laboratório apresenta mais detalhes sobre os recursos de segurança apresentados neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="093e6-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="093e6-504">Autorização baseada em política personalizada</span><span class="sxs-lookup"><span data-stu-id="093e6-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
