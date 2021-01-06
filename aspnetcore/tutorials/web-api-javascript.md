---
title: 'Tutorial: chamar uma API Web do ASP.NET Core com JavaScript'
author: rick-anderson
description: Saiba como chamar uma API Web do ASP.NET Core com o JavaScript.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: c32c5befe0be3b1ad4bd87649d3cc74b0296a134
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94703703"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="f59f4-103">Tutorial: chamar uma API Web do ASP.NET Core com JavaScript</span><span class="sxs-lookup"><span data-stu-id="f59f4-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="f59f4-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f59f4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f59f4-105">Este tutorial mostra como chamar uma API Web do ASP.NET Core com JavaScript, usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="f59f4-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f59f4-106">Para o ASP.NET Core 2.2, confira a versão 2.2 de [Chamar a API Web com o JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="f59f4-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="f59f4-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f59f4-107">Prerequisites</span></span>

* <span data-ttu-id="f59f4-108">[Tutorial completo: criar uma API Web](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="f59f4-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="f59f4-109">Familiaridade com CSS, HTML e JavaScript</span><span class="sxs-lookup"><span data-stu-id="f59f4-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f59f4-110">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="f59f4-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="f59f4-111">Nesta seção, você adicionará uma página HTML que contém formulários para criar e gerenciar itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="f59f4-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="f59f4-112">Manipuladores de eventos são anexados aos elementos na página.</span><span class="sxs-lookup"><span data-stu-id="f59f4-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="f59f4-113">Os manipuladores de eventos resultam em solicitações HTTP para os métodos de ação da API Web.</span><span class="sxs-lookup"><span data-stu-id="f59f4-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="f59f4-114">A função `fetch` da API Fetch inicia cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="f59f4-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="f59f4-115">A `fetch` função retorna um objeto [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , que contém uma resposta http representada como um `Response` objeto.</span><span class="sxs-lookup"><span data-stu-id="f59f4-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="f59f4-116">Um padrão comum é extrair o corpo da resposta JSON invocando a função `json` no objeto `Response`.</span><span class="sxs-lookup"><span data-stu-id="f59f4-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="f59f4-117">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="f59f4-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="f59f4-118">A chamada `fetch` mais simples aceita um parâmetro único que representa a rota.</span><span class="sxs-lookup"><span data-stu-id="f59f4-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="f59f4-119">Um segundo parâmetro, conhecido como objeto `init`, é opcional.</span><span class="sxs-lookup"><span data-stu-id="f59f4-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="f59f4-120">`init` é usado para configurar a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="f59f4-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="f59f4-121">Configure o aplicativo para [atender a arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="f59f4-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f59f4-122">O seguinte código destacado é necessário no método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f59f4-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="f59f4-123">Crie uma pasta *wwwroot* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="f59f4-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="f59f4-124">Crie uma pasta *js* dentro da pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="f59f4-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="f59f4-125">Adicione um arquivo HTML chamado *index.html* à pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="f59f4-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="f59f4-126">Substitua o conteúdo de *index.html* pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="f59f4-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="f59f4-127">Adicione um arquivo CSS chamado *site. css* à pasta *wwwroot/CSS* .</span><span class="sxs-lookup"><span data-stu-id="f59f4-127">Add a CSS file named *site.css* to the *wwwroot/css* folder.</span></span> <span data-ttu-id="f59f4-128">Substitua o conteúdo de *site. css* pelos seguintes estilos:</span><span class="sxs-lookup"><span data-stu-id="f59f4-128">Replace the contents of *site.css* with the following styles:</span></span>

    [!code-css[](first-web-api/samples/3.0/TodoApi/wwwroot/css/site.css)]

1. <span data-ttu-id="f59f4-129">Adicione um arquivo JavaScript chamado *site.js* à pasta *wwwroot/js* .</span><span class="sxs-lookup"><span data-stu-id="f59f4-129">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="f59f4-130">Substitua o conteúdo de *site.js* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f59f4-130">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="f59f4-131">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="f59f4-131">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="f59f4-132">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f59f4-132">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="f59f4-133">Remova a `launchUrl` propriedade para forçar o aplicativo a abrir em *index.html* &mdash; o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="f59f4-133">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="f59f4-134">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="f59f4-134">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="f59f4-135">A seguir, são apresentadas explicações sobre as solicitações de API Web.</span><span class="sxs-lookup"><span data-stu-id="f59f4-135">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="f59f4-136">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="f59f4-136">Get a list of to-do items</span></span>

<span data-ttu-id="f59f4-137">No código a seguir, uma solicitação HTTP GET é enviada para a rota de *api/TodoItems*:</span><span class="sxs-lookup"><span data-stu-id="f59f4-137">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="f59f4-138">Quando a API Web retorna um código de status bem-sucedido, a função `_displayItems` é invocada.</span><span class="sxs-lookup"><span data-stu-id="f59f4-138">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="f59f4-139">Cada item de tarefas pendentes no parâmetro de matriz aceito por `_displayItems` é adicionado a uma tabela com os botões **Editar** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="f59f4-139">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="f59f4-140">Se a solicitação da API Web falhar, um erro será registrado no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="f59f4-140">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="f59f4-141">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="f59f4-141">Add a to-do item</span></span>

<span data-ttu-id="f59f4-142">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f59f4-142">In the following code:</span></span>

* <span data-ttu-id="f59f4-143">Uma variável `item` é declarada para construir uma representação literal de objeto do item de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="f59f4-143">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="f59f4-144">Uma solicitação Fetch é configurada com as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="f59f4-144">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="f59f4-145">`method`&mdash; especifica o verbo de ação HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="f59f4-145">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="f59f4-146">`body`&mdash; especifica a representação JSON do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="f59f4-146">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="f59f4-147">O JSON é produzido passando o literal de objeto armazenado em `item` para a função [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="f59f4-147">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="f59f4-148">`headers`&mdash; especifica os cabeçalhos de solicitação HTTP `Accept` e `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="f59f4-148">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="f59f4-149">Ambos os cabeçalhos são definidos como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="f59f4-149">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="f59f4-150">Uma solicitação HTTP POST é enviada para a rota de *api/TodoItems*.</span><span class="sxs-lookup"><span data-stu-id="f59f4-150">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="f59f4-151">Quando a API Web retorna um código de status de êxito, a função `getItems` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="f59f4-151">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="f59f4-152">Se a solicitação da API Web falhar, um erro será registrado no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="f59f4-152">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="f59f4-153">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="f59f4-153">Update a to-do item</span></span>

<span data-ttu-id="f59f4-154">A atualização de um item de tarefas pendentes é semelhante à adição de um; no entanto, há duas diferenças significativas:</span><span class="sxs-lookup"><span data-stu-id="f59f4-154">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="f59f4-155">A rota é sufixada com o identificador exclusivo do item a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="f59f4-155">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="f59f4-156">Por exemplo, *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="f59f4-156">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="f59f4-157">O verbo de ação HTTP é PUT, conforme indicado pela opção `method`.</span><span class="sxs-lookup"><span data-stu-id="f59f4-157">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="f59f4-158">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="f59f4-158">Delete a to-do item</span></span>

<span data-ttu-id="f59f4-159">Para excluir um item de tarefas pendentes, defina a opção `method` da solicitação como `DELETE` e especifique o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="f59f4-159">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="f59f4-160">Avance para o próximo tutorial para saber como gerar páginas de ajuda da API Web:</span><span class="sxs-lookup"><span data-stu-id="f59f4-160">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
