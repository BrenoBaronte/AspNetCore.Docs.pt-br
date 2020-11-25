---
title: 'Tutorial: criar uma API Web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
ms.openlocfilehash: 9299ba685c95ced522fc725854a66252e67fc799
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "96024950"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="d5024-103">Tutorial: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5024-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="d5024-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="d5024-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="d5024-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5024-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d5024-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="d5024-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d5024-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-107">Create a web API project.</span></span>
> * <span data-ttu-id="d5024-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d5024-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="d5024-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="d5024-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="d5024-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="d5024-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-111">Call the web API with Postman.</span></span>

<span data-ttu-id="d5024-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="d5024-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="d5024-113">Overview</span></span>

<span data-ttu-id="d5024-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="d5024-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d5024-115">API</span><span class="sxs-lookup"><span data-stu-id="d5024-115">API</span></span> | <span data-ttu-id="d5024-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5024-116">Description</span></span> | <span data-ttu-id="d5024-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="d5024-117">Request body</span></span> | <span data-ttu-id="d5024-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="d5024-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="d5024-119">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-119">Get all to-do items</span></span> | <span data-ttu-id="d5024-120">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-120">None</span></span> | <span data-ttu-id="d5024-121">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="d5024-122">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="d5024-122">Get an item by ID</span></span> | <span data-ttu-id="d5024-123">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-123">None</span></span> | <span data-ttu-id="d5024-124">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="d5024-125">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="d5024-125">Add a new item</span></span> | <span data-ttu-id="d5024-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-126">To-do item</span></span> | <span data-ttu-id="d5024-127">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="d5024-128">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="d5024-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d5024-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-129">To-do item</span></span> | <span data-ttu-id="d5024-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-130">None</span></span> |
|<span data-ttu-id="d5024-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d5024-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="d5024-132">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d5024-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d5024-133">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-133">None</span></span> | <span data-ttu-id="d5024-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-134">None</span></span>|

<span data-ttu-id="d5024-135">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-135">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="d5024-141">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d5024-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="d5024-145">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="d5024-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-147">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="d5024-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d5024-148">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d5024-149">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d5024-150">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 5,0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="d5024-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="d5024-151">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-151">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d5024-154">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d5024-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d5024-155">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d5024-156">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5024-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="d5024-157">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d5024-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="d5024-158">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="d5024-158">The preceding commands:</span></span>

  * <span data-ttu-id="d5024-159">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d5024-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="d5024-160">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5024-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5024-162">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="d5024-162">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d5024-164">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="d5024-165">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="d5024-167">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 5. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="d5024-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="d5024-168">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-168">Select **Next**.</span></span>

* <span data-ttu-id="d5024-169">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="d5024-171">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5024-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="d5024-172">Testar o projeto</span><span class="sxs-lookup"><span data-stu-id="d5024-172">Test the project</span></span>

<span data-ttu-id="d5024-173">O modelo de projeto cria uma `WeatherForecast` API com suporte para [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="d5024-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5024-175">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="d5024-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="d5024-176">O Visual Studio é iniciado:</span><span class="sxs-lookup"><span data-stu-id="d5024-176">Visual Studio launches:</span></span>

* <span data-ttu-id="d5024-177">O servidor Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d5024-177">The IIS Express web server.</span></span>
* <span data-ttu-id="d5024-178">O navegador padrão e navega para `https://localhost:<port>/swagger/index.html` , em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="d5024-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="d5024-180">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d5024-181">Em um navegador, vá para a seguinte URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="d5024-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-182">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d5024-183">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d5024-184">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="d5024-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d5024-185">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="d5024-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d5024-186">Acrescente `/swagger` à URL (altere a URL para `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="d5024-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="d5024-187">A página Swagger `/swagger/index.html` é exibida.</span><span class="sxs-lookup"><span data-stu-id="d5024-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="d5024-188">Selecione **Get**  >  **Experimente**  >  **executar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="d5024-189">A página exibe:</span><span class="sxs-lookup"><span data-stu-id="d5024-189">The page displays:</span></span>

* <span data-ttu-id="d5024-190">O comando de [ondulação](https://curl.haxx.se/) para testar a API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="d5024-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="d5024-191">A URL para testar a API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="d5024-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="d5024-192">O código de resposta, o corpo e os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="d5024-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="d5024-193">Uma caixa de listagem suspensa com tipos de mídia e o valor e o esquema de exemplo.</span><span class="sxs-lookup"><span data-stu-id="d5024-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="d5024-194">O Swagger é usado para gerar documentação útil e páginas de ajuda para APIs da Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="d5024-195">Este tutorial se concentra na criação de uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="d5024-196">Para obter mais informações sobre o Swagger, consulte <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="d5024-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="d5024-197">Copie e cole a **URL de solicitação** no navegador:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="d5024-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="d5024-198">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="d5024-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="d5024-199">Atualizar o launchUrl</span><span class="sxs-lookup"><span data-stu-id="d5024-199">Update the launchUrl</span></span>

<span data-ttu-id="d5024-200">No *Properties\launchSettings.jsem*, atualize `launchUrl` do `"swagger"` para `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="d5024-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="d5024-201">Como o Swagger foi removido, a marcação anterior altera a URL que é iniciada para o método GET do controlador adicionado nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="d5024-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="d5024-202">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="d5024-202">Add a model class</span></span>

<span data-ttu-id="d5024-203">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d5024-204">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="d5024-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-206">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d5024-207">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d5024-208">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="d5024-209">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5024-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d5024-210">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d5024-211">Substitua o código do modelo pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="d5024-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d5024-213">Adicione uma pasta chamada *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d5024-214">Adicione uma `TodoItem` classe à *Models* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5024-216">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-216">Right-click the project.</span></span> <span data-ttu-id="d5024-217">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d5024-218">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-218">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d5024-220">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar** > **novo arquivo** > **General** > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="d5024-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d5024-221">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d5024-222">Substitua o código do modelo pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="d5024-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="d5024-223">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="d5024-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d5024-224">As classes de modelo podem ir em qualquer lugar no projeto, mas a *Models* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="d5024-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d5024-225">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5024-225">Add a database context</span></span>

<span data-ttu-id="d5024-226">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d5024-227">Essa classe é criada derivando-a da classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d5024-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="d5024-229">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="d5024-229">Add NuGet packages</span></span>

* <span data-ttu-id="d5024-230">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="d5024-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="d5024-231">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d5024-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="d5024-232">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="d5024-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="d5024-233">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="d5024-234">Use as instruções anteriores para adicionar o pacote NuGet **Microsoft. EntityFrameworkCore. inmemory** .</span><span class="sxs-lookup"><span data-stu-id="d5024-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="d5024-235">![Gerenciador de Pacotes do NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="d5024-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="d5024-236">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="d5024-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="d5024-237">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5024-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d5024-238">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5024-239">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d5024-240">Adicione uma `TodoContext` classe à *Models* pasta.</span><span class="sxs-lookup"><span data-stu-id="d5024-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d5024-241">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d5024-242">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5024-242">Register the database context</span></span>

<span data-ttu-id="d5024-243">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d5024-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d5024-244">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="d5024-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="d5024-245">Atualize *Startup.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="d5024-246">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="d5024-246">The preceding code:</span></span>

* <span data-ttu-id="d5024-247">Remove as chamadas do Swagger.</span><span class="sxs-lookup"><span data-stu-id="d5024-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="d5024-248">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="d5024-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d5024-249">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="d5024-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d5024-250">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="d5024-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="d5024-251">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="d5024-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-253">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="d5024-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d5024-254">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="d5024-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d5024-255">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="d5024-256">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="d5024-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="d5024-257">Selecione **TodoItem (TodoApi. Models )** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="d5024-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="d5024-258">Selecione **TodoContext (TodoApi. Models )** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="d5024-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="d5024-259">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5024-260">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d5024-261">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5024-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="d5024-262">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="d5024-262">The preceding commands:</span></span>

* <span data-ttu-id="d5024-263">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d5024-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="d5024-264">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="d5024-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="d5024-265">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="d5024-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="d5024-266">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="d5024-266">The generated code:</span></span>

* <span data-ttu-id="d5024-267">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="d5024-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="d5024-268">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d5024-269">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="d5024-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d5024-270">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d5024-271">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="d5024-272">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="d5024-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="d5024-273">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="d5024-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="d5024-274">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="d5024-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="d5024-275">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="d5024-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="d5024-276">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="d5024-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="d5024-277">Atualizar o método de criação de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="d5024-278">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="d5024-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="d5024-279">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="d5024-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="d5024-280">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5024-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d5024-281">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d5024-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d5024-282">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="d5024-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="d5024-283">Retorna um [código de status HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) se for bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="d5024-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="d5024-284">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="d5024-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d5024-285">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="d5024-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="d5024-286">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="d5024-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="d5024-287">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="d5024-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d5024-288">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="d5024-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d5024-289">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="d5024-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="d5024-290">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="d5024-290">Install Postman</span></span>

<span data-ttu-id="d5024-291">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d5024-292">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="d5024-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="d5024-293">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-293">Start the web app.</span></span>
* <span data-ttu-id="d5024-294">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-294">Start Postman.</span></span>
* <span data-ttu-id="d5024-295">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="d5024-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="d5024-296">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="d5024-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="d5024-297">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="d5024-298">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="d5024-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="d5024-299">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d5024-299">Create a new request.</span></span>
* <span data-ttu-id="d5024-300">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="d5024-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d5024-301">Defina o URI como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="d5024-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="d5024-302">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="d5024-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="d5024-303">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="d5024-304">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="d5024-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d5024-305">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="d5024-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d5024-306">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="d5024-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d5024-307">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-307">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="d5024-309">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="d5024-309">Test the location header URI</span></span>

<span data-ttu-id="d5024-310">O URI do cabeçalho do local pode ser testado no navegador.</span><span class="sxs-lookup"><span data-stu-id="d5024-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="d5024-311">Copie e cole o URI do cabeçalho de local no navegador.</span><span class="sxs-lookup"><span data-stu-id="d5024-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="d5024-312">Para testar no postmaster:</span><span class="sxs-lookup"><span data-stu-id="d5024-312">To test in Postman:</span></span>

* <span data-ttu-id="d5024-313">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d5024-314">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="d5024-314">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="d5024-316">Defina o método HTTP como `GET`.</span><span class="sxs-lookup"><span data-stu-id="d5024-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="d5024-317">Defina o URI como `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="d5024-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="d5024-318">Por exemplo, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="d5024-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="d5024-319">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="d5024-320">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="d5024-320">Examine the GET methods</span></span>

<span data-ttu-id="d5024-321">Dois pontos de extremidade GET são implementados:</span><span class="sxs-lookup"><span data-stu-id="d5024-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="d5024-322">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="d5024-323">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d5024-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="d5024-324">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="d5024-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="d5024-325">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="d5024-325">Test Get with Postman</span></span>

* <span data-ttu-id="d5024-326">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d5024-326">Create a new request.</span></span>
* <span data-ttu-id="d5024-327">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="d5024-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="d5024-328">Defina o URI de solicitação como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="d5024-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="d5024-329">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="d5024-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="d5024-330">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d5024-331">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-331">Select **Send**.</span></span>

<span data-ttu-id="d5024-332">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="d5024-332">This app uses an in-memory database.</span></span> <span data-ttu-id="d5024-333">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="d5024-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="d5024-334">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="d5024-335">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="d5024-335">Routing and URL paths</span></span>

<span data-ttu-id="d5024-336">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="d5024-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d5024-337">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d5024-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d5024-338">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="d5024-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="d5024-339">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="d5024-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d5024-340">Para esta amostra, o nome da classe do controlador é **TodoItems** Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="d5024-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="d5024-341">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d5024-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d5024-342">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="d5024-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d5024-343">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="d5024-343">This sample doesn't use a template.</span></span> <span data-ttu-id="d5024-344">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d5024-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d5024-345">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="d5024-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d5024-346">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d5024-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d5024-347">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="d5024-347">Return values</span></span>

<span data-ttu-id="d5024-348">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="d5024-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d5024-349">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="d5024-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d5024-350">O código de resposta para esse tipo de retorno é [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="d5024-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d5024-351">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="d5024-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d5024-352">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5024-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d5024-353">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="d5024-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d5024-354">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro de [status 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="d5024-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="d5024-355">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="d5024-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d5024-356">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="d5024-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="d5024-357">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-357">The PutTodoItem method</span></span>

<span data-ttu-id="d5024-358">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="d5024-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="d5024-359">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d5024-360">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d5024-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d5024-361">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="d5024-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d5024-362">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="d5024-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d5024-363">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d5024-364">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="d5024-365">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="d5024-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="d5024-366">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d5024-367">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d5024-368">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="d5024-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d5024-369">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="d5024-369">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="d5024-371">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="d5024-372">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="d5024-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d5024-373">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d5024-374">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="d5024-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d5024-375">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="d5024-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d5024-376">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="d5024-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="d5024-377">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="d5024-378">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="d5024-378">Prevent over-posting</span></span>

<span data-ttu-id="d5024-379">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="d5024-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="d5024-380">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="d5024-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="d5024-381">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="d5024-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="d5024-382">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="d5024-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="d5024-383">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="d5024-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="d5024-384">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="d5024-384">A DTO may be used to:</span></span>

* <span data-ttu-id="d5024-385">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="d5024-385">Prevent over-posting.</span></span>
* <span data-ttu-id="d5024-386">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="d5024-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="d5024-387">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="d5024-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="d5024-388">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="d5024-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="d5024-389">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="d5024-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="d5024-390">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="d5024-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="d5024-391">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="d5024-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="d5024-392">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="d5024-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="d5024-393">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="d5024-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="d5024-394">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="d5024-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="d5024-395">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="d5024-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="d5024-396">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5024-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="d5024-397">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="d5024-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d5024-398">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="d5024-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d5024-399">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-399">Create a web API project.</span></span>
> * <span data-ttu-id="d5024-400">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d5024-401">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="d5024-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="d5024-402">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="d5024-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="d5024-403">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-403">Call the web API with Postman.</span></span>

<span data-ttu-id="d5024-404">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="d5024-405">Visão geral</span><span class="sxs-lookup"><span data-stu-id="d5024-405">Overview</span></span>

<span data-ttu-id="d5024-406">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="d5024-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d5024-407">API</span><span class="sxs-lookup"><span data-stu-id="d5024-407">API</span></span> | <span data-ttu-id="d5024-408">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5024-408">Description</span></span> | <span data-ttu-id="d5024-409">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="d5024-409">Request body</span></span> | <span data-ttu-id="d5024-410">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="d5024-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="d5024-411">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-411">Get all to-do items</span></span> | <span data-ttu-id="d5024-412">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-412">None</span></span> | <span data-ttu-id="d5024-413">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="d5024-414">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="d5024-414">Get an item by ID</span></span> | <span data-ttu-id="d5024-415">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-415">None</span></span> | <span data-ttu-id="d5024-416">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="d5024-417">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="d5024-417">Add a new item</span></span> | <span data-ttu-id="d5024-418">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-418">To-do item</span></span> | <span data-ttu-id="d5024-419">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="d5024-420">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="d5024-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d5024-421">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-421">To-do item</span></span> | <span data-ttu-id="d5024-422">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-422">None</span></span> |
|<span data-ttu-id="d5024-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d5024-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="d5024-424">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d5024-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d5024-425">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-425">None</span></span> | <span data-ttu-id="d5024-426">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-426">None</span></span>|

<span data-ttu-id="d5024-427">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-427">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="d5024-433">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d5024-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-436">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="d5024-437">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="d5024-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-439">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="d5024-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d5024-440">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d5024-441">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d5024-442">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="d5024-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="d5024-443">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-443">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d5024-446">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d5024-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d5024-447">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d5024-448">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5024-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="d5024-449">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d5024-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="d5024-450">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="d5024-450">The preceding commands:</span></span>

  * <span data-ttu-id="d5024-451">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d5024-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="d5024-452">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5024-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-453">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5024-454">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="d5024-454">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d5024-456">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="d5024-457">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![seleção de modelo de API do macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="d5024-459">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 3. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="d5024-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="d5024-460">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-460">Select **Next**.</span></span>

* <span data-ttu-id="d5024-461">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="d5024-463">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5024-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="d5024-464">Testar a API</span><span class="sxs-lookup"><span data-stu-id="d5024-464">Test the API</span></span>

<span data-ttu-id="d5024-465">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="d5024-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="d5024-466">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5024-468">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d5024-469">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="d5024-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d5024-470">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d5024-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d5024-471">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d5024-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d5024-473">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d5024-474">Em um navegador, acesse a seguinte URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="d5024-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-475">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d5024-476">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d5024-477">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="d5024-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d5024-478">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="d5024-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d5024-479">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="d5024-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="d5024-480">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="d5024-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="d5024-481">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="d5024-481">Add a model class</span></span>

<span data-ttu-id="d5024-482">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d5024-483">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="d5024-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-485">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d5024-486">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d5024-487">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="d5024-488">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5024-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d5024-489">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d5024-490">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d5024-492">Adicione uma pasta chamada *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d5024-493">Adicione uma `TodoItem` classe à *Models* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-494">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5024-495">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-495">Right-click the project.</span></span> <span data-ttu-id="d5024-496">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d5024-497">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-497">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d5024-499">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar** > **novo arquivo** > **General** > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="d5024-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d5024-500">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d5024-501">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="d5024-502">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="d5024-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d5024-503">As classes de modelo podem ir em qualquer lugar no projeto, mas a *Models* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="d5024-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d5024-504">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5024-504">Add a database context</span></span>

<span data-ttu-id="d5024-505">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d5024-506">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="d5024-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="d5024-508">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="d5024-508">Add NuGet packages</span></span>

* <span data-ttu-id="d5024-509">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="d5024-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="d5024-510">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d5024-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="d5024-511">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="d5024-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="d5024-512">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="d5024-513">Use as instruções anteriores para adicionar o pacote NuGet **Microsoft. EntityFrameworkCore. inmemory** .</span><span class="sxs-lookup"><span data-stu-id="d5024-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="d5024-515">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="d5024-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="d5024-516">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5024-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d5024-517">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5024-518">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d5024-519">Adicione uma `TodoContext` classe à *Models* pasta.</span><span class="sxs-lookup"><span data-stu-id="d5024-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d5024-520">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d5024-521">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5024-521">Register the database context</span></span>

<span data-ttu-id="d5024-522">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d5024-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d5024-523">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="d5024-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="d5024-524">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="d5024-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="d5024-525">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="d5024-525">The preceding code:</span></span>

* <span data-ttu-id="d5024-526">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="d5024-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d5024-527">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="d5024-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d5024-528">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="d5024-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="d5024-529">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="d5024-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-531">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="d5024-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d5024-532">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="d5024-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d5024-533">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="d5024-534">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="d5024-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="d5024-535">Selecione **TodoItem (TodoApi. Models )** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="d5024-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="d5024-536">Selecione **TodoContext (TodoApi. Models )** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="d5024-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="d5024-537">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5024-538">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d5024-539">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5024-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="d5024-540">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="d5024-540">The preceding commands:</span></span>

* <span data-ttu-id="d5024-541">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d5024-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="d5024-542">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="d5024-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="d5024-543">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="d5024-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="d5024-544">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="d5024-544">The generated code:</span></span>

* <span data-ttu-id="d5024-545">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="d5024-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="d5024-546">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d5024-547">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="d5024-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d5024-548">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d5024-549">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="d5024-550">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="d5024-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="d5024-551">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="d5024-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="d5024-552">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="d5024-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="d5024-553">Quando o `[action]` token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="d5024-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="d5024-554">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="d5024-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="d5024-555">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="d5024-556">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="d5024-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="d5024-557">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="d5024-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="d5024-558">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5024-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d5024-559">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d5024-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d5024-560">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="d5024-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="d5024-561">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="d5024-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="d5024-562">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="d5024-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d5024-563">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="d5024-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="d5024-564">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="d5024-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="d5024-565">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="d5024-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d5024-566">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="d5024-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d5024-567">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="d5024-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="d5024-568">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="d5024-568">Install Postman</span></span>

<span data-ttu-id="d5024-569">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d5024-570">Instalar o [postmaster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="d5024-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="d5024-571">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-571">Start the web app.</span></span>
* <span data-ttu-id="d5024-572">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-572">Start Postman.</span></span>
* <span data-ttu-id="d5024-573">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="d5024-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="d5024-574">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="d5024-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="d5024-575">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="d5024-576">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="d5024-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="d5024-577">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d5024-577">Create a new request.</span></span>
* <span data-ttu-id="d5024-578">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="d5024-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d5024-579">Defina o URI como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="d5024-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="d5024-580">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="d5024-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="d5024-581">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="d5024-582">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="d5024-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d5024-583">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="d5024-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d5024-584">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="d5024-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d5024-585">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-585">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="d5024-587">Testar o URI do cabeçalho do local com o postmaster</span><span class="sxs-lookup"><span data-stu-id="d5024-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="d5024-588">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d5024-589">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="d5024-589">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="d5024-591">Defina o método HTTP como `GET`.</span><span class="sxs-lookup"><span data-stu-id="d5024-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="d5024-592">Defina o URI como `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="d5024-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="d5024-593">Por exemplo, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="d5024-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="d5024-594">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="d5024-595">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="d5024-595">Examine the GET methods</span></span>

<span data-ttu-id="d5024-596">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="d5024-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="d5024-597">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="d5024-598">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d5024-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="d5024-599">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="d5024-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="d5024-600">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="d5024-600">Test Get with Postman</span></span>

* <span data-ttu-id="d5024-601">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d5024-601">Create a new request.</span></span>
* <span data-ttu-id="d5024-602">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="d5024-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="d5024-603">Defina o URI de solicitação como `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="d5024-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="d5024-604">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="d5024-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="d5024-605">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d5024-606">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-606">Select **Send**.</span></span>

<span data-ttu-id="d5024-607">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="d5024-607">This app uses an in-memory database.</span></span> <span data-ttu-id="d5024-608">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="d5024-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="d5024-609">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="d5024-610">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="d5024-610">Routing and URL paths</span></span>

<span data-ttu-id="d5024-611">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="d5024-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d5024-612">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d5024-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d5024-613">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="d5024-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="d5024-614">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="d5024-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d5024-615">Para esta amostra, o nome da classe do controlador é **TodoItems** Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="d5024-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="d5024-616">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d5024-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d5024-617">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="d5024-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d5024-618">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="d5024-618">This sample doesn't use a template.</span></span> <span data-ttu-id="d5024-619">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d5024-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d5024-620">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="d5024-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d5024-621">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método em seu `id` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d5024-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d5024-622">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="d5024-622">Return values</span></span> 

<span data-ttu-id="d5024-623">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="d5024-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d5024-624">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="d5024-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d5024-625">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="d5024-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d5024-626">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="d5024-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d5024-627">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5024-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d5024-628">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="d5024-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d5024-629">Se nenhum item corresponder à ID solicitada, o método retornará um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> código de erro 404.</span><span class="sxs-lookup"><span data-stu-id="d5024-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="d5024-630">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="d5024-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d5024-631">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="d5024-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="d5024-632">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-632">The PutTodoItem method</span></span>

<span data-ttu-id="d5024-633">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="d5024-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="d5024-634">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d5024-635">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d5024-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d5024-636">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="d5024-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d5024-637">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="d5024-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d5024-638">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d5024-639">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="d5024-640">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="d5024-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="d5024-641">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d5024-642">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d5024-643">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como "alimentar feed":</span><span class="sxs-lookup"><span data-stu-id="d5024-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d5024-644">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="d5024-644">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="d5024-646">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="d5024-647">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="d5024-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d5024-648">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="d5024-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d5024-649">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="d5024-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d5024-650">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="d5024-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d5024-651">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="d5024-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="d5024-652">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="d5024-653">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="d5024-653">Prevent over-posting</span></span>

<span data-ttu-id="d5024-654">Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="d5024-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="d5024-655">Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="d5024-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="d5024-656">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="d5024-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="d5024-657">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="d5024-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="d5024-658">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="d5024-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="d5024-659">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="d5024-659">A DTO may be used to:</span></span>

* <span data-ttu-id="d5024-660">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="d5024-660">Prevent over-posting.</span></span>
* <span data-ttu-id="d5024-661">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="d5024-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="d5024-662">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="d5024-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="d5024-663">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="d5024-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="d5024-664">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="d5024-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="d5024-665">Para demonstrar a abordagem do DTO, atualize a `TodoItem` classe para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="d5024-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="d5024-666">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="d5024-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="d5024-667">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="d5024-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="d5024-668">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="d5024-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="d5024-669">Atualize o `TodoItemsController` para usar `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="d5024-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="d5024-670">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="d5024-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="d5024-671">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5024-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="d5024-672">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="d5024-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5024-673">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="d5024-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d5024-674">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-674">Create a web API project.</span></span>
> * <span data-ttu-id="d5024-675">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d5024-676">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-676">Add a controller.</span></span>
> * <span data-ttu-id="d5024-677">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="d5024-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="d5024-678">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="d5024-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="d5024-679">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="d5024-679">Specify return values.</span></span>
> * <span data-ttu-id="d5024-680">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="d5024-681">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5024-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="d5024-682">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="d5024-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="d5024-683">Visão geral 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-683">Overview 2.1</span></span>

<span data-ttu-id="d5024-684">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="d5024-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d5024-685">API</span><span class="sxs-lookup"><span data-stu-id="d5024-685">API</span></span> | <span data-ttu-id="d5024-686">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5024-686">Description</span></span> | <span data-ttu-id="d5024-687">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="d5024-687">Request body</span></span> | <span data-ttu-id="d5024-688">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="d5024-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="d5024-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d5024-689">GET /api/TodoItems</span></span> | <span data-ttu-id="d5024-690">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-690">Get all to-do items</span></span> | <span data-ttu-id="d5024-691">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-691">None</span></span> | <span data-ttu-id="d5024-692">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-692">Array of to-do items</span></span>|
|<span data-ttu-id="d5024-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d5024-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="d5024-694">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="d5024-694">Get an item by ID</span></span> | <span data-ttu-id="d5024-695">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-695">None</span></span> | <span data-ttu-id="d5024-696">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-696">To-do item</span></span>|
|<span data-ttu-id="d5024-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d5024-697">POST /api/TodoItems</span></span> | <span data-ttu-id="d5024-698">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="d5024-698">Add a new item</span></span> | <span data-ttu-id="d5024-699">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-699">To-do item</span></span> | <span data-ttu-id="d5024-700">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-700">To-do item</span></span> |
|<span data-ttu-id="d5024-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d5024-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="d5024-702">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="d5024-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d5024-703">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d5024-703">To-do item</span></span> | <span data-ttu-id="d5024-704">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-704">None</span></span> |
|<span data-ttu-id="d5024-705">EXCLUIR/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d5024-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="d5024-706">Excluir um item &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d5024-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d5024-707">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-707">None</span></span> | <span data-ttu-id="d5024-708">Nenhum</span><span class="sxs-lookup"><span data-stu-id="d5024-708">None</span></span>|

<span data-ttu-id="d5024-709">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-709">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="d5024-715">Pré-requisitos 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-718">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="d5024-719">Criar um projeto Web 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-721">No menu **arquivo** , selecione **novo** > **projeto**.</span><span class="sxs-lookup"><span data-stu-id="d5024-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d5024-722">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d5024-723">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d5024-724">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="d5024-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="d5024-725">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="d5024-726">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="d5024-726">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d5024-729">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d5024-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d5024-730">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d5024-731">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5024-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="d5024-732">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="d5024-733">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d5024-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-734">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5024-735">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="d5024-735">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d5024-737">Em Visual Studio para Mac anteriores à versão 8,6, selecione API de aplicativo do **.NET Core**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="d5024-738">Na versão 8,6 ou posterior, selecione **Web e API de aplicativo de console**  >  **App**  >  **API**  >  **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="d5024-739">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core** , selecione a **estrutura de destino**.NET Core 2. x mais recente.</span><span class="sxs-lookup"><span data-stu-id="d5024-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="d5024-740">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-740">Select **Next**.</span></span>

* <span data-ttu-id="d5024-741">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="d5024-743">Testar a API 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-743">Test the API 2.1</span></span>

<span data-ttu-id="d5024-744">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="d5024-744">The project template creates a `values` API.</span></span> <span data-ttu-id="d5024-745">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5024-747">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d5024-748">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="d5024-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d5024-749">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d5024-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d5024-750">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="d5024-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d5024-752">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d5024-753">Em um navegador, acesse a seguinte URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="d5024-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-754">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d5024-755">Selecione **executar**  >  **Iniciar Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d5024-756">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="d5024-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d5024-757">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="d5024-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d5024-758">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="d5024-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="d5024-759">O JSON a seguir será retornado:</span><span class="sxs-lookup"><span data-stu-id="d5024-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="d5024-760">Adicionar uma classe de modelo 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-760">Add a model class 2.1</span></span>

<span data-ttu-id="d5024-761">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5024-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d5024-762">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="d5024-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-764">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d5024-765">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d5024-766">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="d5024-767">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5024-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d5024-768">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d5024-769">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5024-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5024-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d5024-771">Adicione uma pasta chamada *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d5024-772">Adicione uma `TodoItem` classe à *Models* pasta com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5024-773">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5024-774">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-774">Right-click the project.</span></span> <span data-ttu-id="d5024-775">Selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d5024-776">Nomeie a pasta *Models* .</span><span class="sxs-lookup"><span data-stu-id="d5024-776">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d5024-778">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar** > **novo arquivo** > **General** > **classe vazia** geral.</span><span class="sxs-lookup"><span data-stu-id="d5024-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d5024-779">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d5024-780">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="d5024-781">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="d5024-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d5024-782">As classes de modelo podem ir em qualquer lugar no projeto, mas a *Models* pasta é usada pela Convenção.</span><span class="sxs-lookup"><span data-stu-id="d5024-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="d5024-783">Adicionar um contexto de banco de dados 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-783">Add a database context 2.1</span></span>

<span data-ttu-id="d5024-784">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d5024-785">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="d5024-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-787">Clique com o botão direito do mouse na *Models* pasta e selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5024-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d5024-788">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5024-789">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d5024-790">Adicione uma `TodoContext` classe à *Models* pasta.</span><span class="sxs-lookup"><span data-stu-id="d5024-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d5024-791">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="d5024-792">Registrar o contexto de banco de dados 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-792">Register the database context 2.1</span></span>

<span data-ttu-id="d5024-793">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d5024-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d5024-794">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="d5024-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="d5024-795">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="d5024-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="d5024-796">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="d5024-796">The preceding code:</span></span>

* <span data-ttu-id="d5024-797">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="d5024-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d5024-798">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="d5024-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d5024-799">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="d5024-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="d5024-800">Adicionar um controlador 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-802">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="d5024-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d5024-803">Selecione **Adicionar** > **novo item**.</span><span class="sxs-lookup"><span data-stu-id="d5024-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="d5024-804">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="d5024-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="d5024-805">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5024-807">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d5024-808">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="d5024-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="d5024-809">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5024-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="d5024-810">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="d5024-810">The preceding code:</span></span>

* <span data-ttu-id="d5024-811">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="d5024-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="d5024-812">Marca a classe com o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="d5024-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="d5024-813">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d5024-814">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="d5024-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d5024-815">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d5024-816">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="d5024-817">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="d5024-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="d5024-818">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5024-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="d5024-819">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="d5024-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="d5024-820">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="d5024-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="d5024-821">Adicionar os métodos get 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-821">Add Get methods 2.1</span></span>

<span data-ttu-id="d5024-822">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="d5024-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="d5024-823">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="d5024-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="d5024-824">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="d5024-824">Stop the app if it's still running.</span></span> <span data-ttu-id="d5024-825">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="d5024-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="d5024-826">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="d5024-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="d5024-827">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d5024-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="d5024-828">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="d5024-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="d5024-829">Caminhos de roteamento e URL 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="d5024-830">O [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="d5024-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d5024-831">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d5024-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d5024-832">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="d5024-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="d5024-833">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="d5024-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d5024-834">Para esta amostra, o nome da classe do controlador é **Todo** Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="d5024-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="d5024-835">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="d5024-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d5024-836">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="d5024-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d5024-837">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="d5024-837">This sample doesn't use a template.</span></span> <span data-ttu-id="d5024-838">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d5024-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d5024-839">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="d5024-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d5024-840">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="d5024-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="d5024-841">Valores de retorno 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-841">Return values 2.1</span></span>

<span data-ttu-id="d5024-842">O tipo de retorno dos `GetTodoItems` `GetTodoItem` métodos e é [ActionResult \<T> tipo](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="d5024-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d5024-843">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="d5024-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d5024-844">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="d5024-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d5024-845">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="d5024-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d5024-846">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5024-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d5024-847">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="d5024-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d5024-848">Se nenhum item corresponder à ID solicitada, o método retornará um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> código de erro 404.</span><span class="sxs-lookup"><span data-stu-id="d5024-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="d5024-849">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="d5024-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d5024-850">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="d5024-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="d5024-851">Testar o método GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="d5024-852">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d5024-853">Instalar o [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="d5024-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="d5024-854">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-854">Start the web app.</span></span>
* <span data-ttu-id="d5024-855">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-855">Start Postman.</span></span>
* <span data-ttu-id="d5024-856">Desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="d5024-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5024-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5024-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5024-858">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="d5024-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5024-859">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5024-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d5024-860">De **Postman**  >  **preferências** do postmaster (guia **geral** ), desabilite a **verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="d5024-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="d5024-861">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="d5024-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="d5024-862">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="d5024-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="d5024-863">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d5024-863">Create a new request.</span></span>
  * <span data-ttu-id="d5024-864">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="d5024-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="d5024-865">Defina o URI de solicitação como `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="d5024-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="d5024-866">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="d5024-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="d5024-867">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="d5024-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d5024-868">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-868">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="d5024-870">Adicionar um método Create 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-870">Add a Create method 2.1</span></span>

<span data-ttu-id="d5024-871">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="d5024-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="d5024-872">O código anterior é um método HTTP POST, conforme indicado pelo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="d5024-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="d5024-873">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5024-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d5024-874">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="d5024-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="d5024-875">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="d5024-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="d5024-876">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="d5024-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d5024-877">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="d5024-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="d5024-878">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="d5024-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="d5024-879">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="d5024-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d5024-880">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="d5024-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d5024-881">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="d5024-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="d5024-882">Testar o método PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="d5024-883">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-883">Build the project.</span></span>
* <span data-ttu-id="d5024-884">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="d5024-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d5024-885">Defina o URI como `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="d5024-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="d5024-886">Por exemplo, `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="d5024-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="d5024-887">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="d5024-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="d5024-888">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="d5024-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d5024-889">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="d5024-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d5024-890">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="d5024-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d5024-891">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-891">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="d5024-893">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="d5024-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="d5024-894">Testar o URI do cabeçalho do local 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="d5024-895">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="d5024-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d5024-896">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="d5024-896">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="d5024-898">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="d5024-898">Set the method to GET.</span></span>
* <span data-ttu-id="d5024-899">Defina o URI como `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="d5024-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="d5024-900">Por exemplo, `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="d5024-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="d5024-901">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="d5024-902">Adicionar um método PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="d5024-903">Em seguida, adicione o método `PutTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5024-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="d5024-904">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d5024-905">A resposta é [204 (sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d5024-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d5024-906">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="d5024-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d5024-907">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="d5024-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d5024-908">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5024-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="d5024-909">Testar o método PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="d5024-910">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="d5024-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="d5024-911">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d5024-912">Chame GET para garantir que há um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="d5024-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d5024-913">Atualize o item de tarefas que tem a ID = 1 e defina seu nome como "alimentar feed":</span><span class="sxs-lookup"><span data-stu-id="d5024-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d5024-914">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="d5024-914">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="d5024-916">Adicionar um método DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="d5024-917">Em seguida, adicione o método `DeleteTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5024-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="d5024-918">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d5024-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="d5024-919">Testar o método DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="d5024-920">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="d5024-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d5024-921">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="d5024-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d5024-922">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="d5024-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="d5024-923">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="d5024-923">Select **Send**.</span></span>

<span data-ttu-id="d5024-924">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="d5024-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="d5024-925">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="d5024-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="d5024-926">Chamar a API Web com JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="d5024-927">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="d5024-928">o jQuery inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="d5024-928">jQuery initiates the request.</span></span> <span data-ttu-id="d5024-929">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="d5024-930">Configurar o aplicativo para [servir arquivos estáticos](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) e [habilitar o mapeamento de arquivo padrão](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5024-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="d5024-931">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="d5024-932">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d5024-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="d5024-933">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="d5024-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="d5024-934">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d5024-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="d5024-935">Substitua o conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5024-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="d5024-936">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="d5024-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="d5024-937">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d5024-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="d5024-938">Remova a `launchUrl` propriedade para forçar o aplicativo a abrir em *index.html* &mdash; o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="d5024-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="d5024-939">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="d5024-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="d5024-940">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="d5024-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="d5024-941">Obter uma lista de itens pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="d5024-942">o jQuery envia uma solicitação HTTP GET para a API da Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="d5024-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="d5024-943">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="d5024-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="d5024-944">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="d5024-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="d5024-945">Adicionar um item pendente 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="d5024-946">o jQuery envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d5024-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="d5024-947">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="d5024-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="d5024-948">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="d5024-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="d5024-949">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="d5024-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="d5024-950">Atualizar um item de tarefas pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="d5024-951">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="d5024-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="d5024-952">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="d5024-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="d5024-953">Excluir um item de tarefas pendentes 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="d5024-954">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="d5024-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="d5024-955">Adicionar suporte de autenticação a uma API Web 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="d5024-956">Recursos adicionais 2,1</span><span class="sxs-lookup"><span data-stu-id="d5024-956">Additional resources 2.1</span></span>

<span data-ttu-id="d5024-957">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="d5024-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="d5024-958">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="d5024-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="d5024-959">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="d5024-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="d5024-960">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="d5024-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
