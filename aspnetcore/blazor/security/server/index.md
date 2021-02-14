---
title: Proteger ASP.NET Core Blazor Server aplicativos
author: guardrex
description: Saiba como proteger aplicativos Blazor Server como ASP.NET Core aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5a3d3c6e06653de7f0d01565444d37013f347a5b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280316"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="73efe-103">Proteger ASP.NET Core Blazor Server aplicativos</span><span class="sxs-lookup"><span data-stu-id="73efe-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="73efe-104">Blazor Server os aplicativos são configurados para segurança da mesma maneira que ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="73efe-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="73efe-105">Para obter mais informações, consulte os artigos em <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="73efe-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="73efe-106">Os tópicos desta visão geral se aplicam especificamente ao Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="73efe-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="73efe-107">Blazor Server modelo de projeto</span><span class="sxs-lookup"><span data-stu-id="73efe-107">Blazor Server project template</span></span>

<span data-ttu-id="73efe-108">O Blazor Server modelo de projeto pode ser configurado para autenticação quando o projeto é criado.</span><span class="sxs-lookup"><span data-stu-id="73efe-108">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="73efe-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73efe-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73efe-110">Siga as diretrizes do Visual Studio no <xref:blazor/tooling> para criar um novo Blazor Server projeto com um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="73efe-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="73efe-111">Depois de escolher o modelo de **Blazor Server aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="73efe-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="73efe-112">Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="73efe-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="73efe-113">**Sem autenticação**</span><span class="sxs-lookup"><span data-stu-id="73efe-113">**No Authentication**</span></span>
* <span data-ttu-id="73efe-114">**Contas de usuário individuais**: as contas de usuário podem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="73efe-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="73efe-115">Dentro do aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="73efe-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="73efe-116">Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="73efe-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="73efe-117">**Contas corporativas ou de estudante**</span><span class="sxs-lookup"><span data-stu-id="73efe-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="73efe-118">**Autenticação do Windows**</span><span class="sxs-lookup"><span data-stu-id="73efe-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="73efe-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73efe-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="73efe-120">Siga as diretrizes de Visual Studio Code no <xref:blazor/tooling> para criar um novo Blazor Server projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="73efe-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="73efe-121">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="73efe-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="73efe-122">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="73efe-122">Authentication mechanism</span></span> | <span data-ttu-id="73efe-123">Descrição</span><span class="sxs-lookup"><span data-stu-id="73efe-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="73efe-124">`None` (padrão)</span><span class="sxs-lookup"><span data-stu-id="73efe-124">`None` (default)</span></span>         | <span data-ttu-id="73efe-125">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="73efe-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="73efe-126">Usuários armazenados no aplicativo com ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="73efe-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="73efe-127">Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="73efe-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="73efe-128">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="73efe-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="73efe-129">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="73efe-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="73efe-130">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="73efe-130">Windows Authentication</span></span> |

<span data-ttu-id="73efe-131">Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:</span><span class="sxs-lookup"><span data-stu-id="73efe-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="73efe-132">Crie uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="73efe-132">Create a folder for the project.</span></span>
* <span data-ttu-id="73efe-133">Nomeie o projeto.</span><span class="sxs-lookup"><span data-stu-id="73efe-133">Name the project.</span></span>

<span data-ttu-id="73efe-134">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="73efe-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="73efe-135">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="73efe-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="73efe-136">Siga as diretrizes de Visual Studio para Mac no <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="73efe-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="73efe-137">Na etapa **configurar seu novo Blazor Server aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="73efe-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="73efe-138">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="73efe-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="73efe-139">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="73efe-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="73efe-140">Crie um novo Blazor Server projeto com um mecanismo de autenticação usando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="73efe-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="73efe-141">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="73efe-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="73efe-142">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="73efe-142">Authentication mechanism</span></span> | <span data-ttu-id="73efe-143">Descrição</span><span class="sxs-lookup"><span data-stu-id="73efe-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="73efe-144">`None` (padrão)</span><span class="sxs-lookup"><span data-stu-id="73efe-144">`None` (default)</span></span>         | <span data-ttu-id="73efe-145">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="73efe-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="73efe-146">Usuários armazenados no aplicativo com ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="73efe-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="73efe-147">Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="73efe-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="73efe-148">Autenticação organizacional para um único locatário</span><span class="sxs-lookup"><span data-stu-id="73efe-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="73efe-149">Autenticação organizacional para vários locatários</span><span class="sxs-lookup"><span data-stu-id="73efe-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="73efe-150">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="73efe-150">Windows Authentication</span></span> |

<span data-ttu-id="73efe-151">Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:</span><span class="sxs-lookup"><span data-stu-id="73efe-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="73efe-152">Crie uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="73efe-152">Create a folder for the project.</span></span>
* <span data-ttu-id="73efe-153">Nomeie o projeto.</span><span class="sxs-lookup"><span data-stu-id="73efe-153">Name the project.</span></span>

<span data-ttu-id="73efe-154">Para mais informações:</span><span class="sxs-lookup"><span data-stu-id="73efe-154">For more information:</span></span>

* <span data-ttu-id="73efe-155">Consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="73efe-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="73efe-156">Execute o comando de ajuda para o Blazor Server modelo ( `blazorserver` ) em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="73efe-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="73efe-157">Scaffold Identity</span><span class="sxs-lookup"><span data-stu-id="73efe-157">Scaffold Identity</span></span>

<span data-ttu-id="73efe-158">Scaffold Identity em um Blazor Server projeto:</span><span class="sxs-lookup"><span data-stu-id="73efe-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="73efe-159">[Sem autorização existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="73efe-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="73efe-160">[Com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="73efe-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="73efe-161">Serviço de Azure App no Linux com o Identity servidor</span><span class="sxs-lookup"><span data-stu-id="73efe-161">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="73efe-162">Especifique o emissor explicitamente ao implantar no serviço de Azure App no Linux com o Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="73efe-162">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="73efe-163">Para obter mais informações, consulte <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="73efe-163">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73efe-164">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="73efe-164">Additional resources</span></span>

* [<span data-ttu-id="73efe-165">Início Rápido: Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73efe-165">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="73efe-166">Início Rápido: Proteger uma API Web ASP.NET Core com a plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="73efe-166">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="73efe-167"><xref:host-and-deploy/proxy-load-balancer>: Inclui diretrizes sobre:</span><span class="sxs-lookup"><span data-stu-id="73efe-167"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="73efe-168">Usando o middleware de cabeçalhos encaminhados para preservar as informações do esquema HTTPS entre servidores proxy e redes internas.</span><span class="sxs-lookup"><span data-stu-id="73efe-168">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="73efe-169">Cenários adicionais e casos de uso, incluindo configuração de esquema manual, alterações de caminho de solicitação para roteamento de solicitação correto e encaminhamento do esquema de solicitação para proxies inversos do Linux e não do IIS.</span><span class="sxs-lookup"><span data-stu-id="73efe-169">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
