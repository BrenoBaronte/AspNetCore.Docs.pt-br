---
title: Autenticação de nuvem com Azure Active Directory B2C no ASP.NET Core
author: camsoper
description: Descubra como configurar a autenticação do Azure Active Directory B2C com o ASP.NET Core.
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061386"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="208da-103">Autenticação de nuvem com Azure Active Directory B2C no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="208da-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="208da-104">Por [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="208da-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="208da-105">O [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure ad B2C) é uma solução de gerenciamento de identidade de nuvem para aplicativos Web e móveis.</span><span class="sxs-lookup"><span data-stu-id="208da-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="208da-106">O serviço fornece autenticação para aplicativos hospedados na nuvem e no local.</span><span class="sxs-lookup"><span data-stu-id="208da-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="208da-107">Os tipos de autenticação incluem contas individuais, contas de rede social e contas corporativas federadas.</span><span class="sxs-lookup"><span data-stu-id="208da-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="208da-108">Além disso, Azure AD B2C pode fornecer autenticação multifator com configuração mínima.</span><span class="sxs-lookup"><span data-stu-id="208da-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="208da-109">Azure Active Directory (Azure AD) e Azure AD B2C são ofertas de produtos separadas.</span><span class="sxs-lookup"><span data-stu-id="208da-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="208da-110">Um locatário do Azure AD representa uma organização, enquanto um locatário de Azure AD B2C representa uma coleção de identidades a ser usada com aplicativos de terceira parte confiável.</span><span class="sxs-lookup"><span data-stu-id="208da-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="208da-111">Para saber mais, consulte [Azure ad B2C: perguntas frequentes (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="208da-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="208da-112">Neste tutorial, você aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="208da-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="208da-113">Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="208da-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="208da-114">Registrar um aplicativo no Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="208da-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="208da-115">Use o Visual Studio para criar um aplicativo Web ASP.NET Core configurado para usar o locatário Azure AD B2C para autenticação</span><span class="sxs-lookup"><span data-stu-id="208da-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="208da-116">Configurar políticas que controlam o comportamento do locatário do Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="208da-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="208da-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="208da-117">Prerequisites</span></span>

<span data-ttu-id="208da-118">Os itens a seguir são necessários para este passo a passos:</span><span class="sxs-lookup"><span data-stu-id="208da-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="208da-119">Assinatura Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="208da-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="208da-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="208da-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="208da-121">Criar o locatário Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="208da-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="208da-122">Crie um locatário Azure Active Directory B2C [conforme descrito na documentação](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="208da-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="208da-123">Quando solicitado, associar o locatário a uma assinatura do Azure é opcional para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="208da-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="208da-124">Registrar o aplicativo no Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="208da-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="208da-125">No locatário Azure AD B2C recém-criado, Registre seu aplicativo usando [as etapas na documentação na](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) seção **registrar um aplicativo Web** .</span><span class="sxs-lookup"><span data-stu-id="208da-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="208da-126">Pare na seção **criar um segredo do cliente de aplicativo Web** .</span><span class="sxs-lookup"><span data-stu-id="208da-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="208da-127">Um segredo do cliente não é necessário para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="208da-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="208da-128">Use os seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="208da-128">Use the following values:</span></span>

| <span data-ttu-id="208da-129">Configuração</span><span class="sxs-lookup"><span data-stu-id="208da-129">Setting</span></span>                       | <span data-ttu-id="208da-130">Valor</span><span class="sxs-lookup"><span data-stu-id="208da-130">Value</span></span>                     | <span data-ttu-id="208da-131">Observações</span><span class="sxs-lookup"><span data-stu-id="208da-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="208da-132">**Nome**</span><span class="sxs-lookup"><span data-stu-id="208da-132">**Name**</span></span>                      | <span data-ttu-id="208da-133">*&lt;nome do aplicativo&gt;*</span><span class="sxs-lookup"><span data-stu-id="208da-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="208da-134">Insira um **nome** para o aplicativo que descreve seu aplicativo para os consumidores.</span><span class="sxs-lookup"><span data-stu-id="208da-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="208da-135">**Incluir aplicativo Web/API Web**</span><span class="sxs-lookup"><span data-stu-id="208da-135">**Include web app / web API**</span></span> | <span data-ttu-id="208da-136">Yes</span><span class="sxs-lookup"><span data-stu-id="208da-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="208da-137">**Permitir fluxo implícito**</span><span class="sxs-lookup"><span data-stu-id="208da-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="208da-138">Yes</span><span class="sxs-lookup"><span data-stu-id="208da-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="208da-139">**URL de Resposta**</span><span class="sxs-lookup"><span data-stu-id="208da-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="208da-140">As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retornará os tokens que o aplicativo solicitar.</span><span class="sxs-lookup"><span data-stu-id="208da-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="208da-141">O Visual Studio fornece a URL de resposta a ser usada.</span><span class="sxs-lookup"><span data-stu-id="208da-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="208da-142">Por enquanto, insira `https://localhost:44300/signin-oidc` para preencher o formulário.</span><span class="sxs-lookup"><span data-stu-id="208da-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="208da-143">**URI da ID do aplicativo**</span><span class="sxs-lookup"><span data-stu-id="208da-143">**App ID URI**</span></span>                | <span data-ttu-id="208da-144">Deixar em branco</span><span class="sxs-lookup"><span data-stu-id="208da-144">Leave blank</span></span>               | <span data-ttu-id="208da-145">Não é necessário para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="208da-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="208da-146">**Incluir cliente nativo**</span><span class="sxs-lookup"><span data-stu-id="208da-146">**Include native client**</span></span>     | <span data-ttu-id="208da-147">No</span><span class="sxs-lookup"><span data-stu-id="208da-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="208da-148">Se estiver configurando uma URL de resposta não localhost, esteja ciente das [restrições sobre o que é permitido na lista URL de resposta](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span><span class="sxs-lookup"><span data-stu-id="208da-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="208da-149">Depois que o aplicativo é registrado, a lista de aplicativos no locatário é exibida.</span><span class="sxs-lookup"><span data-stu-id="208da-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="208da-150">Selecione o aplicativo que acabou de ser registrado.</span><span class="sxs-lookup"><span data-stu-id="208da-150">Select the app that was just registered.</span></span> <span data-ttu-id="208da-151">Selecione o ícone de **cópia** à direita do campo **ID do aplicativo** para copiá-lo para a área de transferência.</span><span class="sxs-lookup"><span data-stu-id="208da-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="208da-152">Nada mais pode ser configurado no locatário Azure AD B2C neste momento, mas deixe a janela do navegador aberta.</span><span class="sxs-lookup"><span data-stu-id="208da-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="208da-153">Há mais configuração após a criação do aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="208da-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="208da-154">Criar um aplicativo ASP.NET Core no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="208da-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="208da-155">O modelo de aplicativo Web do Visual Studio pode ser configurado para usar o locatário Azure AD B2C para autenticação.</span><span class="sxs-lookup"><span data-stu-id="208da-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="208da-156">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="208da-156">In Visual Studio:</span></span>

1. <span data-ttu-id="208da-157">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="208da-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="208da-158">Selecione **aplicativo Web** na lista de modelos.</span><span class="sxs-lookup"><span data-stu-id="208da-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="208da-159">Selecione o botão **alterar autenticação** .</span><span class="sxs-lookup"><span data-stu-id="208da-159">Select the **Change Authentication** button.</span></span>
    
    ![Botão Alterar autenticação](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="208da-161">Na caixa de diálogo **alterar autenticação** , selecione **contas de usuário individuais** e, em seguida, selecione **conectar a um repositório de usuários existente na nuvem** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="208da-161">In the **Change Authentication** dialog, select **Individual User Accounts** , and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Caixa de diálogo Alterar autenticação](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="208da-163">Preencha o formulário com os seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="208da-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="208da-164">Configuração</span><span class="sxs-lookup"><span data-stu-id="208da-164">Setting</span></span>                       | <span data-ttu-id="208da-165">Valor</span><span class="sxs-lookup"><span data-stu-id="208da-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="208da-166">**Nome de domínio**</span><span class="sxs-lookup"><span data-stu-id="208da-166">**Domain Name**</span></span>               | <span data-ttu-id="208da-167">*&lt;o nome de domínio do seu locatário B2C&gt;*</span><span class="sxs-lookup"><span data-stu-id="208da-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="208da-168">**ID do Aplicativo**</span><span class="sxs-lookup"><span data-stu-id="208da-168">**Application ID**</span></span>            | <span data-ttu-id="208da-169">*&lt;colar a ID do aplicativo da área de transferência&gt;*</span><span class="sxs-lookup"><span data-stu-id="208da-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="208da-170">**Caminho de retorno de chamada**</span><span class="sxs-lookup"><span data-stu-id="208da-170">**Callback Path**</span></span>             | <span data-ttu-id="208da-171">*&lt;usar o valor padrão&gt;*</span><span class="sxs-lookup"><span data-stu-id="208da-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="208da-172">**Política de inscrição ou entrada**</span><span class="sxs-lookup"><span data-stu-id="208da-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="208da-173">**Redefinir política de senha**</span><span class="sxs-lookup"><span data-stu-id="208da-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="208da-174">**Editar política de perfil**</span><span class="sxs-lookup"><span data-stu-id="208da-174">**Edit profile policy**</span></span>       | <span data-ttu-id="208da-175">*&lt;deixar em branco&gt;*</span><span class="sxs-lookup"><span data-stu-id="208da-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="208da-176">Selecione o link **copiar** ao lado de **URI de resposta** para copiar o URI de resposta para a área de transferência.</span><span class="sxs-lookup"><span data-stu-id="208da-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="208da-177">Selecione **OK** para fechar a caixa de diálogo **alterar autenticação** .</span><span class="sxs-lookup"><span data-stu-id="208da-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="208da-178">Selecione **OK** para criar o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="208da-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="208da-179">Concluir o registro do aplicativo B2C</span><span class="sxs-lookup"><span data-stu-id="208da-179">Finish the B2C app registration</span></span>

<span data-ttu-id="208da-180">Retorne à janela do navegador com as propriedades do aplicativo B2C ainda abertas.</span><span class="sxs-lookup"><span data-stu-id="208da-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="208da-181">Altere a **URL de resposta** temporária especificada anteriormente para o valor copiado do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="208da-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="208da-182">Selecione **salvar** na parte superior da janela.</span><span class="sxs-lookup"><span data-stu-id="208da-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="208da-183">Se você não tiver copiado a URL de resposta, use o endereço HTTPS da guia Depurar nas propriedades do projeto Web e acrescente o valor **CallbackPath** de *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="208da-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json* .</span></span>

## <a name="configure-policies"></a><span data-ttu-id="208da-184">Configurar políticas</span><span class="sxs-lookup"><span data-stu-id="208da-184">Configure policies</span></span>

<span data-ttu-id="208da-185">Use as etapas na documentação do Azure AD B2C para [criar uma política de inscrição ou de entrada](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)e, em seguida, [crie uma política de redefinição de senha](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="208da-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="208da-186">Use os valores de exemplo fornecidos na documentação para **Identity provedores** , **atributos de inscrição** e declarações de **aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="208da-186">Use the example values provided in the documentation for **Identity providers** , **Sign-up attributes** , and **Application claims** .</span></span> <span data-ttu-id="208da-187">Usar o botão **executar agora** para testar as políticas, conforme descrito na documentação é opcional.</span><span class="sxs-lookup"><span data-stu-id="208da-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="208da-188">Verifique se os nomes de política são exatamente conforme descrito na documentação, pois essas políticas foram usadas na caixa de diálogo **alterar autenticação** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="208da-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="208da-189">Os nomes de política podem ser verificados no *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="208da-189">The policy names can be verified in *appsettings.json* .</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="208da-190">Configurar as opções/JwtBearer/OpenIdConnectOptions subjacentes Cookie</span><span class="sxs-lookup"><span data-stu-id="208da-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="208da-191">Para configurar as opções subjacentes diretamente, use a constante de esquema apropriada no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="208da-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="208da-192">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="208da-192">Run the app</span></span>

<span data-ttu-id="208da-193">No Visual Studio, pressione **F5** para compilar e executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="208da-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="208da-194">Depois que o aplicativo Web for iniciado, selecione **aceitar** para aceitar o uso de cookie s (se solicitado) e, em seguida, selecione **entrar** .</span><span class="sxs-lookup"><span data-stu-id="208da-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in** .</span></span>

![Entrar no aplicativo](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="208da-196">O navegador redireciona para o locatário do Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="208da-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="208da-197">Entre com uma conta existente (se uma tiver sido criada testando as políticas) ou selecione **inscrever-se agora** para criar uma nova conta.</span><span class="sxs-lookup"><span data-stu-id="208da-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="208da-198">O link **esqueceu sua senha?** é usado para redefinir uma senha esquecida.</span><span class="sxs-lookup"><span data-stu-id="208da-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Logon do Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="208da-200">Depois de entrar com êxito, o navegador redireciona para o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="208da-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Êxito](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="208da-202">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="208da-202">Next steps</span></span>

<span data-ttu-id="208da-203">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="208da-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="208da-204">Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="208da-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="208da-205">Registrar um aplicativo no Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="208da-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="208da-206">Use o Visual Studio para criar um aplicativo Web ASP.NET Core configurado para usar o locatário Azure AD B2C para autenticação</span><span class="sxs-lookup"><span data-stu-id="208da-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="208da-207">Configurar políticas que controlam o comportamento do locatário do Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="208da-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="208da-208">Agora que o aplicativo ASP.NET Core está configurado para usar Azure AD B2C para autenticação, o [atributo autorizar](xref:security/authorization/simple) pode ser usado para proteger seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="208da-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="208da-209">Continue desenvolvendo seu aplicativo aprendendo a:</span><span class="sxs-lookup"><span data-stu-id="208da-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="208da-210">[Personalizar a interface do usuário do Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="208da-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="208da-211">[Configurar requisitos de complexidade de senha](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="208da-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="208da-212">[Habilite a autenticação multifator](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="208da-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="208da-213">Configure provedores de identidade adicionais, como [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e outros.</span><span class="sxs-lookup"><span data-stu-id="208da-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="208da-214">[Use o API do Graph do Azure ad](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) para recuperar informações adicionais do usuário, como associação de grupo, do locatário Azure ad B2C.</span><span class="sxs-lookup"><span data-stu-id="208da-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="208da-215">[Como proteger uma API Web criada com ASP.NET Core usando o Azure ad B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span><span class="sxs-lookup"><span data-stu-id="208da-215">[How to secure a Web API built with ASP.NET Core using the Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span></span>
* <span data-ttu-id="208da-216">[Tutorial: conceder acesso a uma API Web do ASP.NET usando Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="208da-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
