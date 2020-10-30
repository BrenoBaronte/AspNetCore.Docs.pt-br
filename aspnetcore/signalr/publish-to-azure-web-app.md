---
title: 'Publicar um :::no-loc(SignalR)::: aplicativo de ASP.NET Core no serviço Azure app'
author: bradygaster
description: 'Saiba como publicar um :::no-loc(SignalR)::: aplicativo de ASP.NET Core no serviço Azure app.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: e00eea81788c9b335691b7e5ffe6a46534c3c492
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058214"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="4640d-103">Publicar um :::no-loc(SignalR)::: aplicativo de ASP.NET Core no serviço Azure app</span><span class="sxs-lookup"><span data-stu-id="4640d-103">Publish an ASP.NET Core :::no-loc(SignalR)::: app to Azure App Service</span></span>

<span data-ttu-id="4640d-104">Por [Brady GASTER](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="4640d-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="4640d-105">O [serviço de Azure app](/azure/app-service/app-service-web-overview) é um serviço de plataforma de computação em nuvem da [Microsoft](https://azure.microsoft.com/) para hospedar aplicativos Web, incluindo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4640d-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="4640d-106">Este artigo refere-se à publicação de um :::no-loc(SignalR)::: aplicativo ASP.NET Core do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4640d-106">This article refers to publishing an ASP.NET Core :::no-loc(SignalR)::: app from Visual Studio.</span></span> <span data-ttu-id="4640d-107">Para obter mais informações, consulte [ :::no-loc(SignalR)::: Service for Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="4640d-107">For more information, see [:::no-loc(SignalR)::: service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="4640d-108">Publicar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="4640d-108">Publish the app</span></span>

<span data-ttu-id="4640d-109">Este artigo aborda a publicação usando as ferramentas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4640d-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="4640d-110">Visual Studio Code usuários podem usar [CLI do Azure](/cli/azure) comandos para publicar aplicativos no Azure.</span><span class="sxs-lookup"><span data-stu-id="4640d-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="4640d-111">Para obter mais informações, consulte [publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="4640d-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="4640d-112">Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções** , e selecione **Publicar** .</span><span class="sxs-lookup"><span data-stu-id="4640d-112">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>

1. <span data-ttu-id="4640d-113">Confirme se o **serviço de aplicativo** e **criar novo** estão selecionados na caixa de diálogo **escolher um destino de publicação** .</span><span class="sxs-lookup"><span data-stu-id="4640d-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="4640d-114">Selecione **Criar perfil** na lista suspensa do botão **publicar** .</span><span class="sxs-lookup"><span data-stu-id="4640d-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="4640d-115">Insira as informações descritas na tabela a seguir na caixa de diálogo **Criar serviço de aplicativo** e selecione **criar** .</span><span class="sxs-lookup"><span data-stu-id="4640d-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create** .</span></span>

   | <span data-ttu-id="4640d-116">Item</span><span class="sxs-lookup"><span data-stu-id="4640d-116">Item</span></span>               | <span data-ttu-id="4640d-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="4640d-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="4640d-118">**Nome**</span><span class="sxs-lookup"><span data-stu-id="4640d-118">**Name**</span></span>           | <span data-ttu-id="4640d-119">Nome exclusivo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4640d-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="4640d-120">**Assinatura**</span><span class="sxs-lookup"><span data-stu-id="4640d-120">**Subscription**</span></span>   | <span data-ttu-id="4640d-121">Assinatura do Azure que o aplicativo usa.</span><span class="sxs-lookup"><span data-stu-id="4640d-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="4640d-122">**Grupo de Recursos**</span><span class="sxs-lookup"><span data-stu-id="4640d-122">**Resource Group**</span></span> | <span data-ttu-id="4640d-123">Grupo de recursos relacionados aos quais o aplicativo pertence.</span><span class="sxs-lookup"><span data-stu-id="4640d-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="4640d-124">**Plano de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="4640d-124">**Hosting Plan**</span></span>   | <span data-ttu-id="4640d-125">Plano de preços para o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="4640d-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="4640d-126">Selecione o **:::no-loc(SignalR)::: serviço do Azure** na lista suspensa **dependências**  >  **Adicionar** :</span><span class="sxs-lookup"><span data-stu-id="4640d-126">Select the **Azure :::no-loc(SignalR)::: Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Área de dependências mostrando a seleção de Azure::: no-Loc (Signalr)::: Service na lista suspensa adicionar](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="4640d-128">Na caixa de diálogo **:::no-loc(SignalR)::: serviço do Azure** , selecione **criar uma nova :::no-loc(SignalR)::: instância de serviço do Azure** .</span><span class="sxs-lookup"><span data-stu-id="4640d-128">In the **Azure :::no-loc(SignalR)::: Service** dialog, select **Create a new Azure :::no-loc(SignalR)::: Service instance** .</span></span>

1. <span data-ttu-id="4640d-129">Forneça um **nome** , um **grupo de recursos** e um **local** .</span><span class="sxs-lookup"><span data-stu-id="4640d-129">Provide a **Name** , **Resource Group** , and **Location** .</span></span> <span data-ttu-id="4640d-130">Volte para a caixa de diálogo **:::no-loc(SignalR)::: serviço do Azure** e selecione **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="4640d-130">Return to the **Azure :::no-loc(SignalR)::: Service** dialog and select **Add** .</span></span>

<span data-ttu-id="4640d-131">O Visual Studio conclui as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="4640d-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="4640d-132">Cria um perfil de publicação que contém as configurações de publicação.</span><span class="sxs-lookup"><span data-stu-id="4640d-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="4640d-133">Cria um *aplicativo Web do Azure* com os detalhes fornecidos.</span><span class="sxs-lookup"><span data-stu-id="4640d-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="4640d-134">Publica o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4640d-134">Publishes the app.</span></span>
* <span data-ttu-id="4640d-135">Inicia um navegador, que carrega o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="4640d-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="4640d-136">O formato da URL do aplicativo é `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="4640d-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="4640d-137">Por exemplo, um aplicativo chamado `:::no-loc(SignalR):::ChatApp` tem uma URL de `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="4640d-137">For example, an app named `:::no-loc(SignalR):::ChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="4640d-138">Se ocorrer um erro de *Gateway insatisfatório HTTP 502,2* ao implantar um aplicativo destinado a uma versão prévia do .NET Core, consulte [implantar ASP.NET Core versão de visualização para Azure app serviço](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) para resolvê-lo.</span><span class="sxs-lookup"><span data-stu-id="4640d-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="4640d-139">Configurar o aplicativo no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="4640d-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="4640d-140">*Esta seção se aplica somente a aplicativos que não usam o serviço do Azure :::no-loc(SignalR)::: .*</span><span class="sxs-lookup"><span data-stu-id="4640d-140">*This section only applies to apps not using the Azure :::no-loc(SignalR)::: Service.*</span></span>
>
> <span data-ttu-id="4640d-141">Se o aplicativo usar o serviço do Azure :::no-loc(SignalR)::: , o serviço de aplicativo não exigirá a configuração de afinidade de Application Request Routing (ARR) e os soquetes da Web descritos nesta seção.</span><span class="sxs-lookup"><span data-stu-id="4640d-141">If the app uses the Azure :::no-loc(SignalR)::: Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="4640d-142">Os clientes conectam seus soquetes Web ao serviço do Azure :::no-loc(SignalR)::: , não diretamente ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4640d-142">Clients connect their Web Sockets to the Azure :::no-loc(SignalR)::: Service, not directly to the app.</span></span>

<span data-ttu-id="4640d-143">Para aplicativos hospedados sem o :::no-loc(SignalR)::: serviço do Azure, habilite:</span><span class="sxs-lookup"><span data-stu-id="4640d-143">For apps hosted without the Azure :::no-loc(SignalR)::: Service, enable:</span></span>

* <span data-ttu-id="4640d-144">[Afinidade ARR] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- :::no-loc(cookie)::: -(Arr- :::no-loc(cookie)::: ) -for-Azure-web-apps.html) para rotear solicitações de um usuário de volta para a mesma instância do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4640d-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-:::no-loc(cookie):::-(ARR-:::no-loc(cookie):::)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="4640d-145">A configuração padrão é **on** .</span><span class="sxs-lookup"><span data-stu-id="4640d-145">The default setting is **On** .</span></span>
* <span data-ttu-id="4640d-146">[Web Sockets](xref:fundamentals/websockets) para permitir que o transporte de soquetes da Web funcione.</span><span class="sxs-lookup"><span data-stu-id="4640d-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="4640d-147">A configuração padrão é **off** .</span><span class="sxs-lookup"><span data-stu-id="4640d-147">The default setting is **Off** .</span></span>

1. <span data-ttu-id="4640d-148">Na portal do Azure, navegue até o aplicativo Web nos **serviços de aplicativos** .</span><span class="sxs-lookup"><span data-stu-id="4640d-148">In the Azure portal, navigate to the web app in **App Services** .</span></span>
1. <span data-ttu-id="4640d-149">Abra **Configuration**  >  **configurações gerais** de configuração.</span><span class="sxs-lookup"><span data-stu-id="4640d-149">Open **Configuration** > **General settings** .</span></span>
1. <span data-ttu-id="4640d-150">Defina os **soquetes da Web** como **ativado** .</span><span class="sxs-lookup"><span data-stu-id="4640d-150">Set **Web sockets** to **On** .</span></span>
1. <span data-ttu-id="4640d-151">Verifique se a **afinidade arr** está definida como **on** .</span><span class="sxs-lookup"><span data-stu-id="4640d-151">Verify that **ARR affinity** is set to **On** .</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="4640d-152">Limites do plano do serviço de aplicativo</span><span class="sxs-lookup"><span data-stu-id="4640d-152">App Service Plan limits</span></span>

<span data-ttu-id="4640d-153">Os soquetes da Web e outros transportes são limitados com base no plano do serviço de aplicativo selecionado.</span><span class="sxs-lookup"><span data-stu-id="4640d-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="4640d-154">Para obter mais informações, consulte as seções *limites de serviços de nuvem do Azure* e limites de serviço de *aplicativo* do artigo [assinatura e limites de serviço, cotas e restrições do Azure](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="4640d-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4640d-155">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4640d-155">Additional resources</span></span>

* [<span data-ttu-id="4640d-156">O que é o serviço do Azure :::no-loc(SignalR)::: ?</span><span class="sxs-lookup"><span data-stu-id="4640d-156">What is Azure :::no-loc(SignalR)::: Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="4640d-157">Publicar um aplicativo ASP.NET Core no Azure com as ferramentas de linha de comando</span><span class="sxs-lookup"><span data-stu-id="4640d-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="4640d-158">Hospedar e implantar ASP.NET Core aplicativos de visualização no Azure</span><span class="sxs-lookup"><span data-stu-id="4640d-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
