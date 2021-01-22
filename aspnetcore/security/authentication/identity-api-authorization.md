---
title: Introdução à autenticação para aplicativos de página única no ASP.NET Core
author: javiercn
description: Use Identity com um aplicativo de página única hospedado dentro de um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658658"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="94e2a-103">Autenticação e autorização para SPAs</span><span class="sxs-lookup"><span data-stu-id="94e2a-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="94e2a-104">Os modelos ASP.NET Core 3,1 e posterior oferecem autenticação em aplicativos de página única (SPAs) usando o suporte para autorização de API.</span><span class="sxs-lookup"><span data-stu-id="94e2a-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="94e2a-105">ASP.NET Core Identitypara autenticar e armazenar usuários, é combinado com o [ Identity servidor](https://identityserver.io/) para implementar o OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="94e2a-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="94e2a-106">Um parâmetro de autenticação foi adicionado aos modelos de projeto **angular** e **reajam** que é semelhante ao parâmetro de autenticação nos modelos de projeto de **aplicativo Web (Model-View-Controller)** (MVC) e **aplicativo Web** ( Razor páginas).</span><span class="sxs-lookup"><span data-stu-id="94e2a-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="94e2a-107">Os valores de parâmetro permitidos são **None** e **individual**.</span><span class="sxs-lookup"><span data-stu-id="94e2a-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="94e2a-108">O modelo de projeto **React.js e Redux** não dá suporte ao parâmetro de autenticação neste momento.</span><span class="sxs-lookup"><span data-stu-id="94e2a-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="94e2a-109">Criar um aplicativo com suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="94e2a-109">Create an app with API authorization support</span></span>

<span data-ttu-id="94e2a-110">A autenticação e a autorização do usuário podem ser usadas com SPAs angular e reagir.</span><span class="sxs-lookup"><span data-stu-id="94e2a-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="94e2a-111">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="94e2a-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="94e2a-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="94e2a-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="94e2a-113">**Reagir**:</span><span class="sxs-lookup"><span data-stu-id="94e2a-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="94e2a-114">O comando anterior cria um aplicativo ASP.NET Core com um diretório *ClientApp* que contém o Spa.</span><span class="sxs-lookup"><span data-stu-id="94e2a-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="94e2a-115">Descrição geral dos componentes ASP.NET Core do aplicativo</span><span class="sxs-lookup"><span data-stu-id="94e2a-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="94e2a-116">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído:</span><span class="sxs-lookup"><span data-stu-id="94e2a-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="94e2a-117">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="94e2a-117">Startup class</span></span>

<span data-ttu-id="94e2a-118">Os exemplos de código a seguir contam com o [Microsoft. AspNetCore. ApiAuthorization. Identity Pacote NuGet do servidor](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) .</span><span class="sxs-lookup"><span data-stu-id="94e2a-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="94e2a-119">Os exemplos configuram a autenticação e a autorização da API usando os <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> métodos de extensão e.</span><span class="sxs-lookup"><span data-stu-id="94e2a-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="94e2a-120">Os projetos que usam os modelos de projeto de SPA de reagir ou angulares com autenticação incluem uma referência a esse pacote.</span><span class="sxs-lookup"><span data-stu-id="94e2a-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="94e2a-121">A `Startup` classe tem as seguintes adições:</span><span class="sxs-lookup"><span data-stu-id="94e2a-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="94e2a-122">Dentro do `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="94e2a-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="94e2a-123">Identity com a interface do usuário padrão:</span><span class="sxs-lookup"><span data-stu-id="94e2a-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="94e2a-124">IdentityServidor com um `AddApiAuthorization` método auxiliar adicional que configura algumas convenções de ASP.NET Core padrão na parte superior do Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="94e2a-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="94e2a-125">Autenticação com um `AddIdentityServerJwt` método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos pelo Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="94e2a-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="94e2a-126">Dentro do `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="94e2a-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="94e2a-127">O middleware de autenticação que é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="94e2a-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="94e2a-128">O Identity middleware de servidor que expõe os pontos de extremidade do OpenID Connect:</span><span class="sxs-lookup"><span data-stu-id="94e2a-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="94e2a-129">Serviço de Aplicativo do Azure no Linux</span><span class="sxs-lookup"><span data-stu-id="94e2a-129">Azure App Service on Linux</span></span>

<span data-ttu-id="94e2a-130">Para implantações de serviço Azure App no Linux, especifique o emissor explicitamente no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="94e2a-130">For Azure App Service deployments on Linux, specify the issuer explicitly in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

<span data-ttu-id="94e2a-131">No código anterior, o `{AUTHORITY}` espaço reservado é o <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> a ser usado ao fazer chamadas do OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="94e2a-131">In the preceding code, the `{AUTHORITY}` placeholder is the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> to use when making OpenID Connect calls.</span></span>

<span data-ttu-id="94e2a-132">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="94e2a-132">Example:</span></span>

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a><span data-ttu-id="94e2a-133">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="94e2a-133">AddApiAuthorization</span></span>

<span data-ttu-id="94e2a-134">Esse método auxiliar configura o Identity servidor para usar nossa configuração com suporte.</span><span class="sxs-lookup"><span data-stu-id="94e2a-134">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="94e2a-135">IdentityO servidor é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-135">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="94e2a-136">Ao mesmo tempo, isso expõe uma complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="94e2a-136">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="94e2a-137">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para você que são considerados um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="94e2a-137">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="94e2a-138">Depois que a autenticação precisar ser alterada, todo o poder do Identity servidor ainda estará disponível para personalizar a autenticação de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="94e2a-138">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="94e2a-139">Adicionar Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="94e2a-139">AddIdentityServerJwt</span></span>

<span data-ttu-id="94e2a-140">Esse método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="94e2a-140">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="94e2a-141">A política está configurada para permitir que o Identity manipule todas as solicitações roteadas para qualquer subcaminho no Identity espaço de URL "/ Identity ".</span><span class="sxs-lookup"><span data-stu-id="94e2a-141">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="94e2a-142">O `JwtBearerHandler` lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="94e2a-142">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="94e2a-143">Além disso, esse método registra um `<<ApplicationName>>API` recurso de API com Identity o servidor com um escopo padrão `<<ApplicationName>>API` e configura o middleware do token de portador JWT para validar tokens emitidos pelo Identity servidor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-143">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="94e2a-144">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="94e2a-144">WeatherForecastController</span></span>

<span data-ttu-id="94e2a-145">No arquivo *Controllers\WeatherForecastController.cs* , observe o `[Authorize]` atributo aplicado à classe que indica que o usuário precisa ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="94e2a-145">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="94e2a-146">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado até `AddIdentityServerJwt` o esquema de política mencionado acima, tornando o `JwtBearerHandler` método auxiliar configurado por tal como o manipulador padrão para solicitações para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-146">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="94e2a-147">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="94e2a-147">ApplicationDbContext</span></span>

<span data-ttu-id="94e2a-148">No arquivo *Data\ApplicationDbContext.cs* , observe que o mesmo `DbContext` é usado em Identity com a exceção que ele estende `ApiAuthorizationDbContext` (uma classe mais derivada de `IdentityDbContext` ) para incluir o esquema para o Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="94e2a-148">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="94e2a-149">Para obter controle total do esquema de banco de dados, herde de uma das Identity `DbContext` classes disponíveis e configure o contexto para incluir o Identity esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no `OnModelCreating` método.</span><span class="sxs-lookup"><span data-stu-id="94e2a-149">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="94e2a-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="94e2a-150">OidcConfigurationController</span></span>

<span data-ttu-id="94e2a-151">No arquivo *Controllers\OidcConfigurationController.cs* , observe o ponto de extremidade provisionado para atender aos parâmetros OIDC que o cliente precisa usar.</span><span class="sxs-lookup"><span data-stu-id="94e2a-151">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="94e2a-152">No *appsettings.json* arquivo da raiz do projeto, há uma nova `IdentityServer` seção que descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="94e2a-152">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="94e2a-153">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="94e2a-153">In the following example, there's a single client.</span></span> <span data-ttu-id="94e2a-154">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="94e2a-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="94e2a-155">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="94e2a-156">Ele é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="94e2a-156">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="94e2a-157">Há vários perfis disponíveis, conforme explicado na seção [perfis de aplicativo](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="94e2a-157">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="94e2a-158">appsettings.Development.jsem</span><span class="sxs-lookup"><span data-stu-id="94e2a-158">appsettings.Development.json</span></span>

<span data-ttu-id="94e2a-159">No *appsettings.Development.jsno* arquivo da raiz do projeto, há uma `IdentityServer` seção que descreve a chave usada para assinar tokens.</span><span class="sxs-lookup"><span data-stu-id="94e2a-159">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="94e2a-160">Ao implantar na produção, uma chave precisa ser provisionada e implantada juntamente com o aplicativo, conforme explicado na seção [implantar na produção](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="94e2a-160">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="94e2a-161">Descrição geral do aplicativo angular</span><span class="sxs-lookup"><span data-stu-id="94e2a-161">General description of the Angular app</span></span>

<span data-ttu-id="94e2a-162">O suporte de autorização de API e autenticação no modelo angular reside em seu próprio módulo angular no diretório *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="94e2a-162">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="94e2a-163">O módulo é composto pelos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="94e2a-163">The module is composed of the following elements:</span></span>

* <span data-ttu-id="94e2a-164">3 componentes:</span><span class="sxs-lookup"><span data-stu-id="94e2a-164">3 components:</span></span>
  * <span data-ttu-id="94e2a-165">*login. Component. TS*: manipula o fluxo de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-165">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="94e2a-166">*logout. Component. TS*: manipula o fluxo de logout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-166">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="94e2a-167">*login-menu. Component. TS*: um widget que exibe um dos seguintes conjuntos de links:</span><span class="sxs-lookup"><span data-stu-id="94e2a-167">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="94e2a-168">Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-168">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="94e2a-169">Os links de registro e logon quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-169">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="94e2a-170">Uma proteção de rota `AuthorizeGuard` que pode ser adicionada a rotas e requer que um usuário seja autenticado antes de visitar a rota.</span><span class="sxs-lookup"><span data-stu-id="94e2a-170">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="94e2a-171">Um interceptador HTTP `AuthorizeInterceptor` que anexa o token de acesso a solicitações HTTP de saída direcionando a API quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-171">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="94e2a-172">Um serviço `AuthorizeService` que manipula os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-172">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="94e2a-173">Um módulo angular que define as rotas associadas às partes de autenticação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-173">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="94e2a-174">Ele expõe o componente de menu de logon, o Interceptor, a proteção e o serviço para consumo do restante do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-174">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="94e2a-175">Descrição geral do aplicativo reajam</span><span class="sxs-lookup"><span data-stu-id="94e2a-175">General description of the React app</span></span>

<span data-ttu-id="94e2a-176">O suporte para autenticação e autorização de API no modelo reagir reside no diretório *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="94e2a-176">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="94e2a-177">Ele é composto pelos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="94e2a-177">It's composed of the following elements:</span></span>

* <span data-ttu-id="94e2a-178">4 componentes:</span><span class="sxs-lookup"><span data-stu-id="94e2a-178">4 components:</span></span>
  * <span data-ttu-id="94e2a-179">*Login.js*: manipula o fluxo de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-179">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="94e2a-180">*Logout.js*: manipula o fluxo de logout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-180">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="94e2a-181">*LoginMenu.js*: um widget que exibe um dos seguintes conjuntos de links:</span><span class="sxs-lookup"><span data-stu-id="94e2a-181">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="94e2a-182">Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-182">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="94e2a-183">Os links de registro e logon quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-183">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="94e2a-184">*AuthorizeRoute.js*: um componente de rota que exige que um usuário seja autenticado antes de renderizar o componente indicado no `Component` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="94e2a-184">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="94e2a-185">Uma instância exportada `authService` da classe `AuthorizeService` que manipula os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-185">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="94e2a-186">Agora que você já viu os principais componentes da solução, pode fazer uma análise mais profunda de cenários individuais para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-186">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="94e2a-187">Exigir autorização em uma nova API</span><span class="sxs-lookup"><span data-stu-id="94e2a-187">Require authorization on a new API</span></span>

<span data-ttu-id="94e2a-188">Por padrão, o sistema está configurado para exigir facilmente a autorização para novas APIs.</span><span class="sxs-lookup"><span data-stu-id="94e2a-188">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="94e2a-189">Para fazer isso, crie um novo controlador e adicione o `[Authorize]` atributo à classe do controlador ou a qualquer ação dentro do controlador.</span><span class="sxs-lookup"><span data-stu-id="94e2a-189">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="94e2a-190">Personalizar o manipulador de autenticação de API</span><span class="sxs-lookup"><span data-stu-id="94e2a-190">Customize the API authentication handler</span></span>

<span data-ttu-id="94e2a-191">Para personalizar a configuração do manipulador JWT da API, configure sua <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instância:</span><span class="sxs-lookup"><span data-stu-id="94e2a-191">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="94e2a-192">O manipulador JWT da API gera eventos que permitem o controle sobre o processo de autenticação usando `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="94e2a-192">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="94e2a-193">Para fornecer suporte para autorização de API, `AddIdentityServerJwt` o registra seus próprios manipuladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="94e2a-193">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="94e2a-194">Para personalizar o tratamento de um evento, empacote o manipulador de eventos existente com lógica adicional, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="94e2a-194">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="94e2a-195">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="94e2a-195">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="94e2a-196">No código anterior, o `OnTokenValidated` manipulador de eventos é substituído por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="94e2a-196">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="94e2a-197">Esta implementação:</span><span class="sxs-lookup"><span data-stu-id="94e2a-197">This implementation:</span></span>

1. <span data-ttu-id="94e2a-198">Chama a implementação original fornecida pelo suporte à autorização da API.</span><span class="sxs-lookup"><span data-stu-id="94e2a-198">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="94e2a-199">Execute sua própria lógica personalizada.</span><span class="sxs-lookup"><span data-stu-id="94e2a-199">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="94e2a-200">Proteger uma rota do lado do cliente (angular)</span><span class="sxs-lookup"><span data-stu-id="94e2a-200">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="94e2a-201">A proteção de uma rota do lado do cliente é feita pela adição da proteção de autorização à lista de proteções a serem executadas durante a configuração de uma rota.</span><span class="sxs-lookup"><span data-stu-id="94e2a-201">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="94e2a-202">Como exemplo, você pode ver como a `fetch-data` rota é configurada dentro do módulo angular do aplicativo principal:</span><span class="sxs-lookup"><span data-stu-id="94e2a-202">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="94e2a-203">É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele), mas que apenas impede que o usuário navegue até a rota do lado do cliente quando não é autenticado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-203">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="94e2a-204">Autenticar solicitações de API (angular)</span><span class="sxs-lookup"><span data-stu-id="94e2a-204">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="94e2a-205">A autenticação de solicitações para APIs hospedadas juntamente com o aplicativo é feita automaticamente pelo uso do interceptador de cliente HTTP definido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-205">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="94e2a-206">Proteger uma rota do lado do cliente (reagir)</span><span class="sxs-lookup"><span data-stu-id="94e2a-206">Protect a client-side route (React)</span></span>

<span data-ttu-id="94e2a-207">Proteger uma rota do lado do cliente usando o `AuthorizeRoute` componente em vez do `Route` componente simples.</span><span class="sxs-lookup"><span data-stu-id="94e2a-207">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="94e2a-208">Por exemplo, observe como a `fetch-data` rota é configurada dentro do `App` componente:</span><span class="sxs-lookup"><span data-stu-id="94e2a-208">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="94e2a-209">Protegendo uma rota:</span><span class="sxs-lookup"><span data-stu-id="94e2a-209">Protecting a route:</span></span>

* <span data-ttu-id="94e2a-210">Não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele).</span><span class="sxs-lookup"><span data-stu-id="94e2a-210">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="94e2a-211">Impede que o usuário navegue para a rota determinada do lado do cliente quando ele não é autenticado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-211">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="94e2a-212">Autenticar solicitações de API (reagir)</span><span class="sxs-lookup"><span data-stu-id="94e2a-212">Authenticate API requests (React)</span></span>

<span data-ttu-id="94e2a-213">A autenticação de solicitações com reagir é feita pela primeira importação da `authService` instância do `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="94e2a-213">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="94e2a-214">O token de acesso é recuperado do `authService` e é anexado à solicitação, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-214">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="94e2a-215">Em reajam de componentes, esse trabalho normalmente é feito no `componentDidMount` método de ciclo de vida ou como resultado de alguma interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="94e2a-215">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="94e2a-216">Importar o authService para o componente</span><span class="sxs-lookup"><span data-stu-id="94e2a-216">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="94e2a-217">Recuperar e anexar o token de acesso à resposta</span><span class="sxs-lookup"><span data-stu-id="94e2a-217">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="94e2a-218">Implantar para a produção</span><span class="sxs-lookup"><span data-stu-id="94e2a-218">Deploy to production</span></span>

<span data-ttu-id="94e2a-219">Para implantar o aplicativo na produção, os seguintes recursos precisam ser provisionados:</span><span class="sxs-lookup"><span data-stu-id="94e2a-219">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="94e2a-220">Um banco de dados para armazenar as Identity contas de usuário e o Identity servidor concede.</span><span class="sxs-lookup"><span data-stu-id="94e2a-220">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="94e2a-221">Um certificado de produção a ser usado para tokens de assinatura.</span><span class="sxs-lookup"><span data-stu-id="94e2a-221">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="94e2a-222">Não há requisitos específicos para esse certificado; pode ser um certificado autoassinado ou um certificado provisionado por meio de uma autoridade de certificação.</span><span class="sxs-lookup"><span data-stu-id="94e2a-222">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="94e2a-223">Ele pode ser gerado por meio de ferramentas padrão, como o PowerShell ou o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="94e2a-223">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="94e2a-224">Ele pode ser instalado no repositório de certificados nos computadores de destino ou implantado como um arquivo *. pfx* com uma senha forte.</span><span class="sxs-lookup"><span data-stu-id="94e2a-224">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="94e2a-225">Exemplo: implantar em um provedor de hospedagem Web não Azure</span><span class="sxs-lookup"><span data-stu-id="94e2a-225">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="94e2a-226">No painel de hospedagem na Web, crie ou carregue seu certificado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-226">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="94e2a-227">Em seguida, no arquivo do aplicativo *appsettings.json* , modifique a `IdentityServer` seção para incluir os detalhes da chave.</span><span class="sxs-lookup"><span data-stu-id="94e2a-227">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="94e2a-228">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="94e2a-228">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="94e2a-229">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="94e2a-229">In the preceding example:</span></span>

* <span data-ttu-id="94e2a-230">`StoreName` representa o nome do repositório de certificados onde o certificado está armazenado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-230">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="94e2a-231">Nesse caso, ele aponta para o armazenamento de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="94e2a-231">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="94e2a-232">`StoreLocation` representa para onde carregar o certificado ( `CurrentUser` nesse caso).</span><span class="sxs-lookup"><span data-stu-id="94e2a-232">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="94e2a-233">`Name` corresponde ao assunto distinto do certificado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-233">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="94e2a-234">Exemplo: implantar no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="94e2a-234">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="94e2a-235">Esta seção descreve a implantação do aplicativo no serviço Azure App usando um certificado armazenado no repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="94e2a-235">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="94e2a-236">Para modificar o aplicativo para carregar um certificado do repositório de certificados, um plano de serviço de camada Standard ou melhor é necessário quando você configura o aplicativo no portal do Azure em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="94e2a-236">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="94e2a-237">No arquivo do aplicativo *appsettings.json* , modifique a `IdentityServer` seção para incluir os detalhes da chave:</span><span class="sxs-lookup"><span data-stu-id="94e2a-237">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="94e2a-238">O nome do repositório representa o nome do repositório de certificados onde o certificado está armazenado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-238">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="94e2a-239">Nesse caso, ele aponta para o repositório de usuários pessoais.</span><span class="sxs-lookup"><span data-stu-id="94e2a-239">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="94e2a-240">O local do repositório representa onde carregar o certificado ( `CurrentUser` ou `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="94e2a-240">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="94e2a-241">A propriedade Name no certificado corresponde ao assunto distinto do certificado.</span><span class="sxs-lookup"><span data-stu-id="94e2a-241">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="94e2a-242">Para implantar o serviço Azure App, siga as etapas em [implantar o aplicativo no Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), que explica como criar os recursos do Azure necessários e implantar o aplicativo para produção.</span><span class="sxs-lookup"><span data-stu-id="94e2a-242">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="94e2a-243">Depois de seguir as instruções anteriores, o aplicativo é implantado no Azure, mas ainda não está funcional.</span><span class="sxs-lookup"><span data-stu-id="94e2a-243">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="94e2a-244">O certificado usado pelo aplicativo deve ser configurado no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="94e2a-244">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="94e2a-245">Localize a impressão digital para o certificado e siga as etapas descritas em [carregar seus certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="94e2a-245">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="94e2a-246">Embora essas etapas mencionem o SSL, há uma seção de **certificados privados** no portal do Azure em que você pode carregar o certificado provisionado para usar com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-246">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="94e2a-247">Depois de configurar o aplicativo e as configurações do aplicativo no portal do Azure, reinicie o aplicativo no Portal.</span><span class="sxs-lookup"><span data-stu-id="94e2a-247">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="94e2a-248">Outras opções de configuração</span><span class="sxs-lookup"><span data-stu-id="94e2a-248">Other configuration options</span></span>

<span data-ttu-id="94e2a-249">O suporte para a autorização da API baseia-se na parte superior do Identity servidor com um conjunto de convenções, valores padrão e aprimoramentos para simplificar a experiência do spas.</span><span class="sxs-lookup"><span data-stu-id="94e2a-249">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="94e2a-250">Não é necessário dizer que todo o poder do Identity servidor está disponível nos bastidores se as integrações do ASP.NET Core não abrangem seu cenário.</span><span class="sxs-lookup"><span data-stu-id="94e2a-250">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="94e2a-251">O suporte a ASP.NET Core concentra-se em aplicativos de "primeira parte", onde todos os aplicativos são criados e implantados pela nossa organização.</span><span class="sxs-lookup"><span data-stu-id="94e2a-251">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="94e2a-252">Dessa forma, o suporte não é oferecido para coisas como autorização ou Federação.</span><span class="sxs-lookup"><span data-stu-id="94e2a-252">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="94e2a-253">Para esses cenários, use Identity o servidor e siga sua documentação.</span><span class="sxs-lookup"><span data-stu-id="94e2a-253">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="94e2a-254">Perfis de aplicativo</span><span class="sxs-lookup"><span data-stu-id="94e2a-254">Application profiles</span></span>

<span data-ttu-id="94e2a-255">Perfis de aplicativo são configurações predefinidas para aplicativos que definem ainda mais seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="94e2a-255">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="94e2a-256">Neste momento, há suporte para os seguintes perfis:</span><span class="sxs-lookup"><span data-stu-id="94e2a-256">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="94e2a-257">`IdentityServerSPA`: Representa um SPA hospedado junto Identity com o servidor como uma única unidade.</span><span class="sxs-lookup"><span data-stu-id="94e2a-257">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="94e2a-258">O `redirect_uri` padrão é `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="94e2a-258">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="94e2a-259">O `post_logout_redirect_uri` padrão é `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="94e2a-259">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="94e2a-260">O conjunto de escopos inclui o `openid` , o `profile` e todos os escopos definidos para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="94e2a-261">O conjunto de tipos de resposta permitidos OIDC é `id_token token` ou cada um deles individualmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="94e2a-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="94e2a-262">O modo de resposta permitido é `fragment` .</span><span class="sxs-lookup"><span data-stu-id="94e2a-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="94e2a-263">`SPA`: Representa um SPA que não é hospedado com o Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="94e2a-263">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="94e2a-264">O conjunto de escopos inclui o `openid` , o `profile` e todos os escopos definidos para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-264">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="94e2a-265">O conjunto de tipos de resposta permitidos OIDC é `id_token token` ou cada um deles individualmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="94e2a-265">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="94e2a-266">O modo de resposta permitido é `fragment` .</span><span class="sxs-lookup"><span data-stu-id="94e2a-266">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="94e2a-267">`IdentityServerJwt`: Representa uma API que é hospedada junto com o Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="94e2a-267">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="94e2a-268">O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-268">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="94e2a-269">`API`: Representa uma API que não é hospedada com o Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="94e2a-269">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="94e2a-270">O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="94e2a-270">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="94e2a-271">Configuração através de AppSettings</span><span class="sxs-lookup"><span data-stu-id="94e2a-271">Configuration through AppSettings</span></span>

<span data-ttu-id="94e2a-272">Configure os aplicativos por meio do sistema de configuração adicionando-os à lista de `Clients` ou `Resources` .</span><span class="sxs-lookup"><span data-stu-id="94e2a-272">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="94e2a-273">Configure cada cliente `redirect_uri` e `post_logout_redirect_uri` propriedade, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="94e2a-273">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="94e2a-274">Ao configurar recursos, você pode configurar os escopos para o recurso, conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="94e2a-274">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="94e2a-275">Configuração por meio de código</span><span class="sxs-lookup"><span data-stu-id="94e2a-275">Configuration through code</span></span>

<span data-ttu-id="94e2a-276">Você também pode configurar os clientes e os recursos por meio de código usando uma sobrecarga do `AddApiAuthorization` que executa uma ação para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="94e2a-276">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="94e2a-277">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="94e2a-277">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
