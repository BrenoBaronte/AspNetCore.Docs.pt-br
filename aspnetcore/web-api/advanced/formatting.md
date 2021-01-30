---
title: Formatar dados de resposta na API Web ASP.NET Core
author: rick-anderson
description: Saiba como formatar dados de resposta na API Web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 5d228af00ee34e7f8ca60a5085872fdb93842367
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057493"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="0419c-103">Formatar dados de resposta na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0419c-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="0419c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0419c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0419c-105">ASP.NET Core MVC tem suporte para formatar dados de resposta.</span><span class="sxs-lookup"><span data-stu-id="0419c-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="0419c-106">Os dados de resposta podem ser formatados usando formatos específicos ou em resposta ao formato solicitado do cliente.</span><span class="sxs-lookup"><span data-stu-id="0419c-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="0419c-107">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0419c-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="0419c-108">Resultados de ação específica de formato</span><span class="sxs-lookup"><span data-stu-id="0419c-108">Format-specific Action Results</span></span>

<span data-ttu-id="0419c-109">Alguns tipos de resultado de ação são específicos a um formato específico, como <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="0419c-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="0419c-110">As ações podem retornar resultados formatados em um formato específico, independentemente das preferências do cliente.</span><span class="sxs-lookup"><span data-stu-id="0419c-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="0419c-111">Por exemplo, retornando `JsonResult` retorna dados formatados para JSON.</span><span class="sxs-lookup"><span data-stu-id="0419c-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="0419c-112">Retornar `ContentResult` ou uma cadeia de caracteres retorna dados de cadeia de caracteres formatados com texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="0419c-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="0419c-113">Uma ação não é necessária para retornar qualquer tipo específico.</span><span class="sxs-lookup"><span data-stu-id="0419c-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="0419c-114">ASP.NET Core dá suporte a qualquer valor de retorno de objeto.</span><span class="sxs-lookup"><span data-stu-id="0419c-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="0419c-115">Os resultados de ações que retornam objetos que não são <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipos são serializados usando a <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementação apropriada.</span><span class="sxs-lookup"><span data-stu-id="0419c-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="0419c-116">Para obter mais informações, consulte <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="0419c-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="0419c-117">O método auxiliar interno <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> retorna dados formatados em JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="0419c-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="0419c-118">O download de exemplo retorna a lista de autores.</span><span class="sxs-lookup"><span data-stu-id="0419c-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="0419c-119">Usando as ferramentas de desenvolvedor de navegador F12 ou o [postmaster](https://www.getpostman.com/tools) com o código anterior:</span><span class="sxs-lookup"><span data-stu-id="0419c-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="0419c-120">O cabeçalho de resposta que contém o **tipo de conteúdo:** `application/json; charset=utf-8` é exibido.</span><span class="sxs-lookup"><span data-stu-id="0419c-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="0419c-121">Os cabeçalhos de solicitação são exibidos.</span><span class="sxs-lookup"><span data-stu-id="0419c-121">The request headers are displayed.</span></span> <span data-ttu-id="0419c-122">Por exemplo, o `Accept` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="0419c-122">For example, the `Accept` header.</span></span> <span data-ttu-id="0419c-123">O `Accept` cabeçalho é ignorado pelo código anterior.</span><span class="sxs-lookup"><span data-stu-id="0419c-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="0419c-124">Para retornar dados formatados como texto sem formatação, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> e o auxiliar <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>:</span><span class="sxs-lookup"><span data-stu-id="0419c-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="0419c-125">No código anterior, o `Content-Type` retornado é `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="0419c-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="0419c-126">O retorno de uma cadeia de caracteres é entregue `Content-Type` de `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="0419c-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="0419c-127">Para ações com vários tipos de retorno, retorne `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="0419c-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="0419c-128">Por exemplo, retornando códigos de status HTTP diferentes com base no resultado das operações executadas.</span><span class="sxs-lookup"><span data-stu-id="0419c-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="0419c-129">Negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="0419c-129">Content negotiation</span></span>

<span data-ttu-id="0419c-130">A negociação de conteúdo ocorre quando o cliente especifica um [cabeçalho Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="0419c-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="0419c-131">O formato padrão usado pelo ASP.NET Core é [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="0419c-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="0419c-132">A negociação de conteúdo é:</span><span class="sxs-lookup"><span data-stu-id="0419c-132">Content negotiation is:</span></span>

* <span data-ttu-id="0419c-133">Implementado pelo <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="0419c-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="0419c-134">Incorporados aos resultados da ação específica do código de status retornados dos métodos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="0419c-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="0419c-135">Os métodos auxiliares de resultados de ação se baseiam em `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="0419c-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="0419c-136">Quando um tipo de modelo é retornado, o tipo de retorno é `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="0419c-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="0419c-137">O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="0419c-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="0419c-138">Por padrão, o ASP.NET Core dá suporte aos `application/json` `text/json` tipos de mídia, e `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="0419c-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="0419c-139">Ferramentas como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/tools) podem definir o `Accept` cabeçalho da solicitação para especificar o formato de retorno.</span><span class="sxs-lookup"><span data-stu-id="0419c-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="0419c-140">Quando o `Accept` cabeçalho contém um tipo ao qual o servidor dá suporte, esse tipo é retornado.</span><span class="sxs-lookup"><span data-stu-id="0419c-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="0419c-141">A próxima seção mostra como adicionar outros formatadores.</span><span class="sxs-lookup"><span data-stu-id="0419c-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="0419c-142">As ações do controlador podem retornar POCOs (objetos Plain antigos do CLR).</span><span class="sxs-lookup"><span data-stu-id="0419c-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="0419c-143">Quando um POCO é retornado, o tempo de execução cria automaticamente um `ObjectResult` que encapsula o objeto.</span><span class="sxs-lookup"><span data-stu-id="0419c-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="0419c-144">O cliente obtém o objeto serializado formatado.</span><span class="sxs-lookup"><span data-stu-id="0419c-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="0419c-145">Se o objeto que está sendo retornado for `null` , uma `204 No Content` resposta será retornada.</span><span class="sxs-lookup"><span data-stu-id="0419c-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="0419c-146">Retornando um tipo de objeto:</span><span class="sxs-lookup"><span data-stu-id="0419c-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="0419c-147">No código anterior, uma solicitação para um alias de autor válido retorna uma `200 OK` resposta com os dados do autor.</span><span class="sxs-lookup"><span data-stu-id="0419c-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="0419c-148">Uma solicitação para um alias inválido retorna uma `204 No Content` resposta.</span><span class="sxs-lookup"><span data-stu-id="0419c-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="0419c-149">O cabeçalho Accept</span><span class="sxs-lookup"><span data-stu-id="0419c-149">The Accept header</span></span>

<span data-ttu-id="0419c-150">A *negociação* de conteúdo ocorre quando um `Accept` cabeçalho é exibido na solicitação.</span><span class="sxs-lookup"><span data-stu-id="0419c-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="0419c-151">Quando uma solicitação contém um cabeçalho Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0419c-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="0419c-152">Enumera os tipos de mídia no cabeçalho Accept na ordem de preferência.</span><span class="sxs-lookup"><span data-stu-id="0419c-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="0419c-153">Tenta localizar um formatador que pode produzir uma resposta em um dos formatos especificados.</span><span class="sxs-lookup"><span data-stu-id="0419c-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="0419c-154">Se nenhum formatador for encontrado que possa atender à solicitação do cliente, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0419c-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="0419c-155">Retorna `406 Not Acceptable` se <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> é definido como `true` , ou-</span><span class="sxs-lookup"><span data-stu-id="0419c-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> is set to `true`, or -</span></span>
* <span data-ttu-id="0419c-156">Tenta encontrar o primeiro formatador que pode produzir uma resposta.</span><span class="sxs-lookup"><span data-stu-id="0419c-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="0419c-157">Se nenhum formatador estiver configurado para o formato solicitado, o primeiro formatador que pode formatar o objeto será usado.</span><span class="sxs-lookup"><span data-stu-id="0419c-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="0419c-158">Se nenhum `Accept` cabeçalho aparecer na solicitação:</span><span class="sxs-lookup"><span data-stu-id="0419c-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="0419c-159">O primeiro formatador que pode manipular o objeto é usado para serializar a resposta.</span><span class="sxs-lookup"><span data-stu-id="0419c-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="0419c-160">Não há nenhuma negociação ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="0419c-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="0419c-161">O servidor está determinando o formato a ser retornado.</span><span class="sxs-lookup"><span data-stu-id="0419c-161">The server is determining what format to return.</span></span>

<span data-ttu-id="0419c-162">Se o cabeçalho Accept contiver `*/*` , o cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` seja definido como true em <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="0419c-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="0419c-163">Navegadores e negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="0419c-163">Browsers and content negotiation</span></span>

<span data-ttu-id="0419c-164">Diferentemente dos clientes de API típicos, os navegadores da Web fornecem `Accept` cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="0419c-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="0419c-165">O navegador da Web especifica vários formatos, incluindo curingas.</span><span class="sxs-lookup"><span data-stu-id="0419c-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="0419c-166">Por padrão, quando a estrutura detecta que a solicitação é proveniente de um navegador:</span><span class="sxs-lookup"><span data-stu-id="0419c-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="0419c-167">O `Accept` cabeçalho é ignorado.</span><span class="sxs-lookup"><span data-stu-id="0419c-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="0419c-168">O conteúdo é retornado em JSON, a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="0419c-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="0419c-169">Isso fornece uma experiência mais consistente entre navegadores ao consumir APIs.</span><span class="sxs-lookup"><span data-stu-id="0419c-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="0419c-170">Para configurar um aplicativo para honrar os cabeçalhos de aceitação do navegador, defina <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> como `true` :</span><span class="sxs-lookup"><span data-stu-id="0419c-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="0419c-171">Configurar formatadores</span><span class="sxs-lookup"><span data-stu-id="0419c-171">Configure formatters</span></span>

<span data-ttu-id="0419c-172">Os aplicativos que precisam dar suporte a formatos adicionais podem adicionar os pacotes NuGet apropriados e configurar o suporte.</span><span class="sxs-lookup"><span data-stu-id="0419c-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="0419c-173">Há formatadores separados para entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="0419c-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="0419c-174">Os formatadores de entrada são usados pela [Associação de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="0419c-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="0419c-175">Os formatadores de saída são usados para formatar respostas.</span><span class="sxs-lookup"><span data-stu-id="0419c-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="0419c-176">Para obter informações sobre como criar um formatador personalizado, consulte [formatadores personalizados](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="0419c-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="0419c-177">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="0419c-177">Add XML format support</span></span>

<span data-ttu-id="0419c-178">Os formatadores XML implementados usando o <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="0419c-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="0419c-179">O código anterior serializa os resultados usando `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="0419c-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="0419c-180">Ao usar o código anterior, os métodos do controlador retornam o formato apropriado com base no cabeçalho da solicitação `Accept` .</span><span class="sxs-lookup"><span data-stu-id="0419c-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="0419c-181">Configurar System.Text.Jsem formatadores baseados</span><span class="sxs-lookup"><span data-stu-id="0419c-181">Configure System.Text.Json based formatters</span></span>

<span data-ttu-id="0419c-182">Os recursos para os `System.Text.Json` formatadores baseados podem ser configurados usando <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="0419c-182">Features for the `System.Text.Json` based formatters can be configured using <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName>.</span></span> <span data-ttu-id="0419c-183">A formatação padrão é camelCase.</span><span class="sxs-lookup"><span data-stu-id="0419c-183">The default formatting is camelCase.</span></span> <span data-ttu-id="0419c-184">O código realçado a seguir define a formatação PascalCase:</span><span class="sxs-lookup"><span data-stu-id="0419c-184">The following highlighted code sets PascalCase formatting:</span></span>

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="0419c-185">O seguinte método de ação chama [ControllerBase. problema](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) para criar uma <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> resposta:</span><span class="sxs-lookup"><span data-stu-id="0419c-185">The following action method calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response:</span></span>

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

<span data-ttu-id="0419c-186">Com o código anterior:</span><span class="sxs-lookup"><span data-stu-id="0419c-186">With the preceding code:</span></span>

  * <span data-ttu-id="0419c-187">`https://localhost:5001/WeatherForecast/temperature` Retorna PascalCase.</span><span class="sxs-lookup"><span data-stu-id="0419c-187">`https://localhost:5001/WeatherForecast/temperature` returns PascalCase.</span></span>
  * <span data-ttu-id="0419c-188">`https://localhost:5001/WeatherForecast/error` Retorna camelCase.</span><span class="sxs-lookup"><span data-stu-id="0419c-188">`https://localhost:5001/WeatherForecast/error` returns camelCase.</span></span> <span data-ttu-id="0419c-189">A resposta de erro é sempre camelCase, mesmo quando o aplicativo define o formato como PascalCase.</span><span class="sxs-lookup"><span data-stu-id="0419c-189">The error response is always camelCase, even when the app sets the format to PascalCase.</span></span> <span data-ttu-id="0419c-190">`ProblemDetails` segue a [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), que especifica a minúscula</span><span class="sxs-lookup"><span data-stu-id="0419c-190">`ProblemDetails` follows [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), which specifies lower case</span></span>

<span data-ttu-id="0419c-191">O código a seguir define PascalCase e adiciona um conversor personalizado:</span><span class="sxs-lookup"><span data-stu-id="0419c-191">The following code sets PascalCase and adds a custom converter:</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="0419c-192">As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="0419c-192">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="0419c-193">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0419c-193">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="0419c-194">Adicionar suporte ao formato JSON com base em Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="0419c-194">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="0419c-195">Antes do ASP.NET Core 3,0, os formatadores JSON usados por padrão implementaram usando o `Newtonsoft.Json` pacote.</span><span class="sxs-lookup"><span data-stu-id="0419c-195">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="0419c-196">No ASP.NET Core 3.0 ou posterior, os formatadores JSON padrão baseiam-se no `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="0419c-196">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="0419c-197">O suporte para `Newtonsoft.Json` formatadores e recursos baseados está disponível ao instalar o [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pacote NuGet e configurá-lo no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0419c-197">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="0419c-198">No código anterior, a chamada para `AddNewtonsoftJson` configura os seguintes recursos de API Web, MVC e Razor páginas a serem usados `Newtonsoft.Json` :</span><span class="sxs-lookup"><span data-stu-id="0419c-198">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="0419c-199">Formatadores de entrada e saída que lêem e gravam JSON</span><span class="sxs-lookup"><span data-stu-id="0419c-199">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="0419c-200">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="0419c-200">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="0419c-201">TempData</span><span class="sxs-lookup"><span data-stu-id="0419c-201">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="0419c-202">Alguns recursos podem não funcionar bem com `System.Text.Json` formatadores baseados em e exigem uma referência aos `Newtonsoft.Json` formatadores baseados em.</span><span class="sxs-lookup"><span data-stu-id="0419c-202">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="0419c-203">Continue usando os `Newtonsoft.Json` formatadores baseados em, se o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0419c-203">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="0419c-204">Usa `Newtonsoft.Json` atributos.</span><span class="sxs-lookup"><span data-stu-id="0419c-204">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="0419c-205">Por exemplo, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="0419c-205">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="0419c-206">Personaliza as configurações de serialização.</span><span class="sxs-lookup"><span data-stu-id="0419c-206">Customizes the serialization settings.</span></span>
* <span data-ttu-id="0419c-207">O se baseia em recursos que o `Newtonsoft.Json` fornece.</span><span class="sxs-lookup"><span data-stu-id="0419c-207">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="0419c-208">Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="0419c-208">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="0419c-209">Antes do ASP.NET Core 3.0, `JsonResult.SerializerSettings` aceita uma instância de `JsonSerializerSettings` que é específico para `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="0419c-209">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="0419c-210">Gera documentação [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="0419c-210">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="0419c-211">Os recursos para os `Newtonsoft.Json` formatadores baseados em podem ser configurados usando `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="0419c-211">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="0419c-212">As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="0419c-212">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="0419c-213">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0419c-213">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="0419c-214">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="0419c-214">Add XML format support</span></span>

<span data-ttu-id="0419c-215">A formatação XML requer o [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="0419c-215">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="0419c-216">Os formatadores XML implementados usando o <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="0419c-216">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="0419c-217">O código anterior serializa os resultados usando `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="0419c-217">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="0419c-218">Ao usar o código anterior, os métodos do controlador devem retornar o formato apropriado com base no `Accept` cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="0419c-218">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="0419c-219">Especificar um formato</span><span class="sxs-lookup"><span data-stu-id="0419c-219">Specify a format</span></span>

<span data-ttu-id="0419c-220">Para restringir os formatos de resposta, aplique o [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro.</span><span class="sxs-lookup"><span data-stu-id="0419c-220">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="0419c-221">Assim como a maioria dos [filtros](xref:mvc/controllers/filters), `[Produces]` pode ser aplicado na ação, controlador ou escopo global:</span><span class="sxs-lookup"><span data-stu-id="0419c-221">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="0419c-222">O [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro anterior:</span><span class="sxs-lookup"><span data-stu-id="0419c-222">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="0419c-223">Força todas as ações dentro do controlador a retornar respostas formatadas em JSON.</span><span class="sxs-lookup"><span data-stu-id="0419c-223">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="0419c-224">Se outros formatadores estiverem configurados e o cliente especificar um formato diferente, o JSON será retornado.</span><span class="sxs-lookup"><span data-stu-id="0419c-224">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="0419c-225">Para obter mais informações, consulte [filtros](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="0419c-225">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="0419c-226">Formatadores de caso especiais</span><span class="sxs-lookup"><span data-stu-id="0419c-226">Special case formatters</span></span>

<span data-ttu-id="0419c-227">Alguns casos especiais são implementados com formatadores internos.</span><span class="sxs-lookup"><span data-stu-id="0419c-227">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="0419c-228">Por padrão, os `string` tipos de retorno são formatados como *texto/simples* (*texto/HTML* , se solicitado por meio do `Accept` cabeçalho).</span><span class="sxs-lookup"><span data-stu-id="0419c-228">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="0419c-229">Esse comportamento pode ser excluído com a remoção do <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="0419c-229">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="0419c-230">Os formatadores são removidos do `ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="0419c-230">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="0419c-231">Ações que têm um tipo de retorno de objeto de modelo retornam `204 No Content` ao retornar `null` .</span><span class="sxs-lookup"><span data-stu-id="0419c-231">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="0419c-232">Esse comportamento pode ser excluído com a remoção do <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="0419c-232">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="0419c-233">O código a seguir remove o `StringOutputFormatter` e o `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="0419c-233">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="0419c-234">Sem o `StringOutputFormatter` , os formatos de formatadores JSON internos `string` retornam tipos de retorno.</span><span class="sxs-lookup"><span data-stu-id="0419c-234">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="0419c-235">Se o formatador JSON interno for removido e um formatador XML estiver disponível, o formatador XML formatará os `string` tipos de retorno.</span><span class="sxs-lookup"><span data-stu-id="0419c-235">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="0419c-236">Caso contrário, `string` retornarão tipos de retorno `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="0419c-236">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="0419c-237">Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado.</span><span class="sxs-lookup"><span data-stu-id="0419c-237">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="0419c-238">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0419c-238">For example:</span></span>

* <span data-ttu-id="0419c-239">O formatador JSON retorna uma resposta com um corpo de `null` .</span><span class="sxs-lookup"><span data-stu-id="0419c-239">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="0419c-240">O formatador XML retorna um elemento XML vazio com o atributo `xsi:nil="true"` definido.</span><span class="sxs-lookup"><span data-stu-id="0419c-240">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="0419c-241">Mapeamentos de URL de formato de resposta</span><span class="sxs-lookup"><span data-stu-id="0419c-241">Response format URL mappings</span></span>

<span data-ttu-id="0419c-242">Os clientes podem solicitar um formato específico como parte da URL, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0419c-242">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="0419c-243">Na cadeia de caracteres de consulta ou parte do caminho.</span><span class="sxs-lookup"><span data-stu-id="0419c-243">In the query string or part of the path.</span></span>
* <span data-ttu-id="0419c-244">Usando uma extensão de arquivo específica de formato, como. xml ou. JSON.</span><span class="sxs-lookup"><span data-stu-id="0419c-244">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="0419c-245">O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API.</span><span class="sxs-lookup"><span data-stu-id="0419c-245">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="0419c-246">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0419c-246">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="0419c-247">A rota anterior permite que o formato solicitado seja especificado como uma extensão de arquivo opcional.</span><span class="sxs-lookup"><span data-stu-id="0419c-247">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="0419c-248">O [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) atributo verifica a existência do valor de formato no `RouteData` e mapeia o formato de resposta para o formatador apropriado quando a resposta é criada.</span><span class="sxs-lookup"><span data-stu-id="0419c-248">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="0419c-249">Rota</span><span class="sxs-lookup"><span data-stu-id="0419c-249">Route</span></span>        |             <span data-ttu-id="0419c-250">Formatador</span><span class="sxs-lookup"><span data-stu-id="0419c-250">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="0419c-251">O formatador de saída padrão</span><span class="sxs-lookup"><span data-stu-id="0419c-251">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="0419c-252">O formatador JSON (se configurado)</span><span class="sxs-lookup"><span data-stu-id="0419c-252">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="0419c-253">O formatador XML (se configurado)</span><span class="sxs-lookup"><span data-stu-id="0419c-253">The XML formatter (if configured)</span></span>  |
