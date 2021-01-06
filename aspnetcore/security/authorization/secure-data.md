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
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854646"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="151dd-104">Criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="151dd-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="151dd-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="151dd-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="151dd-106">Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="151dd-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="151dd-107">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="151dd-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="151dd-108">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="151dd-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="151dd-109">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="151dd-109">There are three security groups:</span></span>

* <span data-ttu-id="151dd-110">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="151dd-111">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="151dd-112">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="151dd-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="151dd-113">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="151dd-114">As imagens neste documento não correspondem exatamente aos modelos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="151dd-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="151dd-115">Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado.</span><span class="sxs-lookup"><span data-stu-id="151dd-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="151dd-116">Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="151dd-117">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="151dd-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="151dd-118">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="151dd-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="151dd-120">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="151dd-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

<span data-ttu-id="151dd-122">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="151dd-122">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="151dd-124">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="151dd-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="151dd-125">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="151dd-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

<span data-ttu-id="151dd-127">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="151dd-127">The administrator has all privileges.</span></span> <span data-ttu-id="151dd-128">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="151dd-129">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="151dd-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="151dd-130">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="151dd-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="151dd-131">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="151dd-132">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="151dd-133">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="151dd-134">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="151dd-134">Prerequisites</span></span>

<span data-ttu-id="151dd-135">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="151dd-135">This tutorial is advanced.</span></span> <span data-ttu-id="151dd-136">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="151dd-136">You should be familiar with:</span></span>

* [<span data-ttu-id="151dd-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="151dd-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="151dd-138">Autenticação</span><span class="sxs-lookup"><span data-stu-id="151dd-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="151dd-139">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="151dd-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="151dd-140">Autorização</span><span class="sxs-lookup"><span data-stu-id="151dd-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="151dd-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="151dd-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="151dd-142">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="151dd-142">The starter and completed app</span></span>

<span data-ttu-id="151dd-143">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="151dd-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="151dd-144">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="151dd-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="151dd-145">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="151dd-145">The starter app</span></span>

<span data-ttu-id="151dd-146">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="151dd-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="151dd-147">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="151dd-148">Para criar o aplicativo inicial, consulte [criar o aplicativo inicial](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="151dd-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="151dd-149">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="151dd-149">Secure user data</span></span>

<span data-ttu-id="151dd-150">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="151dd-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="151dd-151">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="151dd-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="151dd-152">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="151dd-152">Tie the contact data to the user</span></span>

<span data-ttu-id="151dd-153">Use a [Identity](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="151dd-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="151dd-154">Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="151dd-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="151dd-155">`OwnerID` é a ID do usuário da `AspNetUser` tabela no banco de [Identity](xref:security/authentication/identity) dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="151dd-156">O `Status` campo determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="151dd-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="151dd-157">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="151dd-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="151dd-158">Adicionar serviços de função a Identity</span><span class="sxs-lookup"><span data-stu-id="151dd-158">Add Role services to Identity</span></span>

<span data-ttu-id="151dd-159">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="151dd-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="151dd-160">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="151dd-160">Require authenticated users</span></span>

<span data-ttu-id="151dd-161">Defina a política de autenticação de fallback para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="151dd-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="151dd-162">O código realçado anterior define a [política de autenticação de fallback](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="151dd-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="151dd-163">A política de autenticação de fallback exige que \**_todos_* os usuários sejam autenticados, exceto por Razor páginas, controladores ou métodos de ação com um atributo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="151dd-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="151dd-164">Por exemplo, Razor páginas, controladores ou métodos de ação com `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` usam o atributo de autenticação aplicado em vez da política de autenticação de fallback.</span><span class="sxs-lookup"><span data-stu-id="151dd-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="151dd-165">A política de autenticação de fallback:</span><span class="sxs-lookup"><span data-stu-id="151dd-165">The fallback authentication policy:</span></span>

<span data-ttu-id="151dd-166">_ É aplicado a todas as solicitações que não especificam explicitamente uma política de autenticação.</span><span class="sxs-lookup"><span data-stu-id="151dd-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="151dd-167">Para solicitações servidas pelo roteamento de ponto de extremidade, isso inclui qualquer ponto de extremidade que não especifica um atributo de autorização.</span><span class="sxs-lookup"><span data-stu-id="151dd-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="151dd-168">Para solicitações servidas por outro middleware após o middleware de autorização, como [arquivos estáticos](xref:fundamentals/static-files), isso aplicaria a política a todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="151dd-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="151dd-169">Definir a política de autenticação de fallback para exigir que os usuários sejam autenticados protege Razor páginas e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="151dd-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="151dd-170">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor páginas para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="151dd-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="151dd-171">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe também contém <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="151dd-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="151dd-172">O `DefaultPolicy` é a política usada com o `[Authorize]` atributo quando nenhuma política é especificada.</span><span class="sxs-lookup"><span data-stu-id="151dd-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="151dd-173">`[Authorize]` Não contém uma política nomeada, ao contrário de `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="151dd-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="151dd-174">Para obter mais informações sobre políticas, consulte <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="151dd-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="151dd-175">Uma maneira alternativa para os controladores e Razor páginas do MVC exigir que todos os usuários sejam autenticados é adicionar um filtro de autorização:</span><span class="sxs-lookup"><span data-stu-id="151dd-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="151dd-176">O código anterior usa um filtro de autorização, a definição da política de fallback usa o roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="151dd-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="151dd-177">Definir a política de fallback é a maneira preferida de exigir que todos os usuários sejam autenticados.</span><span class="sxs-lookup"><span data-stu-id="151dd-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="151dd-178">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às `Index` páginas e `Privacy` para que os usuários anônimos possam obter informações sobre o site antes de se registrarem:</span><span class="sxs-lookup"><span data-stu-id="151dd-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="151dd-179">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="151dd-179">Configure the test account</span></span>

<span data-ttu-id="151dd-180">A `SeedData` classe cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="151dd-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="151dd-181">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="151dd-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="151dd-182">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="151dd-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="151dd-183">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="151dd-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="151dd-184">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="151dd-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="151dd-185">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="151dd-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="151dd-186">Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="151dd-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="151dd-187">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="151dd-188">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="151dd-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="151dd-189">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="151dd-190">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="151dd-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="151dd-191">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="151dd-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="151dd-192">Crie uma `ContactIsOwnerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="151dd-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="151dd-193">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="151dd-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="151dd-194">O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="151dd-195">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="151dd-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="151dd-196">Retornar `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="151dd-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="151dd-197">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="151dd-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="151dd-198">`Task.CompletedTask` Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="151dd-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="151dd-199">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="151dd-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="151dd-200">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="151dd-201">`ContactIsOwnerAuthorizationHandler` Não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="151dd-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="151dd-202">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="151dd-202">Create a manager authorization handler</span></span>

<span data-ttu-id="151dd-203">Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="151dd-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="151dd-204">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="151dd-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="151dd-205">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="151dd-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="151dd-206">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="151dd-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="151dd-207">Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="151dd-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="151dd-208">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="151dd-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="151dd-209">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="151dd-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="151dd-210">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="151dd-210">Register the authorization handlers</span></span>

<span data-ttu-id="151dd-211">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="151dd-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="151dd-212">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [Identity](xref:security/authentication/identity) , que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="151dd-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="151dd-213">Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="151dd-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="151dd-214">Adicione o seguinte código ao final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="151dd-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="151dd-215">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="151dd-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="151dd-216">Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="151dd-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="151dd-217">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="151dd-217">Support authorization</span></span>

<span data-ttu-id="151dd-218">Nesta seção, você atualizará as Razor páginas e adicionará uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="151dd-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="151dd-219">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="151dd-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="151dd-220">Examine a `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="151dd-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="151dd-221">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="151dd-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="151dd-222">Criar uma classe base para as páginas de contatos Razor</span><span class="sxs-lookup"><span data-stu-id="151dd-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="151dd-223">Crie uma classe base que contenha os serviços usados nas páginas de contatos Razor .</span><span class="sxs-lookup"><span data-stu-id="151dd-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="151dd-224">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="151dd-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="151dd-225">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="151dd-225">The preceding code:</span></span>

* <span data-ttu-id="151dd-226">Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="151dd-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="151dd-227">Adiciona o Identity `UserManager` serviço.</span><span class="sxs-lookup"><span data-stu-id="151dd-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="151dd-228">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="151dd-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="151dd-229">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="151dd-229">Update the CreateModel</span></span>

<span data-ttu-id="151dd-230">Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:</span><span class="sxs-lookup"><span data-stu-id="151dd-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="151dd-231">Atualize o `CreateModel.OnPostAsync` método para:</span><span class="sxs-lookup"><span data-stu-id="151dd-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="151dd-232">Adicione a ID de usuário ao `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="151dd-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="151dd-233">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="151dd-234">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="151dd-234">Update the IndexModel</span></span>

<span data-ttu-id="151dd-235">Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="151dd-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="151dd-236">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="151dd-236">Update the EditModel</span></span>

<span data-ttu-id="151dd-237">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="151dd-238">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="151dd-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="151dd-239">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="151dd-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="151dd-240">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="151dd-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="151dd-241">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="151dd-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="151dd-242">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="151dd-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="151dd-243">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="151dd-243">Update the DeleteModel</span></span>

<span data-ttu-id="151dd-244">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="151dd-245">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="151dd-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="151dd-246">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="151dd-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="151dd-247">Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="151dd-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="151dd-248">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="151dd-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="151dd-249">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="151dd-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="151dd-250">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="151dd-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="151dd-251">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="151dd-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="151dd-252">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="151dd-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="151dd-253">A Razor página ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="151dd-254">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="151dd-254">Update Details</span></span>

<span data-ttu-id="151dd-255">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="151dd-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="151dd-256">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="151dd-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="151dd-257">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="151dd-257">Add or remove a user to a role</span></span>

<span data-ttu-id="151dd-258">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="151dd-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="151dd-259">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="151dd-259">Removing privileges from a user.</span></span> <span data-ttu-id="151dd-260">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="151dd-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="151dd-261">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="151dd-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="151dd-262">Diferenças entre desafio e proíba</span><span class="sxs-lookup"><span data-stu-id="151dd-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="151dd-263">Esse aplicativo define a política padrão para [exigir usuários autenticados](#rau).</span><span class="sxs-lookup"><span data-stu-id="151dd-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="151dd-264">O código a seguir permite usuários anônimos.</span><span class="sxs-lookup"><span data-stu-id="151dd-264">The following code allows anonymous users.</span></span> <span data-ttu-id="151dd-265">Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.</span><span class="sxs-lookup"><span data-stu-id="151dd-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="151dd-266">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="151dd-266">In the preceding code:</span></span>

* <span data-ttu-id="151dd-267">Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="151dd-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="151dd-268">Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.</span><span class="sxs-lookup"><span data-stu-id="151dd-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="151dd-269">Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="151dd-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="151dd-270">Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.</span><span class="sxs-lookup"><span data-stu-id="151dd-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="151dd-271">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="151dd-271">Test the completed app</span></span>

<span data-ttu-id="151dd-272">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="151dd-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="151dd-273">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="151dd-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="151dd-274">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="151dd-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="151dd-275">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="151dd-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="151dd-276">Se o aplicativo tiver contatos:</span><span class="sxs-lookup"><span data-stu-id="151dd-276">If the app has contacts:</span></span>

* <span data-ttu-id="151dd-277">Exclua todos os registros na `Contact` tabela.</span><span class="sxs-lookup"><span data-stu-id="151dd-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="151dd-278">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="151dd-279">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="151dd-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="151dd-280">Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="151dd-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="151dd-281">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="151dd-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="151dd-282">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="151dd-282">Verify the following operations:</span></span>

* <span data-ttu-id="151dd-283">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="151dd-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="151dd-284">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="151dd-285">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="151dd-286">A `Details` exibição mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="151dd-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="151dd-287">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="151dd-288">Usuário</span><span class="sxs-lookup"><span data-stu-id="151dd-288">User</span></span>                | <span data-ttu-id="151dd-289">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="151dd-289">Seeded by the app</span></span> | <span data-ttu-id="151dd-290">Opções</span><span class="sxs-lookup"><span data-stu-id="151dd-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="151dd-291">Não</span><span class="sxs-lookup"><span data-stu-id="151dd-291">No</span></span>                | <span data-ttu-id="151dd-292">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="151dd-293">Sim</span><span class="sxs-lookup"><span data-stu-id="151dd-293">Yes</span></span>               | <span data-ttu-id="151dd-294">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="151dd-295">Sim</span><span class="sxs-lookup"><span data-stu-id="151dd-295">Yes</span></span>               | <span data-ttu-id="151dd-296">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="151dd-297">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="151dd-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="151dd-298">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="151dd-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="151dd-299">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="151dd-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="151dd-300">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="151dd-300">Create the starter app</span></span>

* <span data-ttu-id="151dd-301">Criar um Razor aplicativo de páginas chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="151dd-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="151dd-302">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="151dd-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="151dd-303">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="151dd-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="151dd-304">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="151dd-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="151dd-305">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="151dd-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="151dd-306">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="151dd-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="151dd-307">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="151dd-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="151dd-308">Se você tiver um bug com o `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="151dd-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="151dd-309">Atualize a âncora **ContactManager** no arquivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="151dd-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="151dd-310">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="151dd-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="151dd-311">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="151dd-311">Seed the database</span></span>

<span data-ttu-id="151dd-312">Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :</span><span class="sxs-lookup"><span data-stu-id="151dd-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="151dd-313">Chamada `SeedData.Initialize` de `Main` :</span><span class="sxs-lookup"><span data-stu-id="151dd-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="151dd-314">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-314">Test that the app seeded the database.</span></span> <span data-ttu-id="151dd-315">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="151dd-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="151dd-316">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="151dd-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="151dd-317">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="151dd-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="151dd-318">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="151dd-318">There are three security groups:</span></span>

* <span data-ttu-id="151dd-319">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="151dd-320">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="151dd-321">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="151dd-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="151dd-322">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="151dd-323">Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado.</span><span class="sxs-lookup"><span data-stu-id="151dd-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="151dd-324">Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="151dd-325">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="151dd-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="151dd-326">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="151dd-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="151dd-328">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="151dd-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

<span data-ttu-id="151dd-330">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="151dd-330">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="151dd-332">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="151dd-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="151dd-333">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="151dd-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

<span data-ttu-id="151dd-335">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="151dd-335">The administrator has all privileges.</span></span> <span data-ttu-id="151dd-336">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="151dd-337">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="151dd-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="151dd-338">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="151dd-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="151dd-339">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="151dd-340">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="151dd-341">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="151dd-342">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="151dd-342">Prerequisites</span></span>

<span data-ttu-id="151dd-343">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="151dd-343">This tutorial is advanced.</span></span> <span data-ttu-id="151dd-344">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="151dd-344">You should be familiar with:</span></span>

* [<span data-ttu-id="151dd-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="151dd-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="151dd-346">Autenticação</span><span class="sxs-lookup"><span data-stu-id="151dd-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="151dd-347">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="151dd-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="151dd-348">Autorização</span><span class="sxs-lookup"><span data-stu-id="151dd-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="151dd-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="151dd-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="151dd-350">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="151dd-350">The starter and completed app</span></span>

<span data-ttu-id="151dd-351">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="151dd-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="151dd-352">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="151dd-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="151dd-353">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="151dd-353">The starter app</span></span>

<span data-ttu-id="151dd-354">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="151dd-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="151dd-355">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="151dd-356">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="151dd-356">Secure user data</span></span>

<span data-ttu-id="151dd-357">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="151dd-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="151dd-358">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="151dd-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="151dd-359">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="151dd-359">Tie the contact data to the user</span></span>

<span data-ttu-id="151dd-360">Use a [Identity](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="151dd-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="151dd-361">Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="151dd-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="151dd-362">`OwnerID` é a ID do usuário da `AspNetUser` tabela no banco de [Identity](xref:security/authentication/identity) dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="151dd-363">O `Status` campo determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="151dd-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="151dd-364">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="151dd-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="151dd-365">Adicionar serviços de função a Identity</span><span class="sxs-lookup"><span data-stu-id="151dd-365">Add Role services to Identity</span></span>

<span data-ttu-id="151dd-366">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="151dd-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="151dd-367">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="151dd-367">Require authenticated users</span></span>

<span data-ttu-id="151dd-368">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="151dd-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="151dd-369">Você pode recusar a autenticação na Razor página, controlador ou nível de método de ação com o `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="151dd-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="151dd-370">Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege Razor páginas e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="151dd-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="151dd-371">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor páginas para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="151dd-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="151dd-372">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice, sobre e de contato para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="151dd-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="151dd-373">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="151dd-373">Configure the test account</span></span>

<span data-ttu-id="151dd-374">A `SeedData` classe cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="151dd-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="151dd-375">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="151dd-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="151dd-376">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="151dd-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="151dd-377">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="151dd-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="151dd-378">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="151dd-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="151dd-379">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="151dd-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="151dd-380">Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="151dd-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="151dd-381">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="151dd-382">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="151dd-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="151dd-383">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="151dd-384">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="151dd-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="151dd-385">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="151dd-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="151dd-386">Crie uma pasta de *autorização* e crie uma `ContactIsOwnerAuthorizationHandler` classe nela.</span><span class="sxs-lookup"><span data-stu-id="151dd-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="151dd-387">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="151dd-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="151dd-388">O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="151dd-389">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="151dd-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="151dd-390">Retornar `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="151dd-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="151dd-391">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="151dd-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="151dd-392">`Task.CompletedTask` Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="151dd-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="151dd-393">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="151dd-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="151dd-394">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="151dd-395">`ContactIsOwnerAuthorizationHandler` Não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="151dd-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="151dd-396">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="151dd-396">Create a manager authorization handler</span></span>

<span data-ttu-id="151dd-397">Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="151dd-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="151dd-398">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="151dd-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="151dd-399">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="151dd-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="151dd-400">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="151dd-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="151dd-401">Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="151dd-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="151dd-402">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="151dd-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="151dd-403">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="151dd-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="151dd-404">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="151dd-404">Register the authorization handlers</span></span>

<span data-ttu-id="151dd-405">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="151dd-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="151dd-406">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [Identity](xref:security/authentication/identity) , que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="151dd-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="151dd-407">Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="151dd-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="151dd-408">Adicione o seguinte código ao final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="151dd-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="151dd-409">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="151dd-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="151dd-410">Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="151dd-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="151dd-411">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="151dd-411">Support authorization</span></span>

<span data-ttu-id="151dd-412">Nesta seção, você atualizará as Razor páginas e adicionará uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="151dd-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="151dd-413">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="151dd-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="151dd-414">Examine a `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="151dd-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="151dd-415">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="151dd-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="151dd-416">Criar uma classe base para as páginas de contatos Razor</span><span class="sxs-lookup"><span data-stu-id="151dd-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="151dd-417">Crie uma classe base que contenha os serviços usados nas páginas de contatos Razor .</span><span class="sxs-lookup"><span data-stu-id="151dd-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="151dd-418">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="151dd-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="151dd-419">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="151dd-419">The preceding code:</span></span>

* <span data-ttu-id="151dd-420">Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="151dd-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="151dd-421">Adiciona o Identity `UserManager` serviço.</span><span class="sxs-lookup"><span data-stu-id="151dd-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="151dd-422">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="151dd-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="151dd-423">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="151dd-423">Update the CreateModel</span></span>

<span data-ttu-id="151dd-424">Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:</span><span class="sxs-lookup"><span data-stu-id="151dd-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="151dd-425">Atualize o `CreateModel.OnPostAsync` método para:</span><span class="sxs-lookup"><span data-stu-id="151dd-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="151dd-426">Adicione a ID de usuário ao `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="151dd-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="151dd-427">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="151dd-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="151dd-428">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="151dd-428">Update the IndexModel</span></span>

<span data-ttu-id="151dd-429">Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="151dd-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="151dd-430">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="151dd-430">Update the EditModel</span></span>

<span data-ttu-id="151dd-431">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="151dd-432">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="151dd-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="151dd-433">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="151dd-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="151dd-434">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="151dd-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="151dd-435">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="151dd-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="151dd-436">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="151dd-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="151dd-437">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="151dd-437">Update the DeleteModel</span></span>

<span data-ttu-id="151dd-438">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="151dd-439">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="151dd-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="151dd-440">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="151dd-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="151dd-441">Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="151dd-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="151dd-442">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="151dd-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="151dd-443">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="151dd-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="151dd-444">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="151dd-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="151dd-445">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="151dd-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="151dd-446">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="151dd-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="151dd-447">A Razor página ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="151dd-448">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="151dd-448">Update Details</span></span>

<span data-ttu-id="151dd-449">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="151dd-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="151dd-450">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="151dd-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="151dd-451">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="151dd-451">Add or remove a user to a role</span></span>

<span data-ttu-id="151dd-452">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="151dd-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="151dd-453">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="151dd-453">Removing privileges from a user.</span></span> <span data-ttu-id="151dd-454">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="151dd-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="151dd-455">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="151dd-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="151dd-456">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="151dd-456">Test the completed app</span></span>

<span data-ttu-id="151dd-457">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="151dd-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="151dd-458">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="151dd-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="151dd-459">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="151dd-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="151dd-460">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="151dd-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="151dd-461">Remover e atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="151dd-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="151dd-462">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="151dd-463">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="151dd-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="151dd-464">Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="151dd-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="151dd-465">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="151dd-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="151dd-466">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="151dd-466">Verify the following operations:</span></span>

* <span data-ttu-id="151dd-467">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="151dd-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="151dd-468">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="151dd-469">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="151dd-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="151dd-470">A `Details` exibição mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="151dd-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="151dd-471">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="151dd-472">Usuário</span><span class="sxs-lookup"><span data-stu-id="151dd-472">User</span></span>                | <span data-ttu-id="151dd-473">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="151dd-473">Seeded by the app</span></span> | <span data-ttu-id="151dd-474">Opções</span><span class="sxs-lookup"><span data-stu-id="151dd-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="151dd-475">Não</span><span class="sxs-lookup"><span data-stu-id="151dd-475">No</span></span>                | <span data-ttu-id="151dd-476">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="151dd-477">Sim</span><span class="sxs-lookup"><span data-stu-id="151dd-477">Yes</span></span>               | <span data-ttu-id="151dd-478">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="151dd-479">Sim</span><span class="sxs-lookup"><span data-stu-id="151dd-479">Yes</span></span>               | <span data-ttu-id="151dd-480">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="151dd-481">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="151dd-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="151dd-482">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="151dd-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="151dd-483">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="151dd-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="151dd-484">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="151dd-484">Create the starter app</span></span>

* <span data-ttu-id="151dd-485">Criar um Razor aplicativo de páginas chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="151dd-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="151dd-486">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="151dd-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="151dd-487">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="151dd-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="151dd-488">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="151dd-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="151dd-489">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="151dd-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="151dd-490">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="151dd-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="151dd-491">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="151dd-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="151dd-492">Atualize a âncora **ContactManager** no arquivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="151dd-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="151dd-493">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="151dd-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="151dd-494">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="151dd-494">Seed the database</span></span>

<span data-ttu-id="151dd-495">Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .</span><span class="sxs-lookup"><span data-stu-id="151dd-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="151dd-496">Chamada `SeedData.Initialize` de `Main` :</span><span class="sxs-lookup"><span data-stu-id="151dd-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="151dd-497">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="151dd-497">Test that the app seeded the database.</span></span> <span data-ttu-id="151dd-498">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="151dd-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="151dd-499">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="151dd-499">Additional resources</span></span>

* [<span data-ttu-id="151dd-500">Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="151dd-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="151dd-501">[ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="151dd-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="151dd-502">Este laboratório apresenta mais detalhes sobre os recursos de segurança apresentados neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="151dd-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="151dd-503">Autorização baseada em política personalizada</span><span class="sxs-lookup"><span data-stu-id="151dd-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
