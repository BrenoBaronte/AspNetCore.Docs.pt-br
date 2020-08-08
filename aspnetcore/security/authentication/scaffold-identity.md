---
title: Scaffold Identity em projetos de ASP.NET Core
author: rick-anderson
description: Saiba como Scaffold Identity em um projeto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 4404a5513d9dc989e50c904f3e7863de59991939
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022323"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="5af31-103">Scaffold Identity em projetos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5af31-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="5af31-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5af31-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5af31-105">ASP.NET Core fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="5af31-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5af31-106">Os aplicativos que incluem Identity o podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na Identity Razor biblioteca de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="5af31-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="5af31-107">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="5af31-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="5af31-108">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="5af31-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="5af31-109">O código gerado tem precedência sobre o mesmo código no Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="5af31-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="5af31-110">Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar Identity origem da interface do usuário completa](#full).</span><span class="sxs-lookup"><span data-stu-id="5af31-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="5af31-111">Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o Identity pacote RCL.</span><span class="sxs-lookup"><span data-stu-id="5af31-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="5af31-112">Você tem a opção de selecionar o Identity código a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="5af31-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="5af31-113">Embora o scaffolder gere a maior parte do código necessário, você precisa atualizar seu projeto para concluir o processo.</span><span class="sxs-lookup"><span data-stu-id="5af31-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="5af31-114">Este documento explica as etapas necessárias para concluir uma Identity atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5af31-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="5af31-115">É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações.</span><span class="sxs-lookup"><span data-stu-id="5af31-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="5af31-116">Inspecione as alterações depois de executar o Identity scaffolder.</span><span class="sxs-lookup"><span data-stu-id="5af31-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="5af31-117">Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com o Identity .</span><span class="sxs-lookup"><span data-stu-id="5af31-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="5af31-118">Serviços ou stubs de serviço não são gerados quando scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="5af31-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="5af31-119">Os serviços para habilitar esses recursos devem ser adicionados manualmente.</span><span class="sxs-lookup"><span data-stu-id="5af31-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="5af31-120">Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="5af31-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="5af31-121">Quando scaffolding Identity com um novo contexto de dados em um projeto com contas individuais existentes:</span><span class="sxs-lookup"><span data-stu-id="5af31-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="5af31-122">No `Startup.ConfigureServices` , remova as chamadas para:</span><span class="sxs-lookup"><span data-stu-id="5af31-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="5af31-123">Por exemplo, `AddDbContext` e `AddDefaultIdentity` são comentados no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="5af31-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="5af31-124">O código anterior comenta o código que está duplicado em *áreas/ Identity / Identity HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="5af31-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="5af31-125">Normalmente, os aplicativos que foram criados com contas individuais ***não*** devem criar um novo contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="5af31-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="5af31-126">Scaffold Identity em um projeto vazio</span><span class="sxs-lookup"><span data-stu-id="5af31-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5af31-127">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="5af31-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="5af31-128">Scaffold Identity em um Razor projeto sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="5af31-128">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5af31-129">Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5af31-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5af31-130">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="5af31-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="5af31-131">Migrações, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="5af31-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="5af31-132">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="5af31-132">Enable authentication</span></span>

<span data-ttu-id="5af31-133">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="5af31-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="5af31-134">Alterações de layout</span><span class="sxs-lookup"><span data-stu-id="5af31-134">Layout changes</span></span>

<span data-ttu-id="5af31-135">Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo de layout:</span><span class="sxs-lookup"><span data-stu-id="5af31-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="5af31-136">Scaffold Identity em um Razor projeto com autorização</span><span class="sxs-lookup"><span data-stu-id="5af31-136">Scaffold Identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="5af31-137">Algumas Identity opções são configuradas em *áreas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5af31-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5af31-138">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="5af31-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="5af31-139">Scaffold Identity em um projeto MVC sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="5af31-139">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5af31-140">Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5af31-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="5af31-141">Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5af31-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="5af31-142">Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5af31-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5af31-143">Para obter mais informações, consulte IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="5af31-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="5af31-144">Atualize a `Startup` classe com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="5af31-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="5af31-145">Scaffold Identity em um projeto MVC com autorização</span><span class="sxs-lookup"><span data-stu-id="5af31-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="5af31-146">Scaffold Identity em um Blazor Server projeto sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="5af31-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5af31-147">Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5af31-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5af31-148">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="5af31-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="5af31-149">Migrações</span><span class="sxs-lookup"><span data-stu-id="5af31-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="5af31-150">Passar um token XSRF para o aplicativo</span><span class="sxs-lookup"><span data-stu-id="5af31-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="5af31-151">Tokens podem ser passados para componentes:</span><span class="sxs-lookup"><span data-stu-id="5af31-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="5af31-152">Quando os tokens de autenticação são provisionados e salvos na autenticação cookie , eles podem ser passados para os componentes do.</span><span class="sxs-lookup"><span data-stu-id="5af31-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="5af31-153">Razoros componentes não podem usar `HttpContext` diretamente, portanto, não há como obter um [token de XSRF (falsificação de solicitação)](xref:security/anti-request-forgery) para postar no Identity ponto de extremidade de logout em `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="5af31-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="5af31-154">Um token XSRF pode ser passado para componentes.</span><span class="sxs-lookup"><span data-stu-id="5af31-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="5af31-155">Para obter mais informações, consulte <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="5af31-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="5af31-156">No arquivo *pages/_Host. cshtml* , estabeleça o token depois de adicioná-lo às `InitialApplicationState` `TokenProvider` classes e:</span><span class="sxs-lookup"><span data-stu-id="5af31-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="5af31-157">Atualize o `App` componente (*app. Razor*) para atribuir `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="5af31-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="5af31-158">O `TokenProvider` serviço demonstrado no tópico é usado no `LoginDisplay` componente na seção [layout e alterações de fluxo de autenticação](#layout-and-authentication-flow-changes) a seguir.</span><span class="sxs-lookup"><span data-stu-id="5af31-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="5af31-159">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="5af31-159">Enable authentication</span></span>

<span data-ttu-id="5af31-160">Na `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="5af31-160">In the `Startup` class:</span></span>

* <span data-ttu-id="5af31-161">Confirme se as Razor páginas de serviços são adicionadas no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5af31-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="5af31-162">Se estiver usando o [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registre o serviço.</span><span class="sxs-lookup"><span data-stu-id="5af31-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="5af31-163">Chame `UseDatabaseErrorPage` no Application Builder no `Startup.Configure` para o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5af31-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="5af31-164">Chamar `UseAuthentication` e `UseAuthorization` depois `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="5af31-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="5af31-165">Adicione um ponto de extremidade para Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="5af31-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="5af31-166">Alterações no fluxo de layout e de autenticação</span><span class="sxs-lookup"><span data-stu-id="5af31-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="5af31-167">Adicione um `RedirectToLogin` componente (*RedirectToLogin. Razor*) à pasta *compartilhada* do aplicativo na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5af31-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Adicione um `LoginDisplay` componente (*LoginDisplay. Razor*) à pasta *compartilhada* do aplicativo. <span data-ttu-id="5af31-169">O [serviço TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fornece o token XSRF para o formulário HTML que posta para o Identity ponto de extremidade de logout:</span><span class="sxs-lookup"><span data-stu-id="5af31-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="5af31-170">No `MainLayout` componente (*Shared/MainLayout. Razor*), adicione o `LoginDisplay` componente ao `<div>` conteúdo do elemento de linha superior:</span><span class="sxs-lookup"><span data-stu-id="5af31-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="5af31-171">Pontos de extremidade de autenticação de estilo</span><span class="sxs-lookup"><span data-stu-id="5af31-171">Style authentication endpoints</span></span>

<span data-ttu-id="5af31-172">Como Blazor Server o usa páginas Razor Identity de páginas, o estilo da interface do usuário é alterado quando um visitante navega entre Identity páginas e componentes.</span><span class="sxs-lookup"><span data-stu-id="5af31-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="5af31-173">Você tem duas opções para abordar os estilos de incongruous:</span><span class="sxs-lookup"><span data-stu-id="5af31-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="5af31-174">Componentes de compilação Identity</span><span class="sxs-lookup"><span data-stu-id="5af31-174">Build Identity components</span></span>

<span data-ttu-id="5af31-175">Uma abordagem para usar componentes do Identity em vez de páginas é criar Identity componentes.</span><span class="sxs-lookup"><span data-stu-id="5af31-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="5af31-176">Como `SignInManager` e `UserManager` não têm suporte em Razor componentes, use pontos de extremidade de API no Blazor Server aplicativo para processar ações de conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="5af31-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="5af31-177">Usar um layout personalizado com Blazor estilos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="5af31-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="5af31-178">O Identity layout de páginas e os estilos podem ser modificados para produzir páginas que usam o Blazor tema padrão.</span><span class="sxs-lookup"><span data-stu-id="5af31-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="5af31-179">O exemplo nesta seção é meramente um ponto de partida para personalização.</span><span class="sxs-lookup"><span data-stu-id="5af31-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="5af31-180">Um trabalho adicional é provavelmente necessário para a melhor experiência do usuário.</span><span class="sxs-lookup"><span data-stu-id="5af31-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="5af31-181">Crie um novo `NavMenu_IdentityLayout` componente (*Shared/NavMenu_ Identity layout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="5af31-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="5af31-182">Para a marcação e o código do componente, use o mesmo conteúdo do componente do aplicativo `NavMenu` (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="5af31-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="5af31-183">Remova todos os `NavLink` s para componentes que não podem ser acessados anonimamente porque os redirecionamentos automáticos no `RedirectToLogin` componente falham para componentes que exigem autenticação ou autorização.</span><span class="sxs-lookup"><span data-stu-id="5af31-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="5af31-184">No arquivo *pages/Shared/layout. cshtml* , faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="5af31-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="5af31-185">Adicione Razor diretivas à parte superior do arquivo para usar auxiliares de marca e os componentes do aplicativo na pasta *compartilhada* :</span><span class="sxs-lookup"><span data-stu-id="5af31-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="5af31-186">Substituir `{APPLICATION ASSEMBLY}` pelo nome do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5af31-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="5af31-187">Adicione uma `<base>` marca e uma Blazor folha `<link>` de estilos ao `<head>` conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5af31-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="5af31-188">Altere o conteúdo da `<body>` marca para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="5af31-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="5af31-189">Scaffold Identity em um Blazor Server projeto com autorização</span><span class="sxs-lookup"><span data-stu-id="5af31-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="5af31-190">Algumas Identity opções são configuradas em *áreas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5af31-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5af31-191">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="5af31-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="5af31-192">Criar Identity origem da interface do usuário completa</span><span class="sxs-lookup"><span data-stu-id="5af31-192">Create full Identity UI source</span></span>

<span data-ttu-id="5af31-193">Para manter o controle total da Identity interface do usuário, execute o Identity scaffolder e selecione **substituir todos os arquivos**.</span><span class="sxs-lookup"><span data-stu-id="5af31-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="5af31-194">O código realçado a seguir mostra as alterações para substituir a Identity interface do usuário padrão por Identity em um aplicativo web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="5af31-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="5af31-195">Talvez você queira fazer isso para ter controle total da interface do Identity usuário.</span><span class="sxs-lookup"><span data-stu-id="5af31-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="5af31-196">O padrão Identity é substituído no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="5af31-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="5af31-197">O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="5af31-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="5af31-198">Registre uma `IEmailSender` implementação, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5af31-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="5af31-199">Configuração de senha</span><span class="sxs-lookup"><span data-stu-id="5af31-199">Password configuration</span></span>

<span data-ttu-id="5af31-200">Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> o estiver configurado no `Startup.ConfigureServices` , a configuração de [ `[StringLength]` atributo](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) poderá ser necessária para a `Password` propriedade em Identity páginas com Scaffold.</span><span class="sxs-lookup"><span data-stu-id="5af31-200">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="5af31-201">`InputModel``Password`as propriedades são encontradas nos seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="5af31-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="5af31-202">Desabilitar uma página</span><span class="sxs-lookup"><span data-stu-id="5af31-202">Disable a page</span></span>

<span data-ttu-id="5af31-203">Estas seções mostram como desabilitar a página de registro, mas a abordagem pode ser usada para desabilitar qualquer página.</span><span class="sxs-lookup"><span data-stu-id="5af31-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="5af31-204">Para desabilitar o registro do usuário:</span><span class="sxs-lookup"><span data-stu-id="5af31-204">To disable user registration:</span></span>

* <span data-ttu-id="5af31-205">Scaffold Identity .</span><span class="sxs-lookup"><span data-stu-id="5af31-205">Scaffold Identity.</span></span> <span data-ttu-id="5af31-206">Inclua conta. Register, Account. login e Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="5af31-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="5af31-207">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5af31-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="5af31-208">Atualizar *áreas/ Identity /pages/Account/Register.cshtml.cs* para que os usuários não possam se registrar neste ponto de extremidade:</span><span class="sxs-lookup"><span data-stu-id="5af31-208">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="5af31-209">Atualize *áreas/ Identity /pages/Account/Register.cshtml* para que sejam consistentes com as alterações anteriores:</span><span class="sxs-lookup"><span data-stu-id="5af31-209">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="5af31-210">Comente ou remova o link de registro de *áreas/ Identity /pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5af31-210">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="5af31-211">Atualize a página *áreas/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="5af31-211">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="5af31-212">Remova o código e os links do arquivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="5af31-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="5af31-213">Remova o código de confirmação do `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="5af31-213">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="5af31-214">Usar outro aplicativo para adicionar usuários</span><span class="sxs-lookup"><span data-stu-id="5af31-214">Use another app to add users</span></span>

<span data-ttu-id="5af31-215">Forneça um mecanismo para adicionar usuários fora do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="5af31-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="5af31-216">As opções para adicionar usuários incluem:</span><span class="sxs-lookup"><span data-stu-id="5af31-216">Options to add users include:</span></span>

* <span data-ttu-id="5af31-217">Um aplicativo Web de administrador dedicado.</span><span class="sxs-lookup"><span data-stu-id="5af31-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="5af31-218">Um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="5af31-218">A console app.</span></span>

<span data-ttu-id="5af31-219">O código a seguir descreve uma abordagem para adicionar usuários:</span><span class="sxs-lookup"><span data-stu-id="5af31-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="5af31-220">Uma lista de usuários é lida na memória.</span><span class="sxs-lookup"><span data-stu-id="5af31-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="5af31-221">Uma senha exclusiva forte é gerada para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="5af31-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="5af31-222">O usuário é adicionado ao Identity banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5af31-222">The user is added to the Identity database.</span></span>
* <span data-ttu-id="5af31-223">O usuário é notificado e foi instruído a alterar a senha.</span><span class="sxs-lookup"><span data-stu-id="5af31-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="5af31-224">O código a seguir descreve como adicionar um usuário:</span><span class="sxs-lookup"><span data-stu-id="5af31-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="5af31-225">Uma abordagem semelhante pode ser seguida para cenários de produção.</span><span class="sxs-lookup"><span data-stu-id="5af31-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="5af31-226">Impedir a publicação de Identity ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="5af31-226">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="5af31-227">Para evitar a publicação de ativos estáticos na Identity raiz da Web, consulte <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="5af31-227">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5af31-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5af31-228">Additional resources</span></span>

* [<span data-ttu-id="5af31-229">Alterações no código de autenticação para ASP.NET Core 2,1 e posterior</span><span class="sxs-lookup"><span data-stu-id="5af31-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5af31-230">ASP.NET Core 2,1 e posterior fornece [ASP.NET Core Identity ](xref:security/authentication/identity) como uma [ Razor biblioteca de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="5af31-230">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5af31-231">Os aplicativos que incluem Identity o podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na Identity Razor biblioteca de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="5af31-231">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="5af31-232">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="5af31-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="5af31-233">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="5af31-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="5af31-234">O código gerado tem precedência sobre o mesmo código no Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="5af31-234">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="5af31-235">Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).</span><span class="sxs-lookup"><span data-stu-id="5af31-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="5af31-236">Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o Identity pacote RCL.</span><span class="sxs-lookup"><span data-stu-id="5af31-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="5af31-237">Você tem a opção de selecionar o Identity código a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="5af31-237">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="5af31-238">Embora o scaffolder gere a maior parte do código necessário, você precisará atualizar seu projeto para concluir o processo.</span><span class="sxs-lookup"><span data-stu-id="5af31-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="5af31-239">Este documento explica as etapas necessárias para concluir uma Identity atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5af31-239">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="5af31-240">Quando o Identity scaffolder é executado, um arquivo de *ScaffoldingReadme.txt* é criado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="5af31-240">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="5af31-241">O arquivo de *ScaffoldingReadme.txt* contém instruções gerais sobre o que é necessário para concluir a Identity atualização do scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5af31-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="5af31-242">Este documento contém instruções mais completas do que o arquivo de *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="5af31-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="5af31-243">É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações.</span><span class="sxs-lookup"><span data-stu-id="5af31-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="5af31-244">Inspecione as alterações depois de executar o Identity scaffolder.</span><span class="sxs-lookup"><span data-stu-id="5af31-244">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="5af31-245">Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com o Identity .</span><span class="sxs-lookup"><span data-stu-id="5af31-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="5af31-246">Serviços ou stubs de serviço não são gerados quando scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="5af31-246">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="5af31-247">Os serviços para habilitar esses recursos devem ser adicionados manualmente.</span><span class="sxs-lookup"><span data-stu-id="5af31-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="5af31-248">Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="5af31-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="5af31-249">Scaffold Identity em um projeto vazio</span><span class="sxs-lookup"><span data-stu-id="5af31-249">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5af31-250">Adicione as seguintes chamadas realçadas à `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="5af31-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="5af31-251">Scaffold Identity em um Razor projeto sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="5af31-251">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5af31-252">Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5af31-252">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5af31-253">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="5af31-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="5af31-254">Migrações, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="5af31-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="5af31-255">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="5af31-255">Enable authentication</span></span>

<span data-ttu-id="5af31-256">No `Configure` método da `Startup` classe, chame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="5af31-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="5af31-257">Alterações de layout</span><span class="sxs-lookup"><span data-stu-id="5af31-257">Layout changes</span></span>

<span data-ttu-id="5af31-258">Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo de layout:</span><span class="sxs-lookup"><span data-stu-id="5af31-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="5af31-259">Scaffold Identity em um Razor projeto com autorização</span><span class="sxs-lookup"><span data-stu-id="5af31-259">Scaffold Identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="5af31-260">Algumas Identity opções são configuradas em *áreas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5af31-260">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5af31-261">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="5af31-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="5af31-262">Scaffold Identity em um projeto MVC sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="5af31-262">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5af31-263">Opcional: Adicione o logon Partial ( `_LoginPartial` ) ao arquivo *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5af31-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="5af31-264">Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5af31-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="5af31-265">Identityé configurado em *áreas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5af31-265">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5af31-266">Para obter mais informações, consulte IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="5af31-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="5af31-267">Chamar [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="5af31-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="5af31-268">Scaffold Identity em um projeto MVC com autorização</span><span class="sxs-lookup"><span data-stu-id="5af31-268">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="5af31-269">Exclua as *páginas/pasta compartilhada* e os arquivos nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="5af31-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="5af31-270">Criar Identity origem da interface do usuário completa</span><span class="sxs-lookup"><span data-stu-id="5af31-270">Create full Identity UI source</span></span>

<span data-ttu-id="5af31-271">Para manter o controle total da Identity interface do usuário, execute o Identity scaffolder e selecione **substituir todos os arquivos**.</span><span class="sxs-lookup"><span data-stu-id="5af31-271">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="5af31-272">O código realçado a seguir mostra as alterações para substituir a Identity interface do usuário padrão por Identity em um aplicativo web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="5af31-272">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="5af31-273">Talvez você queira fazer isso para ter controle total da interface do Identity usuário.</span><span class="sxs-lookup"><span data-stu-id="5af31-273">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="5af31-274">O padrão Identity é substituído no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="5af31-274">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="5af31-275">O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="5af31-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="5af31-276">Registre uma `IEmailSender` implementação, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5af31-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="5af31-277">Configuração de senha</span><span class="sxs-lookup"><span data-stu-id="5af31-277">Password configuration</span></span>

<span data-ttu-id="5af31-278">Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> o estiver configurado no `Startup.ConfigureServices` , a configuração de [ `[StringLength]` atributo](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) poderá ser necessária para a `Password` propriedade em Identity páginas com Scaffold.</span><span class="sxs-lookup"><span data-stu-id="5af31-278">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="5af31-279">`InputModel``Password`as propriedades são encontradas nos seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="5af31-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="5af31-280">Página desabilitar registro</span><span class="sxs-lookup"><span data-stu-id="5af31-280">Disable register page</span></span>

<span data-ttu-id="5af31-281">Para desabilitar o registro do usuário:</span><span class="sxs-lookup"><span data-stu-id="5af31-281">To disable user registration:</span></span>

* <span data-ttu-id="5af31-282">Scaffold Identity .</span><span class="sxs-lookup"><span data-stu-id="5af31-282">Scaffold Identity.</span></span> <span data-ttu-id="5af31-283">Inclua conta. Register, Account. login e Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="5af31-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="5af31-284">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5af31-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="5af31-285">Atualizar *áreas/ Identity /pages/Account/Register.cshtml.cs* para que os usuários não possam se registrar neste ponto de extremidade:</span><span class="sxs-lookup"><span data-stu-id="5af31-285">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="5af31-286">Atualize *áreas/ Identity /pages/Account/Register.cshtml* para que sejam consistentes com as alterações anteriores:</span><span class="sxs-lookup"><span data-stu-id="5af31-286">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="5af31-287">Comente ou remova o link de registro de *áreas/ Identity /pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5af31-287">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="5af31-288">Atualize a página *áreas/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="5af31-288">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="5af31-289">Remova o código e os links do arquivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="5af31-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="5af31-290">Remova o código de confirmação do `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="5af31-290">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="5af31-291">Usar outro aplicativo para adicionar usuários</span><span class="sxs-lookup"><span data-stu-id="5af31-291">Use another app to add users</span></span>

<span data-ttu-id="5af31-292">Forneça um mecanismo para adicionar usuários fora do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="5af31-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="5af31-293">As opções para adicionar usuários incluem:</span><span class="sxs-lookup"><span data-stu-id="5af31-293">Options to add users include:</span></span>

* <span data-ttu-id="5af31-294">Um aplicativo Web de administrador dedicado.</span><span class="sxs-lookup"><span data-stu-id="5af31-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="5af31-295">Um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="5af31-295">A console app.</span></span>

<span data-ttu-id="5af31-296">O código a seguir descreve uma abordagem para adicionar usuários:</span><span class="sxs-lookup"><span data-stu-id="5af31-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="5af31-297">Uma lista de usuários é lida na memória.</span><span class="sxs-lookup"><span data-stu-id="5af31-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="5af31-298">Uma senha exclusiva forte é gerada para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="5af31-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="5af31-299">O usuário é adicionado ao Identity banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5af31-299">The user is added to the Identity database.</span></span>
* <span data-ttu-id="5af31-300">O usuário é notificado e foi instruído a alterar a senha.</span><span class="sxs-lookup"><span data-stu-id="5af31-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="5af31-301">O código a seguir descreve como adicionar um usuário:</span><span class="sxs-lookup"><span data-stu-id="5af31-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="5af31-302">Uma abordagem semelhante pode ser seguida para cenários de produção.</span><span class="sxs-lookup"><span data-stu-id="5af31-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5af31-303">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5af31-303">Additional resources</span></span>

* [<span data-ttu-id="5af31-304">Alterações no código de autenticação para ASP.NET Core 2,1 e posterior</span><span class="sxs-lookup"><span data-stu-id="5af31-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
