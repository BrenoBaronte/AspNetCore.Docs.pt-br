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
ms.openlocfilehash: dac2203d63b2d924ee6ae4f7012e9c33739e6213
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97792074"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="be6a8-103">Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="be6a8-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="be6a8-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="be6a8-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="be6a8-105">Este artigo descreve como criar um [ Blazor WebAssembly aplicativo hospedado](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="be6a8-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="be6a8-106">Registrar aplicativos em AAD B2C e criar solução</span><span class="sxs-lookup"><span data-stu-id="be6a8-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="be6a8-107">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="be6a8-107">Create a tenant</span></span>

<span data-ttu-id="be6a8-108">Siga as orientações em [tutorial: criar um locatário de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para criar um locatário de AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="be6a8-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="be6a8-109">Retorne a este artigo imediatamente após criar ou identificar um locatário a ser usado.</span><span class="sxs-lookup"><span data-stu-id="be6a8-109">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="be6a8-110">Registre a instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/` , que inclui a barra à direita).</span><span class="sxs-lookup"><span data-stu-id="be6a8-110">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="be6a8-111">A instância é o esquema e o host de um registro de aplicativo B2C do Azure, que pode ser encontrado abrindo a janela **pontos de extremidade** na página **registros de aplicativo** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="be6a8-111">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="be6a8-112">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="be6a8-112">Register a server API app</span></span>

<span data-ttu-id="be6a8-113">Registrar um aplicativo de AAD B2C para o *aplicativo de API do servidor*:</span><span class="sxs-lookup"><span data-stu-id="be6a8-113">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="be6a8-114">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="be6a8-115">Forneça um **nome** para o aplicativo (por exemplo, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="be6a8-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="be6a8-116">Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)**</span><span class="sxs-lookup"><span data-stu-id="be6a8-116">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="be6a8-117">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="be6a8-117">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="be6a8-118">Confirme se **as permissões**  >  **concedem consentimento de administrador ao OpenID e offline_access permissões** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="be6a8-118">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="be6a8-119">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-119">Select **Register**.</span></span>

<span data-ttu-id="be6a8-120">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="be6a8-120">Record the following information:</span></span>

* <span data-ttu-id="be6a8-121">*Aplicativo de API do servidor* ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="be6a8-121">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="be6a8-122">Domínio principal/Publicador/locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.</span><span class="sxs-lookup"><span data-stu-id="be6a8-122">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="be6a8-123">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="be6a8-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="be6a8-124">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="be6a8-125">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="be6a8-126">Forneça um **nome de escopo** (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="be6a8-127">Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="be6a8-128">Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="be6a8-129">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="be6a8-130">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-130">Select **Add scope**.</span></span>

<span data-ttu-id="be6a8-131">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="be6a8-131">Record the following information:</span></span>

* <span data-ttu-id="be6a8-132">URI da ID do aplicativo (por exemplo,, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="be6a8-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="be6a8-133">Nome do escopo (por exemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="be6a8-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="be6a8-134">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="be6a8-134">Register a client app</span></span>

<span data-ttu-id="be6a8-135">Registrar um aplicativo de AAD B2C para o *aplicativo cliente*:</span><span class="sxs-lookup"><span data-stu-id="be6a8-135">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="be6a8-136">Em **Azure Active Directory** > **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="be6a8-137">Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD B2C cliente**).</span><span class="sxs-lookup"><span data-stu-id="be6a8-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="be6a8-138">Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)**</span><span class="sxs-lookup"><span data-stu-id="be6a8-138">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="be6a8-139">Defina a lista suspensa **URI de redirecionamento** para o **aplicativo de página única (Spa)** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="be6a8-139">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="be6a8-140">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="be6a8-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="be6a8-141">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be6a8-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="be6a8-142">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas *`Server`* Propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="be6a8-142">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="be6a8-143">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="be6a8-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="be6a8-144">Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="be6a8-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="be6a8-145">Confirme se **as permissões** > **concedem consentimento de administrador ao OpenID e offline_access permissões** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="be6a8-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="be6a8-146">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-146">Select **Register**.</span></span>

1. <span data-ttu-id="be6a8-147">Registre a ID do aplicativo (cliente) (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="be6a8-148">Em configurações de plataforma de **autenticação** >  > **, um aplicativo de página única (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="be6a8-148">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="be6a8-149">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="be6a8-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="be6a8-150">Para **concessão implícita**, verifique se as caixas de seleção para **tokens de acesso** e **tokens de ID** **não** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="be6a8-150">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="be6a8-151">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="be6a8-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="be6a8-152">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-152">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="be6a8-153">Em **Azure Active Directory** > **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-153">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="be6a8-154">Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD B2C cliente**).</span><span class="sxs-lookup"><span data-stu-id="be6a8-154">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="be6a8-155">Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)**</span><span class="sxs-lookup"><span data-stu-id="be6a8-155">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="be6a8-156">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="be6a8-156">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="be6a8-157">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="be6a8-157">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="be6a8-158">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be6a8-158">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="be6a8-159">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas *`Server`* Propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="be6a8-159">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="be6a8-160">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="be6a8-160">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="be6a8-161">Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="be6a8-161">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="be6a8-162">Confirme se **as permissões** > **concedem consentimento de administrador ao OpenID e offline_access permissões** estão selecionadas.</span><span class="sxs-lookup"><span data-stu-id="be6a8-162">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="be6a8-163">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-163">Select **Register**.</span></span>

<span data-ttu-id="be6a8-164">Registre a ID do aplicativo (cliente) (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-164">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="be6a8-165">Em  > **configurações da plataforma** de autenticação > **Web**:</span><span class="sxs-lookup"><span data-stu-id="be6a8-165">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="be6a8-166">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="be6a8-166">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="be6a8-167">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-167">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="be6a8-168">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="be6a8-168">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="be6a8-169">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-169">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="be6a8-170">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="be6a8-170">In **API permissions**:</span></span>

1. <span data-ttu-id="be6a8-171">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-171">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="be6a8-172">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="be6a8-172">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="be6a8-173">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="be6a8-173">Open the **API** list.</span></span>
1. <span data-ttu-id="be6a8-174">Habilite o acesso à API (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-174">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="be6a8-175">Escolha **Adicionar permissões**.</span><span class="sxs-lookup"><span data-stu-id="be6a8-175">Select **Add permissions**.</span></span>
1. <span data-ttu-id="be6a8-176">Selecione o botão **conceder consentimento de administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="be6a8-176">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="be6a8-177">Clique em **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="be6a8-177">Select **Yes** to confirm.</span></span>

<span data-ttu-id="be6a8-178">Em **casa**  >  **Azure ad B2C**  >  **fluxos de usuário**:</span><span class="sxs-lookup"><span data-stu-id="be6a8-178">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="be6a8-179">Criar um fluxo de usuário de inscrição e de entrada</span><span class="sxs-lookup"><span data-stu-id="be6a8-179">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="be6a8-180">No mínimo, selecione o atributo de usuário nome de exibição de **declarações do aplicativo**  >   para popular o `context.User.Identity.Name` no `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-180">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="be6a8-181">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-181">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="be6a8-182">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="be6a8-182">Create the app</span></span>

<span data-ttu-id="be6a8-183">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="be6a8-183">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="be6a8-184">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="be6a8-184">Placeholder</span></span>                   | <span data-ttu-id="be6a8-185">Nome do portal do Azure</span><span class="sxs-lookup"><span data-stu-id="be6a8-185">Azure portal name</span></span>                                     | <span data-ttu-id="be6a8-186">Exemplo</span><span class="sxs-lookup"><span data-stu-id="be6a8-186">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="be6a8-187">Instância</span><span class="sxs-lookup"><span data-stu-id="be6a8-187">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="be6a8-188">ID do aplicativo (cliente) para o *`Client`* aplicativo</span><span class="sxs-lookup"><span data-stu-id="be6a8-188">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="be6a8-189">Nome do escopo</span><span class="sxs-lookup"><span data-stu-id="be6a8-189">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="be6a8-190">ID do aplicativo (cliente) para o *aplicativo de API do servidor*</span><span class="sxs-lookup"><span data-stu-id="be6a8-190">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="be6a8-191">URI da ID de Aplicativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="be6a8-191">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="be6a8-192">Fluxo de usuário de inscrição/entrada</span><span class="sxs-lookup"><span data-stu-id="be6a8-192">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="be6a8-193">Domínio primário/Publicador/locatário</span><span class="sxs-lookup"><span data-stu-id="be6a8-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="be6a8-194">&dagger;O Blazor WebAssembly modelo adiciona automaticamente um esquema de `api://` para o argumento de URI da ID do aplicativo passado no `dotnet new` comando.</span><span class="sxs-lookup"><span data-stu-id="be6a8-194">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="be6a8-195">Ao fornecer o URI da ID do aplicativo para o `{SERVER API APP ID URI}` espaço reservado e se o esquema for `api://` , remova o esquema ( `api://` ) do argumento, como mostra o valor de exemplo na tabela anterior.</span><span class="sxs-lookup"><span data-stu-id="be6a8-195">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="be6a8-196">Se o URI da ID do aplicativo for um valor personalizado ou tiver algum outro esquema (por exemplo, `https://` para um domínio não confiável do editor semelhante a `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), você deverá atualizar manualmente o URI do escopo padrão e remover o `api://` esquema depois que o *`Client`* aplicativo for criado pelo modelo.</span><span class="sxs-lookup"><span data-stu-id="be6a8-196">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="be6a8-197">Para obter mais informações, consulte a observação na seção [escopos de token de acesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="be6a8-197">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="be6a8-198">O Blazor WebAssembly modelo pode ser alterado em uma versão futura do ASP.NET Core para resolver esses cenários.</span><span class="sxs-lookup"><span data-stu-id="be6a8-198">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="be6a8-199">Para obter mais informações, consulte [esquema duplo para URI de ID do aplicativo com Blazor modelo WASM (hospedado, organização única) (dotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="be6a8-199">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="be6a8-200">A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be6a8-200">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="be6a8-201">O escopo configurado pelo Blazor modelo hospedado pode ter o host de URI de ID de aplicativo repetido.</span><span class="sxs-lookup"><span data-stu-id="be6a8-201">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="be6a8-202">Confirme se o escopo configurado para a `DefaultAccessTokenScopes` coleção está correto no `Program.Main` ( `Program.cs` ) do *`Client`* aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be6a8-202">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="be6a8-203">No portal do Azure, o *`Client`* **URI de redirecionamento** de configuração de plataforma do aplicativo é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="be6a8-203">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="be6a8-204">Se o *`Client`* aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades *do aplicativo de API do servidor* no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="be6a8-204">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="be6a8-205">Se a porta não foi configurada anteriormente com a *`Client`* porta conhecida do aplicativo, retorne ao *`Client`* registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="be6a8-205">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="be6a8-206">*`Server`* configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="be6a8-206">*`Server`* app configuration</span></span>

<span data-ttu-id="be6a8-207">*Esta seção pertence ao aplicativo da solução **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="be6a8-207">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="be6a8-208">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="be6a8-208">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="be6a8-209">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web com a Identity plataforma Microsoft é fornecido pelos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="be6a8-209">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="be6a8-210">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="be6a8-210">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="be6a8-211">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) pacote:</span><span class="sxs-lookup"><span data-stu-id="be6a8-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="be6a8-212">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="be6a8-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="be6a8-213">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="be6a8-213">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="be6a8-214">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="be6a8-214">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="be6a8-215">O <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> método configura os serviços para proteger a API Web com a Identity plataforma Microsoft v 2.0.</span><span class="sxs-lookup"><span data-stu-id="be6a8-215">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="be6a8-216">Esse método espera uma `AzureAdB2C` seção na configuração do aplicativo com as configurações necessárias para inicializar as opções de autenticação.</span><span class="sxs-lookup"><span data-stu-id="be6a8-216">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="be6a8-217">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="be6a8-217">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="be6a8-218">O <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="be6a8-218">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="be6a8-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Verifique se:</span><span class="sxs-lookup"><span data-stu-id="be6a8-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="be6a8-220">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="be6a8-220">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="be6a8-221">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="be6a8-221">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="be6a8-222">Usuário. Identity . Nomes</span><span class="sxs-lookup"><span data-stu-id="be6a8-222">User.Identity.Name</span></span>

<span data-ttu-id="be6a8-223">Por padrão, o `User.Identity.Name` não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="be6a8-223">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="be6a8-224">Para configurar o aplicativo para receber o valor do `name` tipo de declaração:</span><span class="sxs-lookup"><span data-stu-id="be6a8-224">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="be6a8-225">Adicione um namespace para <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="be6a8-225">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="be6a8-226">Configure o <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="be6a8-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="be6a8-227">Configure o <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="be6a8-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="be6a8-228">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="be6a8-228">App settings</span></span>

<span data-ttu-id="be6a8-229">O `appsettings.json` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="be6a8-229">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="be6a8-230">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="be6a8-230">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="be6a8-231">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="be6a8-231">WeatherForecast controller</span></span>

<span data-ttu-id="be6a8-232">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="be6a8-232">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="be6a8-233">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="be6a8-233">It's **important** to understand that:</span></span>

* <span data-ttu-id="be6a8-234">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="be6a8-234">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="be6a8-235">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo usado no Blazor WebAssembly aplicativo serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="be6a8-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="be6a8-236">*`Client`* configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="be6a8-236">*`Client`* app configuration</span></span>

<span data-ttu-id="be6a8-237">*Esta seção pertence ao aplicativo da solução **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="be6a8-237">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="be6a8-238">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="be6a8-238">Authentication package</span></span>

<span data-ttu-id="be6a8-239">Quando um aplicativo é criado para usar uma conta individual do B2C ( `IndividualB2C` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-239">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="be6a8-240">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="be6a8-240">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="be6a8-241">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="be6a8-241">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="be6a8-242">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="be6a8-242">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="be6a8-243">O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be6a8-243">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="be6a8-244">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="be6a8-244">Authentication service support</span></span>

<span data-ttu-id="be6a8-245">O suporte para <xref:System.Net.Http.HttpClient> instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="be6a8-245">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="be6a8-246">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="be6a8-246">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="be6a8-247">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-247">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="be6a8-248">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote.</span><span class="sxs-lookup"><span data-stu-id="be6a8-248">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="be6a8-249">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="be6a8-249">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="be6a8-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="be6a8-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="be6a8-251">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be6a8-251">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="be6a8-252">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be6a8-252">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="be6a8-253">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="be6a8-253">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="be6a8-254">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="be6a8-254">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="be6a8-255">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="be6a8-255">Access token scopes</span></span>

<span data-ttu-id="be6a8-256">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="be6a8-256">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="be6a8-257">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="be6a8-257">Included by default in the sign in request.</span></span>
* <span data-ttu-id="be6a8-258">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="be6a8-258">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="be6a8-259">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="be6a8-259">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="be6a8-260">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="be6a8-260">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="be6a8-261">O Blazor WebAssembly modelo adiciona automaticamente um esquema de `api://` para o argumento de URI da ID do aplicativo passado no `dotnet new` comando.</span><span class="sxs-lookup"><span data-stu-id="be6a8-261">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="be6a8-262">Ao gerar um aplicativo a partir do Blazor modelo de projeto, confirme se o valor do escopo de token de acesso padrão usa o valor de URI de ID de aplicativo personalizado correto que você forneceu no portal do Azure ou um valor com **um** dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="be6a8-262">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="be6a8-263">Quando o domínio do Publicador do diretório é **confiável**, o escopo do token de acesso padrão normalmente é um valor semelhante ao exemplo a seguir, em que `API.Access` é o nome do escopo padrão:</span><span class="sxs-lookup"><span data-stu-id="be6a8-263">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="be6a8-264">Inspecione o valor de um esquema duplo ( `api://api://...` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-264">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="be6a8-265">Se um esquema duplo estiver presente, remova o primeiro `api://` esquema do valor.</span><span class="sxs-lookup"><span data-stu-id="be6a8-265">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="be6a8-266">Quando o domínio do Publicador do diretório não é **confiável**, o escopo do token de acesso padrão normalmente é um valor semelhante ao exemplo a seguir, em que `API.Access` é o nome do escopo padrão:</span><span class="sxs-lookup"><span data-stu-id="be6a8-266">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="be6a8-267">Inspecione o valor de um `api://` esquema extra ( `api://https://contoso.onmicrosoft.com/...` ).</span><span class="sxs-lookup"><span data-stu-id="be6a8-267">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="be6a8-268">Se um `api://` esquema extra estiver presente, remova o `api://` esquema do valor.</span><span class="sxs-lookup"><span data-stu-id="be6a8-268">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="be6a8-269">O Blazor WebAssembly modelo pode ser alterado em uma versão futura do ASP.NET Core para resolver esses cenários.</span><span class="sxs-lookup"><span data-stu-id="be6a8-269">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="be6a8-270">Para obter mais informações, consulte [esquema duplo para URI de ID do aplicativo com Blazor modelo WASM (hospedado, organização única) (dotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="be6a8-270">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="be6a8-271">Especifique escopos adicionais com `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="be6a8-271">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="be6a8-272">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="be6a8-272">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="be6a8-273">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="be6a8-273">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="be6a8-274">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="be6a8-274">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="be6a8-275">Modo de logon</span><span class="sxs-lookup"><span data-stu-id="be6a8-275">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="be6a8-276">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="be6a8-276">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="be6a8-277">Página de índice</span><span class="sxs-lookup"><span data-stu-id="be6a8-277">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="be6a8-278">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="be6a8-278">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="be6a8-279">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="be6a8-279">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="be6a8-280">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="be6a8-280">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="be6a8-281">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="be6a8-281">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="be6a8-282">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="be6a8-282">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="be6a8-283">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="be6a8-283">Run the app</span></span>

<span data-ttu-id="be6a8-284">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="be6a8-284">Run the app from the Server project.</span></span> <span data-ttu-id="be6a8-285">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="be6a8-285">When using Visual Studio, either:</span></span>

* <span data-ttu-id="be6a8-286">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="be6a8-286">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="be6a8-287">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="be6a8-287">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="be6a8-288">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="be6a8-288">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="be6a8-289">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="be6a8-289">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="be6a8-290">Tutorial: Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="be6a8-290">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="be6a8-291">Tutorial: Registrar um aplicativo no Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="be6a8-291">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="be6a8-292">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="be6a8-292">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
