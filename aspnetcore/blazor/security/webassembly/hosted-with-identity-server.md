---
title: Proteger um aplicativo ASP.NET Core hospedado Blazor WebAssembly com o Identity servidor
author: guardrex
description: Saiba como proteger um aplicativo ASP.NET Core hospedado Blazor WebAssembly com o Identity servidor.
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: c74711c10fe399718600f879c3d9151bfb1abd42
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281002"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="aac9b-103">Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com o Identity servidor</span><span class="sxs-lookup"><span data-stu-id="aac9b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="aac9b-104">Este artigo explica como criar um [ Blazor WebAssembly aplicativo hospedado](xref:blazor/hosting-models#blazor-webassembly) que usa o [ Identity servidor do](https://identityserver.io/) para autenticar usuários e chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="aac9b-104">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="aac9b-105">Para configurar um aplicativo autônomo ou hospedado Blazor WebAssembly para usar uma instância de servidor externa existente Identity , siga as orientações em <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="aac9b-105">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aac9b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aac9b-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aac9b-107">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="aac9b-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="aac9b-108">Depois de escolher o modelo de **Blazor WebAssembly aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="aac9b-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="aac9b-109">Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="aac9b-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="aac9b-110">Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-110">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="aac9b-111">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="aac9b-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="aac9b-112">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação em uma pasta vazia, especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [Identity](xref:security/authentication/identity) sistema do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="aac9b-112">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="aac9b-113">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="aac9b-113">Placeholder</span></span>  | <span data-ttu-id="aac9b-114">Exemplo</span><span class="sxs-lookup"><span data-stu-id="aac9b-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="aac9b-115">A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="aac9b-116">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aac9b-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aac9b-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="aac9b-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aac9b-118">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="aac9b-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="aac9b-119">Na etapa **configurar seu novo Blazor WebAssembly aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="aac9b-120">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="aac9b-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="aac9b-121">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-121">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="aac9b-122">*`Server`* configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="aac9b-122">*`Server`* app configuration</span></span>

<span data-ttu-id="aac9b-123">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.</span><span class="sxs-lookup"><span data-stu-id="aac9b-123">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="aac9b-124">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="aac9b-124">Startup class</span></span>

<span data-ttu-id="aac9b-125">A `Startup` classe tem as seguintes adições.</span><span class="sxs-lookup"><span data-stu-id="aac9b-125">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="aac9b-126">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="aac9b-126">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="aac9b-127">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="aac9b-127">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="aac9b-128">IdentityServidor com um <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura as convenções de ASP.NET Core padrão na parte superior do Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="aac9b-128">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="aac9b-129">Autenticação com um <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos pelo Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="aac9b-129">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="aac9b-130">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aac9b-130">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="aac9b-131">O Identity middleware do servidor expõe os pontos de extremidade do OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="aac9b-131">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="aac9b-132">O middleware de autenticação é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="aac9b-132">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="aac9b-133">O middleware de autorização habilita os recursos de autorização:</span><span class="sxs-lookup"><span data-stu-id="aac9b-133">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthorization();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="aac9b-134">Serviço de Aplicativo do Azure no Linux</span><span class="sxs-lookup"><span data-stu-id="aac9b-134">Azure App Service on Linux</span></span>

<span data-ttu-id="aac9b-135">Especifique o emissor explicitamente ao implantar no serviço Azure App no Linux.</span><span class="sxs-lookup"><span data-stu-id="aac9b-135">Specify the issuer explicitly when deploying to Azure App Service on Linux.</span></span> <span data-ttu-id="aac9b-136">Para obter mais informações, consulte <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="aac9b-136">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

### <a name="addapiauthorization"></a><span data-ttu-id="aac9b-137">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="aac9b-137">AddApiAuthorization</span></span>

<span data-ttu-id="aac9b-138">O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura o [ Identity servidor](https://identityserver.io/) para cenários de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aac9b-138">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="aac9b-139">IdentityO servidor é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-139">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="aac9b-140">IdentityO servidor expõe complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="aac9b-140">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="aac9b-141">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="aac9b-141">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="aac9b-142">Depois que a autenticação precisar ser alterada, todo o poder do Identity servidor estará disponível para personalizar a autenticação de acordo com os requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-142">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="aac9b-143">Adicionar Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="aac9b-143">AddIdentityServerJwt</span></span>

<span data-ttu-id="aac9b-144">O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="aac9b-144">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="aac9b-145">A política está configurada para permitir que o Identity manipule todas as solicitações roteadas para qualquer subcaminho no Identity espaço de URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-145">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="aac9b-146">O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="aac9b-146">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="aac9b-147">Além disso, esse método:</span><span class="sxs-lookup"><span data-stu-id="aac9b-147">Additionally, this method:</span></span>

* <span data-ttu-id="aac9b-148">Registra um `{APPLICATION NAME}API` recurso de API com Identity o servidor com um escopo padrão de `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-148">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="aac9b-149">Configura o middleware do token de portador JWT para validar tokens emitidos pelo Identity servidor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-149">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="aac9b-150">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="aac9b-150">WeatherForecastController</span></span>

<span data-ttu-id="aac9b-151">No `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), o [ `[Authorize]` atributo](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) é aplicado à classe.</span><span class="sxs-lookup"><span data-stu-id="aac9b-151">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) is applied to the class.</span></span> <span data-ttu-id="aac9b-152">O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="aac9b-152">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="aac9b-153">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="aac9b-153">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="aac9b-154">O método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> é configurado como o manipulador padrão para solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-154">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="aac9b-155">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="aac9b-155">ApplicationDbContext</span></span>

<span data-ttu-id="aac9b-156">No `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende- <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se para incluir o esquema para o Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-156">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="aac9b-157"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> é derivado de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="aac9b-157"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="aac9b-158">Para obter controle total do esquema de banco de dados, herde de uma das Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponíveis e configure o contexto para incluir o Identity esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> método.</span><span class="sxs-lookup"><span data-stu-id="aac9b-158">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="aac9b-159">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="aac9b-159">OidcConfigurationController</span></span>

<span data-ttu-id="aac9b-160">No `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.</span><span class="sxs-lookup"><span data-stu-id="aac9b-160">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="aac9b-161">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="aac9b-161">App settings</span></span>

<span data-ttu-id="aac9b-162">No arquivo de configurações do aplicativo ( `appsettings.json` ) na raiz do projeto, a `IdentityServer` seção descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="aac9b-162">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="aac9b-163">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="aac9b-163">In the following example, there's a single client.</span></span> <span data-ttu-id="aac9b-164">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="aac9b-164">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="aac9b-165">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="aac9b-165">The profile indicates the app type being configured.</span></span> <span data-ttu-id="aac9b-166">O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-166">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="aac9b-167">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="aac9b-167">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="aac9b-168">*`Client`* configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="aac9b-168">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="aac9b-169">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="aac9b-169">Authentication package</span></span>

<span data-ttu-id="aac9b-170">Quando um aplicativo é criado para usar contas de usuário individuais ( `Individual` ), o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-170">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="aac9b-171">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="aac9b-171">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="aac9b-172">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="aac9b-172">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="aac9b-173">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="aac9b-173">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="aac9b-174">`HttpClient` configuração</span><span class="sxs-lookup"><span data-stu-id="aac9b-174">`HttpClient` configuration</span></span>

<span data-ttu-id="aac9b-175">Em `Program.Main` ( `Program.cs` ), um nome <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) é configurado para fornecer <xref:System.Net.Http.HttpClient> instâncias que incluem tokens de acesso ao fazer solicitações para a API do servidor:</span><span class="sxs-lookup"><span data-stu-id="aac9b-175">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="aac9b-176">Se você estiver configurando um Blazor WebAssembly aplicativo para usar uma Identity instância de servidor existente que não faz parte de uma solução hospedada Blazor , altere o <xref:System.Net.Http.HttpClient> registro do endereço base de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) para a URL do ponto de extremidade de autorização da API do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-176">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="aac9b-177">Suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="aac9b-177">API authorization support</span></span>

<span data-ttu-id="aac9b-178">O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote.</span><span class="sxs-lookup"><span data-stu-id="aac9b-178">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="aac9b-179">Esse método configura os serviços exigidos pelo aplicativo para interagir com o sistema de autorização existente.</span><span class="sxs-lookup"><span data-stu-id="aac9b-179">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="aac9b-180">Por padrão, a configuração para o aplicativo é carregada por convenção de `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-180">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="aac9b-181">Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-181">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="aac9b-182">Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.</span><span class="sxs-lookup"><span data-stu-id="aac9b-182">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="aac9b-183">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="aac9b-183">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="aac9b-184">Página de índice</span><span class="sxs-lookup"><span data-stu-id="aac9b-184">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="aac9b-185">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="aac9b-185">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="aac9b-186">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="aac9b-186">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="aac9b-187">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="aac9b-187">LoginDisplay component</span></span>

<span data-ttu-id="aac9b-188">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="aac9b-188">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="aac9b-189">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="aac9b-189">For authenticated users:</span></span>
  * <span data-ttu-id="aac9b-190">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="aac9b-190">Displays the current user name.</span></span>
  * <span data-ttu-id="aac9b-191">Oferece um link para a página de perfil do usuário no ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="aac9b-191">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="aac9b-192">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-192">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="aac9b-193">Para usuários anônimos:</span><span class="sxs-lookup"><span data-stu-id="aac9b-193">For anonymous users:</span></span>
  * <span data-ttu-id="aac9b-194">Oferece a opção de se registrar.</span><span class="sxs-lookup"><span data-stu-id="aac9b-194">Offers the option to register.</span></span>
  * <span data-ttu-id="aac9b-195">Oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="aac9b-195">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="aac9b-196">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="aac9b-196">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="aac9b-197">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="aac9b-197">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="aac9b-198">Execute o aplicativo</span><span class="sxs-lookup"><span data-stu-id="aac9b-198">Run the app</span></span>

<span data-ttu-id="aac9b-199">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-199">Run the app from the Server project.</span></span> <span data-ttu-id="aac9b-200">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="aac9b-200">When using Visual Studio, either:</span></span>

* <span data-ttu-id="aac9b-201">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-201">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="aac9b-202">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-202">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="aac9b-203">Nome e declaração de função com autorização de API</span><span class="sxs-lookup"><span data-stu-id="aac9b-203">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="aac9b-204">Fábrica de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="aac9b-204">Custom user factory</span></span>

<span data-ttu-id="aac9b-205">No *`Client`* aplicativo, crie uma fábrica de usuário personalizada.</span><span class="sxs-lookup"><span data-stu-id="aac9b-205">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="aac9b-206">Identity O servidor envia várias funções como uma matriz JSON em uma única `role` declaração.</span><span class="sxs-lookup"><span data-stu-id="aac9b-206">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="aac9b-207">Uma única função é enviada como um valor de cadeia de caracteres na declaração.</span><span class="sxs-lookup"><span data-stu-id="aac9b-207">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="aac9b-208">A fábrica cria uma `role` declaração individual para cada uma das funções do usuário.</span><span class="sxs-lookup"><span data-stu-id="aac9b-208">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="aac9b-209">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="aac9b-209">`CustomUserFactory.cs`:</span></span>

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
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

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

<span data-ttu-id="aac9b-210">No *`Client`* aplicativo, registre a fábrica em `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="aac9b-210">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="aac9b-211">No *`Server`* aplicativo, chame <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> no Identity Construtor, que adiciona serviços relacionados a funções:</span><span class="sxs-lookup"><span data-stu-id="aac9b-211">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="aac9b-212">Configurar Identity servidor</span><span class="sxs-lookup"><span data-stu-id="aac9b-212">Configure Identity Server</span></span>

<span data-ttu-id="aac9b-213">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="aac9b-213">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="aac9b-214">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="aac9b-214">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="aac9b-215">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="aac9b-215">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="aac9b-216">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="aac9b-216">API authorization options</span></span>

<span data-ttu-id="aac9b-217">No *`Server`* aplicativo:</span><span class="sxs-lookup"><span data-stu-id="aac9b-217">In the *`Server`* app:</span></span>

* <span data-ttu-id="aac9b-218">Configure Identity o servidor para colocar o `name` e as `role` declarações no token de ID e no token de acesso.</span><span class="sxs-lookup"><span data-stu-id="aac9b-218">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="aac9b-219">Impedir o mapeamento padrão para funções no manipulador de tokens JWT.</span><span class="sxs-lookup"><span data-stu-id="aac9b-219">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="aac9b-220">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="aac9b-220">Profile Service</span></span>

<span data-ttu-id="aac9b-221">No *`Server`* aplicativo, crie uma `ProfileService` implementação.</span><span class="sxs-lookup"><span data-stu-id="aac9b-221">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="aac9b-222">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="aac9b-222">`ProfileService.cs`:</span></span>

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

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="aac9b-223">No *`Server`* aplicativo, registre o serviço de perfil em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="aac9b-223">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="aac9b-224">Usar mecanismos de autorização</span><span class="sxs-lookup"><span data-stu-id="aac9b-224">Use authorization mechanisms</span></span>

<span data-ttu-id="aac9b-225">No *`Client`* aplicativo, as abordagens de autorização de componente são funcionais neste ponto.</span><span class="sxs-lookup"><span data-stu-id="aac9b-225">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="aac9b-226">Qualquer um dos mecanismos de autorização nos componentes pode usar uma função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="aac9b-226">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="aac9b-227">[ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="aac9b-227">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="aac9b-228">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="aac9b-228">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="aac9b-229">[Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="aac9b-229">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="aac9b-230">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="aac9b-230">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="aac9b-231">`User.Identity.Name` é populado no *`Client`* aplicativo com o nome de usuário do usuário, que geralmente é seu endereço de email de entrada.</span><span class="sxs-lookup"><span data-stu-id="aac9b-231">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain-and-certificate"></a><span data-ttu-id="aac9b-232">Host no serviço de Azure App com um domínio e certificado personalizados</span><span class="sxs-lookup"><span data-stu-id="aac9b-232">Host in Azure App Service with a custom domain and certificate</span></span>

<span data-ttu-id="aac9b-233">As diretrizes a seguir explicam:</span><span class="sxs-lookup"><span data-stu-id="aac9b-233">The following guidance explains:</span></span>

* <span data-ttu-id="aac9b-234">Como implantar um aplicativo hospedado Blazor WebAssembly com Identity o servidor para [Azure app serviço](https://azure.microsoft.com/services/app-service/) com um domínio personalizado.</span><span class="sxs-lookup"><span data-stu-id="aac9b-234">How to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>
* <span data-ttu-id="aac9b-235">Como criar e usar um certificado TLS para comunicação de protocolo HTTPS com navegadores.</span><span class="sxs-lookup"><span data-stu-id="aac9b-235">How to create and use a TLS certificate for HTTPS protocol communication with browsers.</span></span> <span data-ttu-id="aac9b-236">Embora a orientação se concentre em usar o certificado com um domínio personalizado, as diretrizes são igualmente aplicáveis ao uso de um domínio padrão de aplicativos do Azure, por exemplo `contoso.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-236">Although the guidance focuses on using the certificate with a custom domain, the guidance is equally applicable to using a default Azure Apps domain, for example `contoso.azurewebsites.net`.</span></span>

<span data-ttu-id="aac9b-237">Para esse cenário de hospedagem, **não use o** mesmo certificado para a [ Identity chave de assinatura de token do servidor](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) e a comunicação https segura do site com navegadores:</span><span class="sxs-lookup"><span data-stu-id="aac9b-237">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="aac9b-238">Usar certificados diferentes para esses dois requisitos é uma boa prática de segurança porque isola chaves privadas para cada finalidade.</span><span class="sxs-lookup"><span data-stu-id="aac9b-238">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="aac9b-239">Os certificados TLS para comunicação com navegadores são gerenciados de forma independente sem afetar a Identity assinatura de token do servidor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-239">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="aac9b-240">Quando [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fornece um certificado a um aplicativo do serviço de aplicativo para associação de domínio personalizada, Identity o servidor não pode obter o mesmo certificado de Azure Key Vault para assinatura de token.</span><span class="sxs-lookup"><span data-stu-id="aac9b-240">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="aac9b-241">Embora Identity seja possível configurar o servidor para usar o mesmo certificado TLS de um caminho físico, colocar os certificados de segurança no controle do código-fonte é uma **prática inadequada e deve ser evitado na maioria dos cenários**.</span><span class="sxs-lookup"><span data-stu-id="aac9b-241">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="aac9b-242">Nas diretrizes a seguir, um certificado autoassinado é criado em Azure Key Vault exclusivamente para Identity assinatura de token do servidor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-242">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="aac9b-243">A Identity configuração do servidor usa o certificado do Key Vault por meio do repositório de certificados do aplicativo `My`  >  `CurrentUser` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-243">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="aac9b-244">Outros certificados usados para tráfego HTTPS com domínios personalizados são criados e configurados separadamente do Identity certificado de autenticação do servidor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-244">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="aac9b-245">Para configurar um aplicativo, Azure App serviço e Azure Key Vault para hospedar com um domínio personalizado e HTTPS:</span><span class="sxs-lookup"><span data-stu-id="aac9b-245">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="aac9b-246">Crie um [plano do serviço de aplicativo](/azure/app-service/overview-hosting-plans) com um nível de plano `Basic B1` ou superior.</span><span class="sxs-lookup"><span data-stu-id="aac9b-246">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="aac9b-247">O serviço de aplicativo requer uma `Basic B1` camada de serviço ou mais alta para usar domínios personalizados.</span><span class="sxs-lookup"><span data-stu-id="aac9b-247">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="aac9b-248">Crie um certificado PFX para a comunicação segura do navegador do site (protocolo HTTPS) com um nome comum do FQDN (nome de domínio totalmente qualificado) do site que sua organização controla (por exemplo, `www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="aac9b-248">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="aac9b-249">Crie o certificado com:</span><span class="sxs-lookup"><span data-stu-id="aac9b-249">Create the certificate with:</span></span>
   * <span data-ttu-id="aac9b-250">Usos de chave</span><span class="sxs-lookup"><span data-stu-id="aac9b-250">Key uses</span></span>
     * <span data-ttu-id="aac9b-251">Validação de assinatura digital ( `digitalSignature` )</span><span class="sxs-lookup"><span data-stu-id="aac9b-251">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="aac9b-252">Codificação de chave ( `keyEncipherment` )</span><span class="sxs-lookup"><span data-stu-id="aac9b-252">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="aac9b-253">Uso avançado/estendido de chave</span><span class="sxs-lookup"><span data-stu-id="aac9b-253">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="aac9b-254">Autenticação de cliente (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="aac9b-254">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="aac9b-255">Autenticação de servidor (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="aac9b-255">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="aac9b-256">Para criar o certificado, use uma das abordagens a seguir ou qualquer outra ferramenta ou serviço online adequado:</span><span class="sxs-lookup"><span data-stu-id="aac9b-256">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="aac9b-257">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="aac9b-257">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="aac9b-258">MakeCert no Windows</span><span class="sxs-lookup"><span data-stu-id="aac9b-258">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="aac9b-259">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="aac9b-259">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="aac9b-260">Anote a senha, que será usada posteriormente para importar o certificado para o Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="aac9b-260">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="aac9b-261">Para obter mais informações sobre certificados Azure Key Vault, consulte [Azure Key Vault: certificados](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="aac9b-261">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="aac9b-262">Crie um novo Azure Key Vault ou use um cofre de chaves existente em sua assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="aac9b-262">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="aac9b-263">Na área **certificados** do Key Vault, importe o certificado do site PFX.</span><span class="sxs-lookup"><span data-stu-id="aac9b-263">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="aac9b-264">Registre a impressão digital do certificado, que será usada na configuração do aplicativo mais tarde.</span><span class="sxs-lookup"><span data-stu-id="aac9b-264">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="aac9b-265">No Azure Key Vault, gere um novo certificado autoassinado para Identity assinatura de token do servidor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-265">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="aac9b-266">Dê ao certificado um **nome de certificado** e **assunto**.</span><span class="sxs-lookup"><span data-stu-id="aac9b-266">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="aac9b-267">O **assunto** é especificado como `CN={COMMON NAME}` , em que o `{COMMON NAME}` espaço reservado é o nome comum do certificado.</span><span class="sxs-lookup"><span data-stu-id="aac9b-267">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="aac9b-268">O nome comum pode ser qualquer cadeia de caracteres alfanumérica.</span><span class="sxs-lookup"><span data-stu-id="aac9b-268">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="aac9b-269">Por exemplo, `CN=IdentityServerSigning` é uma **entidade** de certificado válida.</span><span class="sxs-lookup"><span data-stu-id="aac9b-269">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="aac9b-270">Use as definições de **configuração de política avançada** padrão.</span><span class="sxs-lookup"><span data-stu-id="aac9b-270">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="aac9b-271">Registre a impressão digital do certificado, que será usada na configuração do aplicativo mais tarde.</span><span class="sxs-lookup"><span data-stu-id="aac9b-271">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="aac9b-272">Navegue até Azure App serviço no portal do Azure e crie um novo serviço de aplicativo com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="aac9b-272">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="aac9b-273">**Publicar** conjunto como `Code` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-273">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="aac9b-274">Conjunto de **pilhas em tempo de execução** para o tempo de execução do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-274">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="aac9b-275">Para **SKU e tamanho**, confirme se a camada do serviço de aplicativo é `Basic B1` ou superior.</span><span class="sxs-lookup"><span data-stu-id="aac9b-275">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="aac9b-276">O serviço de aplicativo requer uma `Basic B1` camada de serviço ou mais alta para usar domínios personalizados.</span><span class="sxs-lookup"><span data-stu-id="aac9b-276">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="aac9b-277">Depois que o Azure criar o serviço de aplicativo, abra a **configuração** do aplicativo e adicione uma nova configuração de aplicativo especificando as impressões digitais do certificado registradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="aac9b-277">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="aac9b-278">A chave de configuração do aplicativo é `WEBSITE_LOAD_CERTIFICATES` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-278">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="aac9b-279">Separe as impressões digitais do certificado no valor de configuração do aplicativo com uma vírgula, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="aac9b-279">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="aac9b-280">Chave: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="aac9b-280">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="aac9b-281">Valor: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="aac9b-281">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="aac9b-282">No portal do Azure, salvar as configurações do aplicativo é um processo de duas etapas: Salve a `WEBSITE_LOAD_CERTIFICATES` configuração chave-valor e, em seguida, selecione o botão **salvar** na parte superior da folha.</span><span class="sxs-lookup"><span data-stu-id="aac9b-282">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="aac9b-283">Selecione as configurações de **TLS/SSL** do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-283">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="aac9b-284">Selecione **certificados de chave privada (. pfx)**.</span><span class="sxs-lookup"><span data-stu-id="aac9b-284">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="aac9b-285">Use o processo **importar Key Vault certificado** duas vezes para importar o certificado do site para comunicação HTTPS e o Identity certificado de autenticação de tokens do servidor autoassinado do site.</span><span class="sxs-lookup"><span data-stu-id="aac9b-285">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="aac9b-286">Navegue até a folha **domínios personalizados** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-286">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="aac9b-287">No site do registrador de domínio, use o **endereço IP** e a **ID de verificação de domínio personalizado** para configurar o domínio.</span><span class="sxs-lookup"><span data-stu-id="aac9b-287">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="aac9b-288">Uma configuração de domínio típica inclui:</span><span class="sxs-lookup"><span data-stu-id="aac9b-288">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="aac9b-289">Um **registro a** com um **host** de `@` e um valor do endereço IP do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="aac9b-289">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="aac9b-290">Um **registro txt** com um **host** de `asuid` e o valor da ID de verificação gerado pelo Azure e fornecidos pelo portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="aac9b-290">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="aac9b-291">Certifique-se de salvar as alterações no site do registrador de domínio corretamente.</span><span class="sxs-lookup"><span data-stu-id="aac9b-291">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="aac9b-292">Alguns sites do registrador exigem um processo de duas etapas para salvar registros de domínio: um ou mais registros são salvos individualmente, atualizando o registro do domínio com um botão separado.</span><span class="sxs-lookup"><span data-stu-id="aac9b-292">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="aac9b-293">Retorne para a folha **domínios personalizados** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="aac9b-293">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="aac9b-294">Selecione **Adicionar domínio personalizado**.</span><span class="sxs-lookup"><span data-stu-id="aac9b-294">Select **Add custom domain**.</span></span> <span data-ttu-id="aac9b-295">Selecione a opção **um registro** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-295">Select the **A Record** option.</span></span> <span data-ttu-id="aac9b-296">Forneça o domínio e selecione **validar**.</span><span class="sxs-lookup"><span data-stu-id="aac9b-296">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="aac9b-297">Se os registros de domínio estiverem corretos e propagados pela Internet, o portal permitirá que você selecione o botão **Adicionar domínio personalizado** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-297">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="aac9b-298">Pode levar alguns dias para que as alterações no registro do domínio se propaguem entre os servidores de nomes de domínio da Internet (DNS) depois que eles forem processados pelo seu registrador de domínio.</span><span class="sxs-lookup"><span data-stu-id="aac9b-298">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="aac9b-299">Se os registros de domínio não forem atualizados dentro de três dias úteis, confirme se os registros estão definidos corretamente com o registrador de domínios e entre em contato com o atendimento ao cliente.</span><span class="sxs-lookup"><span data-stu-id="aac9b-299">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="aac9b-300">Na folha **domínios personalizados** , o **estado do SSL** para o domínio é marcado `Not Secure` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-300">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="aac9b-301">Selecione o link **Adicionar Associação** .</span><span class="sxs-lookup"><span data-stu-id="aac9b-301">Select the **Add binding** link.</span></span> <span data-ttu-id="aac9b-302">Selecione o certificado HTTPS do site do cofre de chaves para a associação de domínio personalizada.</span><span class="sxs-lookup"><span data-stu-id="aac9b-302">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="aac9b-303">No Visual Studio, abra o arquivo de configurações do aplicativo do projeto do *servidor* ( `appsettings.json` ou `appsettings.Production.json` ).</span><span class="sxs-lookup"><span data-stu-id="aac9b-303">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="aac9b-304">Na Identity configuração do servidor, adicione a seção a seguir `Key` .</span><span class="sxs-lookup"><span data-stu-id="aac9b-304">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="aac9b-305">Especifique a **entidade** do certificado autoassinado para a `Name` chave.</span><span class="sxs-lookup"><span data-stu-id="aac9b-305">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="aac9b-306">No exemplo a seguir, o nome comum do certificado atribuído no cofre de chaves é `IdentityServerSigning` , que gera um **assunto** de `CN=IdentityServerSigning` :</span><span class="sxs-lookup"><span data-stu-id="aac9b-306">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="aac9b-307">No Visual Studio, crie um perfil de [publicação](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) de serviço Azure app para o projeto de *servidor* .</span><span class="sxs-lookup"><span data-stu-id="aac9b-307">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="aac9b-308">Na barra de menus, selecione: **Compilar**  >  **publicar**  >  **novo**  >    >  **serviço de Azure app** do Azure (Windows ou Linux).</span><span class="sxs-lookup"><span data-stu-id="aac9b-308">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="aac9b-309">Quando o Visual Studio está conectado a uma assinatura do Azure, você pode definir a **exibição** de recursos do Azure por **tipo de recurso**.</span><span class="sxs-lookup"><span data-stu-id="aac9b-309">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="aac9b-310">Navegue dentro da lista de **aplicativos Web** para localizar o serviço de aplicativo para o aplicativo e selecioná-lo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-310">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="aac9b-311">Selecione **Concluir**.</span><span class="sxs-lookup"><span data-stu-id="aac9b-311">Select **Finish**.</span></span>
1. <span data-ttu-id="aac9b-312">Quando o Visual Studio retorna para a janela de **publicação** , as dependências do cofre de chaves e do serviço de banco de dados SQL Server são detectadas automaticamente.</span><span class="sxs-lookup"><span data-stu-id="aac9b-312">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="aac9b-313">Nenhuma alteração de configuração nas configurações padrão é necessária para o serviço de cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="aac9b-313">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="aac9b-314">Para fins de teste, o banco de dados [SQLite](https://www.sqlite.org/index.html) local de um aplicativo, que é configurado por padrão pelo Blazor modelo, pode ser implantado com o aplicativo sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="aac9b-314">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="aac9b-315">A configuração de um banco de dados diferente para o Identity servidor em produção está além do escopo deste artigo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-315">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="aac9b-316">Para obter mais informações, consulte os recursos de banco de dados nos seguintes conjuntos de documentação:</span><span class="sxs-lookup"><span data-stu-id="aac9b-316">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="aac9b-317">Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="aac9b-317">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="aac9b-318">Identity Servidor</span><span class="sxs-lookup"><span data-stu-id="aac9b-318">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="aac9b-319">Selecione o link **Editar** sob o nome do perfil de implantação na parte superior da janela.</span><span class="sxs-lookup"><span data-stu-id="aac9b-319">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="aac9b-320">Altere a URL de destino para a URL de domínio personalizado do site (por exemplo, `https://www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="aac9b-320">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="aac9b-321">Salve as configurações.</span><span class="sxs-lookup"><span data-stu-id="aac9b-321">Save the settings.</span></span>
1. <span data-ttu-id="aac9b-322">Publique o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aac9b-322">Publish the app.</span></span> <span data-ttu-id="aac9b-323">O Visual Studio abre uma janela do navegador e solicita o site em seu domínio personalizado.</span><span class="sxs-lookup"><span data-stu-id="aac9b-323">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="aac9b-324">A documentação do Azure contém detalhes adicionais sobre como usar os serviços do Azure e domínios personalizados com a associação TLS no serviço de aplicativo, incluindo informações sobre como usar registros CNAME em vez de registros.</span><span class="sxs-lookup"><span data-stu-id="aac9b-324">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="aac9b-325">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="aac9b-325">For more information, see the following resources:</span></span>

* [<span data-ttu-id="aac9b-326">Documentação do Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="aac9b-326">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="aac9b-327">Tutorial: Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="aac9b-327">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="aac9b-328">Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="aac9b-328">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="aac9b-329">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="aac9b-329">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="aac9b-330">É recomendável usar uma nova janela de navegador em particular ou Incognito para cada execução de teste de aplicativo após uma alteração no aplicativo, configuração de aplicativo ou serviços do Azure no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="aac9b-330">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="aac9b-331">cookieOs s remanescentes de uma execução de teste anterior podem resultar em falha na autenticação ou autorização ao testar o site mesmo quando a configuração do site está correta.</span><span class="sxs-lookup"><span data-stu-id="aac9b-331">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="aac9b-332">Para obter mais informações sobre como configurar o Visual Studio para abrir uma nova janela de navegador em particular ou Incognito para cada execução de teste, consulte a seção [ Cookie s e dados do site](#cookies-and-site-data) .</span><span class="sxs-lookup"><span data-stu-id="aac9b-332">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="aac9b-333">Quando a configuração do serviço de aplicativo é alterada na portal do Azure, as atualizações geralmente entram em vigor rapidamente, mas não são instantâneas.</span><span class="sxs-lookup"><span data-stu-id="aac9b-333">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="aac9b-334">Às vezes, você deve aguardar um curto período para que um serviço de aplicativo seja reiniciado para que uma alteração de configuração entre em vigor.</span><span class="sxs-lookup"><span data-stu-id="aac9b-334">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="aac9b-335">Se estiver solucionando um problema de carregamento de certificado, execute o seguinte comando em um shell de comando portal do Azure [kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell.</span><span class="sxs-lookup"><span data-stu-id="aac9b-335">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="aac9b-336">O comando fornece uma lista de certificados que o aplicativo pode acessar do `My`  >  `CurrentUser` repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="aac9b-336">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="aac9b-337">A saída inclui entidades de certificado e impressões digitais úteis ao depurar um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="aac9b-337">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="aac9b-338">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="aac9b-338">Additional resources</span></span>

* [<span data-ttu-id="aac9b-339">Implantação no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="aac9b-339">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="aac9b-340">Importar um certificado do Key Vault (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="aac9b-340">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="aac9b-341">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="aac9b-341">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="aac9b-342"><xref:host-and-deploy/proxy-load-balancer>: Inclui diretrizes sobre:</span><span class="sxs-lookup"><span data-stu-id="aac9b-342"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="aac9b-343">Usando o middleware de cabeçalhos encaminhados para preservar as informações do esquema HTTPS entre servidores proxy e redes internas.</span><span class="sxs-lookup"><span data-stu-id="aac9b-343">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="aac9b-344">Cenários adicionais e casos de uso, incluindo configuração de esquema manual, alterações de caminho de solicitação para roteamento de solicitação correto e encaminhamento do esquema de solicitação para proxies inversos do Linux e não do IIS.</span><span class="sxs-lookup"><span data-stu-id="aac9b-344">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
