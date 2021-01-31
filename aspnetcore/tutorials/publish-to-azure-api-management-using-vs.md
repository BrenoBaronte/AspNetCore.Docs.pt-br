---
title: Publicar uma API da Web do ASP.NET Core no gerenciamento de API do Azure com o Visual Studio
author: codemillmatt
description: Saiba como publicar uma API da Web do ASP.NET Core no gerenciamento de API do Azure usando o Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: ddff54bbd146c98cf83a865910401df26e7ac4ec
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217577"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="a6f49-103">Publicar uma API da Web do ASP.NET Core no gerenciamento de API do Azure com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6f49-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="a6f49-104">Por [Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="a6f49-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="a6f49-105">Configuração</span><span class="sxs-lookup"><span data-stu-id="a6f49-105">Set up</span></span>

- <span data-ttu-id="a6f49-106">Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma.</span><span class="sxs-lookup"><span data-stu-id="a6f49-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="a6f49-107">[Crie uma nova instância de gerenciamento de API do Azure](/azure/api-management/get-started-create-service-instance) , se ainda não tiver feito isso.</span><span class="sxs-lookup"><span data-stu-id="a6f49-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="a6f49-108">[Crie um projeto de aplicativo de API Web](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="a6f49-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="a6f49-109">Configurar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a6f49-109">Configure the app</span></span>

<span data-ttu-id="a6f49-110">Adicionar definições de Swagger à API Web do ASP.NET Core permite que o gerenciamento de API do Azure Leia as definições de API do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a6f49-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="a6f49-111">Conclua as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="a6f49-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="a6f49-112">Adicionar Swagger</span><span class="sxs-lookup"><span data-stu-id="a6f49-112">Add Swagger</span></span>

1. <span data-ttu-id="a6f49-113">Adicione o pacote NuGet [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) ao projeto de API Web ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a6f49-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Captura de tela para configurar a caixa de diálogo NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="a6f49-115">Adicione a seguinte linha ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a6f49-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="a6f49-116">Adicione a seguinte linha ao método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a6f49-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="a6f49-117">Alterar o roteamento de API</span><span class="sxs-lookup"><span data-stu-id="a6f49-117">Change the API routing</span></span>

<span data-ttu-id="a6f49-118">Em seguida, você alterará a estrutura de URL necessária para acessar a `Get` ação do `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="a6f49-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="a6f49-119">Conclua as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="a6f49-119">Complete the following steps:</span></span>

1. <span data-ttu-id="a6f49-120">Abra o arquivo *WeatherForecastController.cs* .</span><span class="sxs-lookup"><span data-stu-id="a6f49-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="a6f49-121">Exclua o `[Route("[controller]")]` atributo de nível de classe.</span><span class="sxs-lookup"><span data-stu-id="a6f49-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="a6f49-122">A definição de classe se parecerá com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="a6f49-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="a6f49-123">Adicione um `[Route("/")]` atributo à `Get()` ação.</span><span class="sxs-lookup"><span data-stu-id="a6f49-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="a6f49-124">A definição da função será parecida com a seguinte:</span><span class="sxs-lookup"><span data-stu-id="a6f49-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="a6f49-125">Publicar a API da Web para o serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="a6f49-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="a6f49-126">Conclua as etapas a seguir para publicar a API Web do ASP.NET Core no gerenciamento de API do Azure:</span><span class="sxs-lookup"><span data-stu-id="a6f49-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="a6f49-127">Publique o aplicativo de API para Azure App serviço.</span><span class="sxs-lookup"><span data-stu-id="a6f49-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="a6f49-128">Adicione uma API em branco à instância do serviço de gerenciamento de API do Azure.</span><span class="sxs-lookup"><span data-stu-id="a6f49-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="a6f49-129">Publique o aplicativo de API Web do ASP.NET Core na instância do serviço de gerenciamento de API do Azure.</span><span class="sxs-lookup"><span data-stu-id="a6f49-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="a6f49-130">Publicar o aplicativo de API no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="a6f49-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="a6f49-131">Conclua as etapas a seguir para publicar a API Web do ASP.NET Core no gerenciamento de API do Azure:</span><span class="sxs-lookup"><span data-stu-id="a6f49-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="a6f49-132">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **publicar**:</span><span class="sxs-lookup"><span data-stu-id="a6f49-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Menu contextual aberto com o link Publicar realçado](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="a6f49-134">Na caixa de diálogo **publicar** , selecione **Azure** e selecione o botão **Avançar** :</span><span class="sxs-lookup"><span data-stu-id="a6f49-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Caixa de diálogo Publicar](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="a6f49-136">Selecione **serviço de Azure app (Windows)** e selecione o botão **Avançar** :</span><span class="sxs-lookup"><span data-stu-id="a6f49-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Diálogo de publicação: selecione serviço de aplicativo](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="a6f49-138">Selecione **criar um novo serviço de Azure app**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-138">Select **Create a new Azure App Service**.</span></span>

    ![Diálogo de publicação: selecione instância de serviço do Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="a6f49-140">A caixa de diálogo **Criar Serviço de Aplicativo** é exibida.</span><span class="sxs-lookup"><span data-stu-id="a6f49-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="a6f49-141">Os campos de entrada **Nome do Aplicativo**, **Grupo de Recursos** e **Plano do Serviço de Aplicativo** serão populados.</span><span class="sxs-lookup"><span data-stu-id="a6f49-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="a6f49-142">Você pode manter esses nomes ou alterá-los.</span><span class="sxs-lookup"><span data-stu-id="a6f49-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="a6f49-143">Selecione o botão **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-143">Select the **Create** button.</span></span>

    ![Criar caixa de diálogo do Serviço de Aplicativo](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="a6f49-145">Após a conclusão da criação, a caixa de diálogo é fechada automaticamente e a caixa de diálogo **publicar** fica em foco novamente.</span><span class="sxs-lookup"><span data-stu-id="a6f49-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="a6f49-146">A instância que foi criada é selecionada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="a6f49-146">The instance that was created is automatically selected.</span></span>

![Diálogo de publicação: selecione instância do serviço de aplicativo](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="a6f49-148">Neste ponto, você precisa adicionar uma API ao serviço de gerenciamento de API do Azure.</span><span class="sxs-lookup"><span data-stu-id="a6f49-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="a6f49-149">Deixe o Visual Studio aberto enquanto você conclui as tarefas a seguir.</span><span class="sxs-lookup"><span data-stu-id="a6f49-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="a6f49-150">Adicionar uma API ao gerenciamento de API do Azure</span><span class="sxs-lookup"><span data-stu-id="a6f49-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="a6f49-151">Abra a instância do serviço de gerenciamento de API criada anteriormente no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="a6f49-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="a6f49-152">Selecione a folha **APIs** :</span><span class="sxs-lookup"><span data-stu-id="a6f49-152">Select the **APIs** blade:</span></span>

  ![Folha de APIs selecionada na instância do serviço de gerenciamento de API](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="a6f49-154">Selecione os 3 pontos ao lado de **API de eco** e, em seguida, selecione **excluir** no menu pop-up para removê-lo.</span><span class="sxs-lookup"><span data-stu-id="a6f49-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![Excluindo a API de eco da instância do serviço de gerenciamento de API](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="a6f49-156">No painel **Adicionar uma nova API** , selecione o bloco **API em branco** :</span><span class="sxs-lookup"><span data-stu-id="a6f49-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![Tela mostrando o bloco de API em branco realçado](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="a6f49-158">Insira os seguintes valores na caixa de diálogo **criar uma API em branco** que aparece:</span><span class="sxs-lookup"><span data-stu-id="a6f49-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="a6f49-159">**Nome de exibição**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="a6f49-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="a6f49-160">**Nome**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="a6f49-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="a6f49-161">**Sufixo de URL da API**: *v1*</span><span class="sxs-lookup"><span data-stu-id="a6f49-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="a6f49-162">Deixe o campo **URL do serviço Web** vazio.</span><span class="sxs-lookup"><span data-stu-id="a6f49-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="a6f49-163">Selecione o botão **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-163">Select the **Create** button.</span></span>

    ![Captura de tela da caixa de diálogo criar uma API em branco concluída](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="a6f49-165">A API em branco é criada.</span><span class="sxs-lookup"><span data-stu-id="a6f49-165">The blank API is created.</span></span>

![Captura de tela de uma API em branco no portal de gerenciamento de API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="a6f49-167">Publicar a API Web do ASP.NET Core no gerenciamento de API do Azure</span><span class="sxs-lookup"><span data-stu-id="a6f49-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="a6f49-168">Retorne ao Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a6f49-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="a6f49-169">A caixa de diálogo de **publicação** ainda deve estar aberta onde você parou antes.</span><span class="sxs-lookup"><span data-stu-id="a6f49-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="a6f49-170">Selecione o serviço de Azure App publicado recentemente para que ele seja realçado.</span><span class="sxs-lookup"><span data-stu-id="a6f49-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="a6f49-171">Selecione o botão **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-171">Select the **Next** button.</span></span>

    ![Captura de tela da caixa de diálogo Publicar com o serviço de aplicativo selecionado](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="a6f49-173">A caixa de diálogo agora mostra o serviço de gerenciamento de API do Azure criado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="a6f49-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="a6f49-174">Expanda-o e a pasta *APIs* para ver a API em branco que você criou.</span><span class="sxs-lookup"><span data-stu-id="a6f49-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="a6f49-175">Selecione o nome da API em branco e selecione o botão **concluir** .</span><span class="sxs-lookup"><span data-stu-id="a6f49-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![Captura de tela da API em branco do gerenciamento de API do Azure recém-criada selecionada na caixa de diálogo publicar](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="a6f49-177">A caixa de diálogo é fechada e uma tela de resumo é exibida com informações sobre a publicação.</span><span class="sxs-lookup"><span data-stu-id="a6f49-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="a6f49-178">Clique no botão **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-178">Select the **Publish** button.</span></span>

    ![Captura de tela do Visual Studio com o perfil de publicação exibido](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="a6f49-180">A API da Web será publicada no serviço de Azure App e no gerenciamento de API do Azure.</span><span class="sxs-lookup"><span data-stu-id="a6f49-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="a6f49-181">Uma nova janela do navegador será exibida e mostrará a API em execução no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="a6f49-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="a6f49-182">Você pode fechar essa janela.</span><span class="sxs-lookup"><span data-stu-id="a6f49-182">You can close that window.</span></span>

1. <span data-ttu-id="a6f49-183">Volte para a instância de gerenciamento de API do Azure no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="a6f49-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="a6f49-184">Atualize a janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="a6f49-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="a6f49-185">Selecione a API em branco que você criou nas etapas anteriores.</span><span class="sxs-lookup"><span data-stu-id="a6f49-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="a6f49-186">Agora ele está populado e você pode explorar.</span><span class="sxs-lookup"><span data-stu-id="a6f49-186">It's now populated and you can explore around.</span></span>

    ![Captura de tela da API preenchida no gerenciamento de API do Azure](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="a6f49-188">Configurar o nome da API publicada</span><span class="sxs-lookup"><span data-stu-id="a6f49-188">Configure the published API name</span></span>

<span data-ttu-id="a6f49-189">Observe que o nome da API é diferente do que você o nomeou.</span><span class="sxs-lookup"><span data-stu-id="a6f49-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="a6f49-190">A API publicada é denominada *WeatherAPI*; no entanto, você o nomeou como *WeatherForecasts* quando a criou.</span><span class="sxs-lookup"><span data-stu-id="a6f49-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="a6f49-191">Conclua as seguintes etapas para corrigir o nome:</span><span class="sxs-lookup"><span data-stu-id="a6f49-191">Complete the following steps to fix the name:</span></span>

![Captura de tela da API preenchida com nomes diferentes realçadas](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="a6f49-193">Exclua a seguinte linha do `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="a6f49-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="a6f49-194">Adicione o seguinte código ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a6f49-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "WeatherForecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="a6f49-195">Abra o perfil de publicação recém-criado.</span><span class="sxs-lookup"><span data-stu-id="a6f49-195">Open the newly created publish profile.</span></span> <span data-ttu-id="a6f49-196">Ele pode ser encontrado em **Gerenciador de soluções** na pasta *Properties/PublishProfiles* .</span><span class="sxs-lookup"><span data-stu-id="a6f49-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Captura de tela mostrando o local do arquivo de perfil de publicação realçado](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="a6f49-198">Altere o `<OpenAPIDocumentName>` valor do elemento de `v1` para `WeatherForecasts` .</span><span class="sxs-lookup"><span data-stu-id="a6f49-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![captura de tela das alterações necessárias para o perfil de publicação](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="a6f49-200">Republique a API Web do ASP.NET Core e abra a instância do gerenciamento de API do Azure no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="a6f49-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="a6f49-201">Atualize a página em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="a6f49-201">Refresh the page in your browser.</span></span> <span data-ttu-id="a6f49-202">Você verá o nome da API agora está correto.</span><span class="sxs-lookup"><span data-stu-id="a6f49-202">You'll see the name of the API is now correct.</span></span>

    ![Captura de tela da API concluída no portal](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="a6f49-204">Verifique se a API Web está funcionando</span><span class="sxs-lookup"><span data-stu-id="a6f49-204">Verify the web API is working</span></span>

<span data-ttu-id="a6f49-205">Você pode testar a API da Web ASP.NET Core implantada no gerenciamento de API do Azure por meio do portal do Azure com as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="a6f49-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="a6f49-206">Abra a guia **Testar**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="a6f49-207">Selecione **/** ou a operação **Get** .</span><span class="sxs-lookup"><span data-stu-id="a6f49-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="a6f49-208">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-208">Select **Send**.</span></span>

    ![Captura de tela do portal antes do teste](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="a6f49-210">Uma resposta bem-sucedida se parecerá com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="a6f49-210">A successful response will look like the following:</span></span>

![Captura de tela de uma resposta bem-sucedida do gerenciamento de API](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="a6f49-212">Limpeza</span><span class="sxs-lookup"><span data-stu-id="a6f49-212">Clean up</span></span>

<span data-ttu-id="a6f49-213">Quando você terminar de testar o aplicativo, vá para a [portal do Azure](https://portal.azure.com/) e exclua o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a6f49-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="a6f49-214">Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado.</span><span class="sxs-lookup"><span data-stu-id="a6f49-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Portal do Azure: Grupos de Recursos no menu da barra lateral](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="a6f49-216">Na página **Grupos de recursos**, selecione **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Portal do Azure: página Grupos de Recursos](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="a6f49-218">Insira o nome do grupo de recursos e selecione **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="a6f49-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="a6f49-219">O aplicativo e todos os outros recursos criados neste tutorial agora foram excluídos do Azure.</span><span class="sxs-lookup"><span data-stu-id="a6f49-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a6f49-220">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a6f49-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="a6f49-221">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a6f49-221">Additional resources</span></span>

- [<span data-ttu-id="a6f49-222">Gerenciamento de API do Azure</span><span class="sxs-lookup"><span data-stu-id="a6f49-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="a6f49-223">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="a6f49-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
