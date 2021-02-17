---
title: 'Tutorial: criar uma API Web com o ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 1f7c7db857090ff0a174d37b86e1265bab40b4fd
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564072"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="ffaeb-103">Tutorial: criar uma API Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffaeb-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="ffaeb-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="ffaeb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="ffaeb-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ffaeb-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ffaeb-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-107">Create a web API project.</span></span>
> * <span data-ttu-id="ffaeb-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ffaeb-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="ffaeb-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="ffaeb-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-111">Call the web API with Postman.</span></span>

<span data-ttu-id="ffaeb-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="ffaeb-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="ffaeb-113">Overview</span></span>

<span data-ttu-id="ffaeb-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ffaeb-115">API</span><span class="sxs-lookup"><span data-stu-id="ffaeb-115">API</span></span> | <span data-ttu-id="ffaeb-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="ffaeb-116">Description</span></span> | <span data-ttu-id="ffaeb-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ffaeb-117">Request body</span></span> | <span data-ttu-id="ffaeb-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="ffaeb-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="ffaeb-119">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-119">Get all to-do items</span></span> | <span data-ttu-id="ffaeb-120">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-120">None</span></span> | <span data-ttu-id="ffaeb-121">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="ffaeb-122">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="ffaeb-122">Get an item by ID</span></span> | <span data-ttu-id="ffaeb-123">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-123">None</span></span> | <span data-ttu-id="ffaeb-124">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="ffaeb-125">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="ffaeb-125">Add a new item</span></span> | <span data-ttu-id="ffaeb-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-126">To-do item</span></span> | <span data-ttu-id="ffaeb-127">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="ffaeb-128">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="ffaeb-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ffaeb-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-129">To-do item</span></span> | <span data-ttu-id="ffaeb-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-130">None</span></span> |
|<span data-ttu-id="ffaeb-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ffaeb-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="ffaeb-132">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="ffaeb-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ffaeb-133">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-133">None</span></span> | <span data-ttu-id="ffaeb-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-134">None</span></span>|

<span data-ttu-id="ffaeb-135">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-135">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="ffaeb-141">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ffaeb-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ffaeb-145">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="ffaeb-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-147">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ffaeb-148">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ffaeb-149">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ffaeb-150">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 5,0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="ffaeb-151">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-151">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ffaeb-154">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ffaeb-155">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ffaeb-156">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="ffaeb-157">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="ffaeb-158">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-158">The preceding commands:</span></span>

  * <span data-ttu-id="ffaeb-159">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="ffaeb-160">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ffaeb-162">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-162">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ffaeb-164">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >    >    >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ffaeb-165">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >    >    >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="ffaeb-167">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 5. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="ffaeb-168">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-168">Select **Next**.</span></span>

* <span data-ttu-id="ffaeb-169">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="ffaeb-171">Abra um terminal de comando na pasta do projeto e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="ffaeb-172">Testar o projeto</span><span class="sxs-lookup"><span data-stu-id="ffaeb-172">Test the project</span></span>

<span data-ttu-id="ffaeb-173">O modelo de projeto cria uma `WeatherForecast` API com suporte para [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ffaeb-175">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="ffaeb-176">O Visual Studio é iniciado:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-176">Visual Studio launches:</span></span>

* <span data-ttu-id="ffaeb-177">O servidor Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-177">The IIS Express web server.</span></span>
* <span data-ttu-id="ffaeb-178">O navegador padrão e navega para `https://localhost:<port>/swagger/index.html` , em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="ffaeb-180">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ffaeb-181">Em um navegador, vá para a seguinte URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="ffaeb-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-182">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ffaeb-183">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ffaeb-184">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ffaeb-185">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ffaeb-186">Acrescente `/swagger` à URL (altere a URL para `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="ffaeb-187">A página Swagger `/swagger/index.html` é exibida.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="ffaeb-188">Selecione **Get**  >  **Experimente**  >  **executar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="ffaeb-189">A página exibe:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-189">The page displays:</span></span>

* <span data-ttu-id="ffaeb-190">O comando de [ondulação](https://curl.haxx.se/) para testar a API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="ffaeb-191">A URL para testar a API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="ffaeb-192">O código de resposta, o corpo e os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="ffaeb-193">Uma caixa de listagem suspensa com tipos de mídia e o valor e o esquema de exemplo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="ffaeb-194">O Swagger é usado para gerar documentação útil e páginas de ajuda para APIs da Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="ffaeb-195">Este tutorial se concentra na criação de uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="ffaeb-196">Para obter mais informações sobre o Swagger, consulte <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="ffaeb-197">Copie e cole a **URL de solicitação** no navegador:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="ffaeb-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="ffaeb-198">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="ffaeb-199">Atualizar o launchUrl</span><span class="sxs-lookup"><span data-stu-id="ffaeb-199">Update the launchUrl</span></span>

<span data-ttu-id="ffaeb-200">No *Properties\launchSettings.jsem*, atualize `launchUrl` do `"swagger"` para `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="ffaeb-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="ffaeb-201">Como o Swagger foi removido, a marcação anterior altera a URL que é iniciada para o método GET do controlador adicionado nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="ffaeb-202">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="ffaeb-202">Add a model class</span></span>

<span data-ttu-id="ffaeb-203">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ffaeb-204">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-206">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ffaeb-207">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ffaeb-208">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="ffaeb-209">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ffaeb-210">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ffaeb-211">Substitua o código do modelo pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ffaeb-213">Adicione uma pasta chamada *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ffaeb-214">Adicione uma `TodoItem` classe à *Models* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ffaeb-216">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-216">Right-click the project.</span></span> <span data-ttu-id="ffaeb-217">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ffaeb-218">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-218">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ffaeb-220">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar** > **novo arquivo** >  > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ffaeb-221">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ffaeb-222">Substitua o código do modelo pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="ffaeb-223">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ffaeb-224">As classes de modelo podem ir em qualquer lugar no projeto, mas a *Models* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="ffaeb-225">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ffaeb-225">Add a database context</span></span>

<span data-ttu-id="ffaeb-226">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ffaeb-227">Essa classe é criada derivando-a da classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="ffaeb-229">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="ffaeb-229">Add NuGet packages</span></span>

* <span data-ttu-id="ffaeb-230">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="ffaeb-231">Selecione a guia **procurar** e, em seguida, insira **Microsoft. EntityFrameworkCore. inmemory** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="ffaeb-232">Selecione **Microsoft. EntityFrameworkCore. inmemory** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-232">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="ffaeb-233">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="ffaeb-235">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="ffaeb-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="ffaeb-236">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ffaeb-237">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-238">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ffaeb-239">Adicione uma `TodoContext` classe à *Models* pasta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ffaeb-240">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="ffaeb-241">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="ffaeb-241">Register the database context</span></span>

<span data-ttu-id="ffaeb-242">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ffaeb-243">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="ffaeb-244">Atualize *Startup.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="ffaeb-245">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-245">The preceding code:</span></span>

* <span data-ttu-id="ffaeb-246">Remove as chamadas do Swagger.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="ffaeb-247">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ffaeb-248">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ffaeb-249">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="ffaeb-250">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="ffaeb-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-252">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ffaeb-253">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ffaeb-254">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="ffaeb-255">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="ffaeb-256">Selecione **TodoItem (TodoApi. Models )** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="ffaeb-257">Selecione **TodoContext (TodoApi. Models )** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="ffaeb-258">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-259">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ffaeb-260">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="ffaeb-261">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-261">The preceding commands:</span></span>

* <span data-ttu-id="ffaeb-262">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="ffaeb-263">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="ffaeb-264">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="ffaeb-265">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-265">The generated code:</span></span>

* <span data-ttu-id="ffaeb-266">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ffaeb-267">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ffaeb-268">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ffaeb-269">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ffaeb-270">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="ffaeb-271">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="ffaeb-272">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="ffaeb-273">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="ffaeb-274">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="ffaeb-275">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="ffaeb-276">Atualizar o método de criação de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="ffaeb-277">Atualize a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="ffaeb-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="ffaeb-278">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ffaeb-279">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ffaeb-280">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ffaeb-281">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="ffaeb-282">Retorna um [código de status HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) se for bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="ffaeb-283">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ffaeb-284">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="ffaeb-285">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="ffaeb-286">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ffaeb-287">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ffaeb-288">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="ffaeb-289">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="ffaeb-289">Install Postman</span></span>

<span data-ttu-id="ffaeb-290">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ffaeb-291">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="ffaeb-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="ffaeb-292">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-292">Start the web app.</span></span>
* <span data-ttu-id="ffaeb-293">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-293">Start Postman.</span></span>
* <span data-ttu-id="ffaeb-294">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="ffaeb-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="ffaeb-295">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="ffaeb-296">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="ffaeb-297">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="ffaeb-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="ffaeb-298">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-298">Create a new request.</span></span>
* <span data-ttu-id="ffaeb-299">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ffaeb-300">Defina o URI como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ffaeb-301">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ffaeb-302">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="ffaeb-303">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ffaeb-304">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ffaeb-305">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ffaeb-306">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-306">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="ffaeb-308">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="ffaeb-308">Test the location header URI</span></span>

<span data-ttu-id="ffaeb-309">O URI do cabeçalho do local pode ser testado no navegador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="ffaeb-310">Copie e cole o URI do cabeçalho de local no navegador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="ffaeb-311">Para testar no postmaster:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-311">To test in Postman:</span></span>

* <span data-ttu-id="ffaeb-312">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ffaeb-313">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-313">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="ffaeb-315">Defina o método HTTP como `GET`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="ffaeb-316">Defina o URI como `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="ffaeb-317">Por exemplo, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="ffaeb-318">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="ffaeb-319">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="ffaeb-319">Examine the GET methods</span></span>

<span data-ttu-id="ffaeb-320">Dois pontos de extremidade GET são implementados:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="ffaeb-321">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="ffaeb-322">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="ffaeb-323">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="ffaeb-324">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="ffaeb-324">Test Get with Postman</span></span>

* <span data-ttu-id="ffaeb-325">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-325">Create a new request.</span></span>
* <span data-ttu-id="ffaeb-326">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="ffaeb-327">Defina o URI de solicitação como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ffaeb-328">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ffaeb-329">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ffaeb-330">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-330">Select **Send**.</span></span>

<span data-ttu-id="ffaeb-331">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-331">This app uses an in-memory database.</span></span> <span data-ttu-id="ffaeb-332">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="ffaeb-333">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="ffaeb-334">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="ffaeb-334">Routing and URL paths</span></span>

<span data-ttu-id="ffaeb-335">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ffaeb-336">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ffaeb-337">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="ffaeb-338">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ffaeb-339">Para esta amostra, o nome da classe do controlador é **TodoItems** Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="ffaeb-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="ffaeb-340">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ffaeb-341">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ffaeb-342">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-342">This sample doesn't use a template.</span></span> <span data-ttu-id="ffaeb-343">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ffaeb-344">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ffaeb-345">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="ffaeb-346">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="ffaeb-346">Return values</span></span>

<span data-ttu-id="ffaeb-347">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ffaeb-348">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ffaeb-349">O código de resposta para esse tipo de retorno é [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ffaeb-350">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ffaeb-351">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ffaeb-352">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ffaeb-353">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro de [status 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ffaeb-354">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ffaeb-355">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="ffaeb-356">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-356">The PutTodoItem method</span></span>

<span data-ttu-id="ffaeb-357">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="ffaeb-358">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ffaeb-359">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ffaeb-360">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ffaeb-361">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ffaeb-362">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="ffaeb-363">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="ffaeb-364">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ffaeb-365">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ffaeb-366">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ffaeb-367">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="ffaeb-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ffaeb-368">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-368">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="ffaeb-370">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="ffaeb-371">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="ffaeb-372">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="ffaeb-373">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ffaeb-374">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ffaeb-375">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="ffaeb-376">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="ffaeb-377">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-377">Prevent over-posting</span></span>

<span data-ttu-id="ffaeb-378">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="ffaeb-379">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="ffaeb-380">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="ffaeb-381">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="ffaeb-382">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="ffaeb-383">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-383">A DTO may be used to:</span></span>

* <span data-ttu-id="ffaeb-384">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-384">Prevent over-posting.</span></span>
* <span data-ttu-id="ffaeb-385">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="ffaeb-386">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="ffaeb-387">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="ffaeb-388">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="ffaeb-389">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="ffaeb-390">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="ffaeb-391">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="ffaeb-392">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="ffaeb-393">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="ffaeb-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="ffaeb-394">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ffaeb-395">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="ffaeb-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="ffaeb-396">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ffaeb-397">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ffaeb-398">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-398">Create a web API project.</span></span>
> * <span data-ttu-id="ffaeb-399">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ffaeb-400">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="ffaeb-401">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="ffaeb-402">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-402">Call the web API with Postman.</span></span>

<span data-ttu-id="ffaeb-403">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="ffaeb-404">Visão geral</span><span class="sxs-lookup"><span data-stu-id="ffaeb-404">Overview</span></span>

<span data-ttu-id="ffaeb-405">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ffaeb-406">API</span><span class="sxs-lookup"><span data-stu-id="ffaeb-406">API</span></span> | <span data-ttu-id="ffaeb-407">Descrição</span><span class="sxs-lookup"><span data-stu-id="ffaeb-407">Description</span></span> | <span data-ttu-id="ffaeb-408">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ffaeb-408">Request body</span></span> | <span data-ttu-id="ffaeb-409">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="ffaeb-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="ffaeb-410">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-410">Get all to-do items</span></span> | <span data-ttu-id="ffaeb-411">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-411">None</span></span> | <span data-ttu-id="ffaeb-412">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="ffaeb-413">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="ffaeb-413">Get an item by ID</span></span> | <span data-ttu-id="ffaeb-414">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-414">None</span></span> | <span data-ttu-id="ffaeb-415">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="ffaeb-416">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="ffaeb-416">Add a new item</span></span> | <span data-ttu-id="ffaeb-417">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-417">To-do item</span></span> | <span data-ttu-id="ffaeb-418">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="ffaeb-419">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="ffaeb-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ffaeb-420">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-420">To-do item</span></span> | <span data-ttu-id="ffaeb-421">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-421">None</span></span> |
|<span data-ttu-id="ffaeb-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ffaeb-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="ffaeb-423">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="ffaeb-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ffaeb-424">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-424">None</span></span> | <span data-ttu-id="ffaeb-425">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-425">None</span></span>|

<span data-ttu-id="ffaeb-426">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-426">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="ffaeb-432">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ffaeb-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-435">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ffaeb-436">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="ffaeb-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-438">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ffaeb-439">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ffaeb-440">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ffaeb-441">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="ffaeb-442">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-442">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ffaeb-445">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ffaeb-446">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ffaeb-447">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="ffaeb-448">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="ffaeb-449">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-449">The preceding commands:</span></span>

  * <span data-ttu-id="ffaeb-450">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="ffaeb-451">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-452">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ffaeb-453">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-453">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ffaeb-455">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >    >    >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ffaeb-456">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >    >    >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="ffaeb-458">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="ffaeb-459">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-459">Select **Next**.</span></span>

* <span data-ttu-id="ffaeb-460">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="ffaeb-462">Abra um terminal de comando na pasta do projeto e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="ffaeb-463">Testar a API</span><span class="sxs-lookup"><span data-stu-id="ffaeb-463">Test the API</span></span>

<span data-ttu-id="ffaeb-464">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="ffaeb-465">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ffaeb-467">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ffaeb-468">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="ffaeb-469">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="ffaeb-470">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ffaeb-472">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ffaeb-473">Em um navegador, acesse a seguinte URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-474">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ffaeb-475">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ffaeb-476">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ffaeb-477">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ffaeb-478">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="ffaeb-479">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-479">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="ffaeb-480">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="ffaeb-480">Add a model class</span></span>

<span data-ttu-id="ffaeb-481">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ffaeb-482">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-484">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ffaeb-485">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ffaeb-486">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="ffaeb-487">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ffaeb-488">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ffaeb-489">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ffaeb-491">Adicione uma pasta chamada *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ffaeb-492">Adicione uma `TodoItem` classe à *Models* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-493">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ffaeb-494">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-494">Right-click the project.</span></span> <span data-ttu-id="ffaeb-495">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ffaeb-496">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-496">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ffaeb-498">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar** > **novo arquivo** >  > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ffaeb-499">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ffaeb-500">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="ffaeb-501">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ffaeb-502">As classes de modelo podem ir em qualquer lugar no projeto, mas a *Models* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="ffaeb-503">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="ffaeb-503">Add a database context</span></span>

<span data-ttu-id="ffaeb-504">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ffaeb-505">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="ffaeb-507">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="ffaeb-507">Add NuGet packages</span></span>

* <span data-ttu-id="ffaeb-508">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="ffaeb-509">Selecione a guia **procurar** e, em seguida, insira **Microsoft. EntityFrameworkCore. inmemory** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="ffaeb-510">Selecione **Microsoft. EntityFrameworkCore. inmemory** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="ffaeb-511">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="ffaeb-513">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="ffaeb-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="ffaeb-514">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ffaeb-515">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-516">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ffaeb-517">Adicione uma `TodoContext` classe à *Models* pasta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ffaeb-518">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="ffaeb-519">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="ffaeb-519">Register the database context</span></span>

<span data-ttu-id="ffaeb-520">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ffaeb-521">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="ffaeb-522">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="ffaeb-523">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-523">The preceding code:</span></span>

* <span data-ttu-id="ffaeb-524">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ffaeb-525">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ffaeb-526">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="ffaeb-527">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="ffaeb-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-529">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ffaeb-530">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ffaeb-531">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="ffaeb-532">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="ffaeb-533">Selecione **TodoItem (TodoApi. Models )** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="ffaeb-534">Selecione **TodoContext (TodoApi. Models )** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="ffaeb-535">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-536">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ffaeb-537">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="ffaeb-538">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-538">The preceding commands:</span></span>

* <span data-ttu-id="ffaeb-539">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="ffaeb-540">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="ffaeb-541">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="ffaeb-542">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-542">The generated code:</span></span>

* <span data-ttu-id="ffaeb-543">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ffaeb-544">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ffaeb-545">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ffaeb-546">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ffaeb-547">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="ffaeb-548">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="ffaeb-549">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="ffaeb-550">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="ffaeb-551">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="ffaeb-552">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="ffaeb-553">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="ffaeb-554">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="ffaeb-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="ffaeb-555">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ffaeb-556">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ffaeb-557">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ffaeb-558">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="ffaeb-559">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="ffaeb-560">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ffaeb-561">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="ffaeb-562">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="ffaeb-563">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ffaeb-564">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ffaeb-565">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="ffaeb-566">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="ffaeb-566">Install Postman</span></span>

<span data-ttu-id="ffaeb-567">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ffaeb-568">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="ffaeb-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="ffaeb-569">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-569">Start the web app.</span></span>
* <span data-ttu-id="ffaeb-570">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-570">Start Postman.</span></span>
* <span data-ttu-id="ffaeb-571">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="ffaeb-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="ffaeb-572">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="ffaeb-573">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="ffaeb-574">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="ffaeb-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="ffaeb-575">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-575">Create a new request.</span></span>
* <span data-ttu-id="ffaeb-576">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ffaeb-577">Defina o URI como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ffaeb-578">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ffaeb-579">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="ffaeb-580">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ffaeb-581">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ffaeb-582">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ffaeb-583">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-583">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="ffaeb-585">Testar o URI do cabeçalho do local com o postmaster</span><span class="sxs-lookup"><span data-stu-id="ffaeb-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="ffaeb-586">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ffaeb-587">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-587">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="ffaeb-589">Defina o método HTTP como `GET`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="ffaeb-590">Defina o URI como `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="ffaeb-591">Por exemplo, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="ffaeb-592">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="ffaeb-593">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="ffaeb-593">Examine the GET methods</span></span>

<span data-ttu-id="ffaeb-594">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="ffaeb-595">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="ffaeb-596">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="ffaeb-597">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="ffaeb-598">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="ffaeb-598">Test Get with Postman</span></span>

* <span data-ttu-id="ffaeb-599">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-599">Create a new request.</span></span>
* <span data-ttu-id="ffaeb-600">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="ffaeb-601">Defina o URI de solicitação como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ffaeb-602">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ffaeb-603">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ffaeb-604">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-604">Select **Send**.</span></span>

<span data-ttu-id="ffaeb-605">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-605">This app uses an in-memory database.</span></span> <span data-ttu-id="ffaeb-606">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="ffaeb-607">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="ffaeb-608">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="ffaeb-608">Routing and URL paths</span></span>

<span data-ttu-id="ffaeb-609">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ffaeb-610">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ffaeb-611">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="ffaeb-612">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ffaeb-613">Para esta amostra, o nome da classe do controlador é **TodoItems** Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="ffaeb-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="ffaeb-614">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ffaeb-615">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ffaeb-616">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-616">This sample doesn't use a template.</span></span> <span data-ttu-id="ffaeb-617">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ffaeb-618">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ffaeb-619">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="ffaeb-620">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="ffaeb-620">Return values</span></span> 

<span data-ttu-id="ffaeb-621">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ffaeb-622">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ffaeb-623">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ffaeb-624">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ffaeb-625">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ffaeb-626">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ffaeb-627">Se nenhum item corresponder à ID solicitada, o método retornará um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> código de erro 404.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ffaeb-628">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ffaeb-629">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="ffaeb-630">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-630">The PutTodoItem method</span></span>

<span data-ttu-id="ffaeb-631">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="ffaeb-632">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ffaeb-633">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ffaeb-634">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ffaeb-635">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ffaeb-636">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="ffaeb-637">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="ffaeb-638">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ffaeb-639">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ffaeb-640">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ffaeb-641">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como "alimentar feed":</span><span class="sxs-lookup"><span data-stu-id="ffaeb-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ffaeb-642">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-642">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="ffaeb-644">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="ffaeb-645">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="ffaeb-646">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="ffaeb-647">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ffaeb-648">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ffaeb-649">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="ffaeb-650">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="ffaeb-651">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="ffaeb-651">Prevent over-posting</span></span>

<span data-ttu-id="ffaeb-652">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="ffaeb-653">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="ffaeb-654">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="ffaeb-655">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="ffaeb-656">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="ffaeb-657">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-657">A DTO may be used to:</span></span>

* <span data-ttu-id="ffaeb-658">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-658">Prevent over-posting.</span></span>
* <span data-ttu-id="ffaeb-659">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="ffaeb-660">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="ffaeb-661">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="ffaeb-662">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="ffaeb-663">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="ffaeb-664">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="ffaeb-665">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="ffaeb-666">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="ffaeb-667">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="ffaeb-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="ffaeb-668">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ffaeb-669">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="ffaeb-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="ffaeb-670">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ffaeb-671">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ffaeb-672">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-672">Create a web API project.</span></span>
> * <span data-ttu-id="ffaeb-673">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ffaeb-674">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-674">Add a controller.</span></span>
> * <span data-ttu-id="ffaeb-675">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="ffaeb-676">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="ffaeb-677">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-677">Specify return values.</span></span>
> * <span data-ttu-id="ffaeb-678">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="ffaeb-679">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="ffaeb-680">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="ffaeb-681">Visão geral 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-681">Overview 2.1</span></span>

<span data-ttu-id="ffaeb-682">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ffaeb-683">API</span><span class="sxs-lookup"><span data-stu-id="ffaeb-683">API</span></span> | <span data-ttu-id="ffaeb-684">Descrição</span><span class="sxs-lookup"><span data-stu-id="ffaeb-684">Description</span></span> | <span data-ttu-id="ffaeb-685">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ffaeb-685">Request body</span></span> | <span data-ttu-id="ffaeb-686">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="ffaeb-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="ffaeb-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ffaeb-687">GET /api/TodoItems</span></span> | <span data-ttu-id="ffaeb-688">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-688">Get all to-do items</span></span> | <span data-ttu-id="ffaeb-689">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-689">None</span></span> | <span data-ttu-id="ffaeb-690">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-690">Array of to-do items</span></span>|
|<span data-ttu-id="ffaeb-691">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="ffaeb-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="ffaeb-692">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="ffaeb-692">Get an item by ID</span></span> | <span data-ttu-id="ffaeb-693">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-693">None</span></span> | <span data-ttu-id="ffaeb-694">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-694">To-do item</span></span>|
|<span data-ttu-id="ffaeb-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ffaeb-695">POST /api/TodoItems</span></span> | <span data-ttu-id="ffaeb-696">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="ffaeb-696">Add a new item</span></span> | <span data-ttu-id="ffaeb-697">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-697">To-do item</span></span> | <span data-ttu-id="ffaeb-698">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-698">To-do item</span></span> |
|<span data-ttu-id="ffaeb-699">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="ffaeb-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="ffaeb-700">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="ffaeb-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ffaeb-701">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="ffaeb-701">To-do item</span></span> | <span data-ttu-id="ffaeb-702">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-702">None</span></span> |
|<span data-ttu-id="ffaeb-703">EXCLUIR/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="ffaeb-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="ffaeb-704">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="ffaeb-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ffaeb-705">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-705">None</span></span> | <span data-ttu-id="ffaeb-706">Nenhum</span><span class="sxs-lookup"><span data-stu-id="ffaeb-706">None</span></span>|

<span data-ttu-id="ffaeb-707">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-707">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="ffaeb-713">Pré-requisitos 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-716">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="ffaeb-717">Criar um projeto Web 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-719">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ffaeb-720">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ffaeb-721">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ffaeb-722">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="ffaeb-723">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="ffaeb-724">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-724">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ffaeb-727">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ffaeb-728">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ffaeb-729">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="ffaeb-730">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="ffaeb-731">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-732">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ffaeb-733">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-733">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ffaeb-735">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >    >    >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ffaeb-736">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >    >    >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="ffaeb-737">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="ffaeb-738">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-738">Select **Next**.</span></span>

* <span data-ttu-id="ffaeb-739">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="ffaeb-741">Testar a API 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-741">Test the API 2.1</span></span>

<span data-ttu-id="ffaeb-742">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-742">The project template creates a `values` API.</span></span> <span data-ttu-id="ffaeb-743">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ffaeb-745">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ffaeb-746">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="ffaeb-747">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="ffaeb-748">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ffaeb-750">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ffaeb-751">Em um navegador, acesse a seguinte URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-752">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ffaeb-753">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ffaeb-754">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ffaeb-755">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ffaeb-756">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="ffaeb-757">O JSON a seguir será retornado:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="ffaeb-758">Adicionar uma classe de modelo 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-758">Add a model class 2.1</span></span>

<span data-ttu-id="ffaeb-759">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ffaeb-760">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-762">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ffaeb-763">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ffaeb-764">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="ffaeb-765">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ffaeb-766">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ffaeb-767">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ffaeb-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ffaeb-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ffaeb-769">Adicione uma pasta chamada *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ffaeb-770">Adicione uma `TodoItem` classe à *Models* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-771">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ffaeb-772">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-772">Right-click the project.</span></span> <span data-ttu-id="ffaeb-773">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ffaeb-774">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-774">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ffaeb-776">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar** > **novo arquivo** >  > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ffaeb-777">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ffaeb-778">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="ffaeb-779">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ffaeb-780">As classes de modelo podem ir em qualquer lugar no projeto, mas a *Models* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="ffaeb-781">Adicionar um contexto de banco de dados 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-781">Add a database context 2.1</span></span>

<span data-ttu-id="ffaeb-782">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ffaeb-783">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-785">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ffaeb-786">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-787">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ffaeb-788">Adicione uma `TodoContext` classe à *Models* pasta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ffaeb-789">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="ffaeb-790">Registrar o contexto de banco de dados 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-790">Register the database context 2.1</span></span>

<span data-ttu-id="ffaeb-791">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ffaeb-792">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="ffaeb-793">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="ffaeb-794">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-794">The preceding code:</span></span>

* <span data-ttu-id="ffaeb-795">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ffaeb-796">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ffaeb-797">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="ffaeb-798">Adicionar um controlador 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-800">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ffaeb-801">Selecione **Adicionar** > **novo item**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="ffaeb-802">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="ffaeb-803">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-805">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ffaeb-806">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="ffaeb-807">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="ffaeb-808">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-808">The preceding code:</span></span>

* <span data-ttu-id="ffaeb-809">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="ffaeb-810">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ffaeb-811">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ffaeb-812">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ffaeb-813">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ffaeb-814">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="ffaeb-815">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="ffaeb-816">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="ffaeb-817">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="ffaeb-818">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="ffaeb-819">Adicionar os métodos get 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-819">Add Get methods 2.1</span></span>

<span data-ttu-id="ffaeb-820">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="ffaeb-821">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="ffaeb-822">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-822">Stop the app if it's still running.</span></span> <span data-ttu-id="ffaeb-823">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="ffaeb-824">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="ffaeb-825">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="ffaeb-826">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="ffaeb-827">Caminhos de roteamento e URL 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="ffaeb-828">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ffaeb-829">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ffaeb-830">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="ffaeb-831">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ffaeb-832">Para esta amostra, o nome da classe do controlador é **Todo** Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="ffaeb-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="ffaeb-833">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ffaeb-834">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ffaeb-835">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-835">This sample doesn't use a template.</span></span> <span data-ttu-id="ffaeb-836">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ffaeb-837">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ffaeb-838">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="ffaeb-839">Valores de retorno 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-839">Return values 2.1</span></span>

<span data-ttu-id="ffaeb-840">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ffaeb-841">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ffaeb-842">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ffaeb-843">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ffaeb-844">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ffaeb-845">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ffaeb-846">Se nenhum item corresponder à ID solicitada, o método retornará um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> código de erro 404.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ffaeb-847">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ffaeb-848">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="ffaeb-849">Testar o método GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="ffaeb-850">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ffaeb-851">Instalar o [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="ffaeb-852">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-852">Start the web app.</span></span>
* <span data-ttu-id="ffaeb-853">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-853">Start Postman.</span></span>
* <span data-ttu-id="ffaeb-854">Desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ffaeb-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffaeb-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ffaeb-856">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ffaeb-857">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ffaeb-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ffaeb-858">De   >  **preferências** do postmaster (guia **geral** ), desabilite a **verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="ffaeb-859">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="ffaeb-860">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="ffaeb-861">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-861">Create a new request.</span></span>
  * <span data-ttu-id="ffaeb-862">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="ffaeb-863">Defina o URI de solicitação como `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="ffaeb-864">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="ffaeb-865">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ffaeb-866">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-866">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="ffaeb-868">Adicionar um método Create 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-868">Add a Create method 2.1</span></span>

<span data-ttu-id="ffaeb-869">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="ffaeb-870">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ffaeb-871">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ffaeb-872">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="ffaeb-873">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="ffaeb-874">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ffaeb-875">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="ffaeb-876">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="ffaeb-877">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ffaeb-878">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ffaeb-879">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="ffaeb-880">Testar o método PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="ffaeb-881">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-881">Build the project.</span></span>
* <span data-ttu-id="ffaeb-882">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ffaeb-883">Defina o URI como `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="ffaeb-884">Por exemplo, `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="ffaeb-885">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="ffaeb-886">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ffaeb-887">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ffaeb-888">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ffaeb-889">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-889">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="ffaeb-891">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="ffaeb-892">Testar o URI do cabeçalho do local 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="ffaeb-893">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ffaeb-894">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-894">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="ffaeb-896">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-896">Set the method to GET.</span></span>
* <span data-ttu-id="ffaeb-897">Defina o URI como `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="ffaeb-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="ffaeb-898">Por exemplo, `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="ffaeb-899">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="ffaeb-900">Adicionar um método PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="ffaeb-901">Em seguida, adicione o método `PutTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="ffaeb-902">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ffaeb-903">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ffaeb-904">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ffaeb-905">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ffaeb-906">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="ffaeb-907">Testar o método PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="ffaeb-908">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ffaeb-909">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ffaeb-910">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ffaeb-911">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como "alimentar feed":</span><span class="sxs-lookup"><span data-stu-id="ffaeb-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ffaeb-912">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-912">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="ffaeb-914">Adicionar um método DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="ffaeb-915">Em seguida, adicione o método `DeleteTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="ffaeb-916">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="ffaeb-917">Testar o método DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="ffaeb-918">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ffaeb-919">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ffaeb-920">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="ffaeb-921">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-921">Select **Send**.</span></span>

<span data-ttu-id="ffaeb-922">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="ffaeb-923">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="ffaeb-924">Chamar a API Web com JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="ffaeb-925">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="ffaeb-926">o jQuery inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-926">jQuery initiates the request.</span></span> <span data-ttu-id="ffaeb-927">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="ffaeb-928">Configurar o aplicativo para [servir arquivos estáticos](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) e [habilitar o mapeamento de arquivo padrão](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="ffaeb-929">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="ffaeb-930">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="ffaeb-931">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="ffaeb-932">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="ffaeb-933">Substitua o conteúdo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="ffaeb-934">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="ffaeb-935">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="ffaeb-936">Remova a `launchUrl` propriedade para forçar o aplicativo a abrir em *index.html* &mdash; o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="ffaeb-937">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="ffaeb-938">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="ffaeb-939">Obter uma lista de itens pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="ffaeb-940">o jQuery envia uma solicitação HTTP GET para a API da Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="ffaeb-941">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="ffaeb-942">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="ffaeb-943">Adicionar um item pendente 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="ffaeb-944">o jQuery envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="ffaeb-945">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="ffaeb-946">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="ffaeb-947">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="ffaeb-948">Atualizar um item de tarefas pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="ffaeb-949">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="ffaeb-950">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="ffaeb-951">Excluir um item de tarefas pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="ffaeb-952">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="ffaeb-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="ffaeb-953">Adicionar suporte de autenticação a uma API Web 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="ffaeb-954">Recursos adicionais 2,1</span><span class="sxs-lookup"><span data-stu-id="ffaeb-954">Additional resources 2.1</span></span>

<span data-ttu-id="ffaeb-955">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="ffaeb-956">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ffaeb-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ffaeb-957">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="ffaeb-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="ffaeb-958">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="ffaeb-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
