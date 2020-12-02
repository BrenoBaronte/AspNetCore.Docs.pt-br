---
title: APIs JSON básicas com Route-to-code no ASP.NET Core
author: jamesnk
description: Saiba como usar os Route-to-code métodos de extensão JSON e para criar APIs da Web JSON leves.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 1f5f532053f8f5ca7f73df8c1a910a484e2488d9
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513090"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="26f81-103">APIs JSON básicas com Route-to-code no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26f81-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="26f81-104">Por [James Newton – King](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="26f81-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="26f81-105">O ASP.NET Core dá suporte a várias maneiras de criar APIs Web JSON:</span><span class="sxs-lookup"><span data-stu-id="26f81-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="26f81-106">[ASP.NET Core API Web](xref:web-api/index) fornece uma estrutura completa para a criação de APIs.</span><span class="sxs-lookup"><span data-stu-id="26f81-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="26f81-107">Um serviço é criado pela herança de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="26f81-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="26f81-108">Alguns recursos fornecidos pela estrutura incluem Associação de modelo, validação, negociação de conteúdo, formatação de entrada e saída e OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="26f81-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="26f81-109">Route-to-code é uma alternativa não estruturada para ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="26f81-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="26f81-110">Route-to-code conecta [ASP.NET Core roteamento](xref:fundamentals/routing) diretamente ao seu código.</span><span class="sxs-lookup"><span data-stu-id="26f81-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="26f81-111">Seu código lê a partir da solicitação e grava a resposta.</span><span class="sxs-lookup"><span data-stu-id="26f81-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="26f81-112">Route-to-code Não tem recursos avançados da API Web, mas também não há nenhuma configuração necessária para usá-la.</span><span class="sxs-lookup"><span data-stu-id="26f81-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="26f81-113">Route-to-code é uma boa abordagem ao criar APIs Web JSON pequenas e básicas.</span><span class="sxs-lookup"><span data-stu-id="26f81-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="26f81-114">Criar APIs Web JSON</span><span class="sxs-lookup"><span data-stu-id="26f81-114">Create JSON web APIs</span></span>

<span data-ttu-id="26f81-115">ASP.NET Core fornece métodos auxiliares que facilitam a criação de APIs Web JSON:</span><span class="sxs-lookup"><span data-stu-id="26f81-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="26f81-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> verifica o `Content-Type` cabeçalho de um tipo de conteúdo JSON.</span><span class="sxs-lookup"><span data-stu-id="26f81-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="26f81-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> lê o JSON da solicitação e o desserializa para o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="26f81-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="26f81-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> grava o valor especificado como JSON no corpo da resposta e define o tipo de conteúdo da resposta como `application/json` .</span><span class="sxs-lookup"><span data-stu-id="26f81-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="26f81-119">As APIs de JSON leves baseadas em rota são especificadas em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="26f81-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="26f81-120">A rota e a lógica da API são configuradas <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> como parte do pipeline de solicitação de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26f81-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="26f81-121">Gravar resposta JSON</span><span class="sxs-lookup"><span data-stu-id="26f81-121">Write JSON response</span></span>

<span data-ttu-id="26f81-122">Considere o seguinte código que configura uma API JSON para um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="26f81-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="26f81-123">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="26f81-123">The preceding code:</span></span>

* <span data-ttu-id="26f81-124">Adiciona um ponto de extremidade de API HTTP GET ao `/hello/{name:alpha}` como o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="26f81-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="26f81-125">Quando a rota for correspondida, a API lerá o `name` valor de rota da solicitação.</span><span class="sxs-lookup"><span data-stu-id="26f81-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="26f81-126">Grava um tipo anônimo como uma resposta JSON com `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="26f81-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="26f81-127">Ler solicitação JSON</span><span class="sxs-lookup"><span data-stu-id="26f81-127">Read JSON request</span></span>

<span data-ttu-id="26f81-128">`HasJsonContentType` e `ReadFromJsonAsync` pode ser usado para desserializar uma resposta JSON em uma API JSON baseada em rota:</span><span class="sxs-lookup"><span data-stu-id="26f81-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="26f81-129">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="26f81-129">The preceding code:</span></span>

* <span data-ttu-id="26f81-130">Adiciona um ponto de extremidade de API HTTP POST ao `/weather` como o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="26f81-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="26f81-131">Quando a rota for correspondida, `HasJsonContentType` o validará o tipo de conteúdo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="26f81-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="26f81-132">Um tipo de conteúdo não JSON retorna um código de status 415.</span><span class="sxs-lookup"><span data-stu-id="26f81-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="26f81-133">Se o tipo de conteúdo for JSON, o conteúdo da solicitação será desserializado pelo `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="26f81-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="26f81-134">Configurar a serialização JSON</span><span class="sxs-lookup"><span data-stu-id="26f81-134">Configure JSON serialization</span></span>

<span data-ttu-id="26f81-135">Há duas maneiras de personalizar a serialização JSON:</span><span class="sxs-lookup"><span data-stu-id="26f81-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="26f81-136">As opções de serialização padrão podem ser configuradas com `JsonOptions` o no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="26f81-136">Default serialization options can be configured with `JsonOptions` in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="26f81-137">`WriteAsJsonAsync` e `ReadFromJsonAsync` têm sobrecargas que aceitam um `JsonSerializerOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="26f81-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a `JsonSerializerOptions` object.</span></span> <span data-ttu-id="26f81-138">Esse `JsonSerializerOptions` objeto substitui as opções padrão.</span><span class="sxs-lookup"><span data-stu-id="26f81-138">This `JsonSerializerOptions` object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="26f81-139">Autenticação e autorização</span><span class="sxs-lookup"><span data-stu-id="26f81-139">Authentication and authorization</span></span>

<span data-ttu-id="26f81-140">Route-to-code dá suporte à autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="26f81-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="26f81-141">Atributos, como `[Authorize]` e `[AllowAnonymous]` , não podem ser colocados em pontos de extremidade que são mapeados para um delegado de solicitação.</span><span class="sxs-lookup"><span data-stu-id="26f81-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="26f81-142">Em vez disso, os metadados de autorização são adicionados usando os `RequireAuthorization` `AllowAnonymous` métodos de extensão e.</span><span class="sxs-lookup"><span data-stu-id="26f81-142">Instead, authorization metadata is added using the `RequireAuthorization` and `AllowAnonymous` extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="26f81-143">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="26f81-143">Dependency injection</span></span>

<span data-ttu-id="26f81-144">[Injeção de dependência (di)](xref:fundamentals/dependency-injection) usando um construtor não é possível com Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="26f81-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="26f81-145">A API da Web cria um controlador para você com os serviços injetados no construtor.</span><span class="sxs-lookup"><span data-stu-id="26f81-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="26f81-146">Um tipo não é criado quando um ponto de extremidade é executado, de modo que os serviços devem ser resolvidos manualmente.</span><span class="sxs-lookup"><span data-stu-id="26f81-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="26f81-147">As APIs baseadas em rota podem usar o <xref:System.IServiceProvider> para resolver serviços:</span><span class="sxs-lookup"><span data-stu-id="26f81-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="26f81-148">Serviços de tempo de vida transitórios e com escopo definido, como `DbContext` , devem ser resolvidos de [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) dentro do delegado de solicitação de um ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="26f81-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="26f81-149">Os serviços de vida útil singleton, como `ILogger` , podem ser resolvidos em [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span><span class="sxs-lookup"><span data-stu-id="26f81-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="26f81-150">Os serviços podem ser resolvidos fora dos delegados de solicitação e compartilhados entre os pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="26f81-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="26f81-151">As APIs que usam a DI extensivamente devem considerar o uso de um tipo de aplicativo ASP.NET Core que dá suporte a DI.</span><span class="sxs-lookup"><span data-stu-id="26f81-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="26f81-152">Por exemplo, ASP.NET Core API da Web.</span><span class="sxs-lookup"><span data-stu-id="26f81-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="26f81-153">A injeção de serviço usando um construtor de controlador é mais fácil do que resolver manualmente os serviços.</span><span class="sxs-lookup"><span data-stu-id="26f81-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="26f81-154">Estrutura do projeto de API</span><span class="sxs-lookup"><span data-stu-id="26f81-154">API project structure</span></span>

<span data-ttu-id="26f81-155">As APIs baseadas em rota não precisam estar localizadas em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="26f81-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="26f81-156">As APIs podem ser colocadas em outros arquivos e mapeadas na inicialização com o `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="26f81-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="26f81-157">Essa abordagem reduz o tamanho do arquivo de configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="26f81-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="26f81-158">Considere a seguinte `UserApi` classe estática que define um `Map` método.</span><span class="sxs-lookup"><span data-stu-id="26f81-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="26f81-159">O método mapeia as APIs baseadas em rota.</span><span class="sxs-lookup"><span data-stu-id="26f81-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="26f81-160">No `Startup.Configure` método, o `Map` método e os métodos estáticos de outras classes são chamados em `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="26f81-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="26f81-161">Recursos ausentes notáveis em comparação com a API da Web</span><span class="sxs-lookup"><span data-stu-id="26f81-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="26f81-162">Route-to-code o é projetado para as APIs JSON básicas.</span><span class="sxs-lookup"><span data-stu-id="26f81-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="26f81-163">Ele não tem suporte para muitos dos recursos avançados fornecidos pela API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="26f81-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="26f81-164">Recursos não fornecidos pelo Route-to-code incluem:</span><span class="sxs-lookup"><span data-stu-id="26f81-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="26f81-165">Model binding</span><span class="sxs-lookup"><span data-stu-id="26f81-165">Model binding</span></span>
* <span data-ttu-id="26f81-166">Validação de modelo</span><span class="sxs-lookup"><span data-stu-id="26f81-166">Model validation</span></span>
* <span data-ttu-id="26f81-167">OpenAPI/Swagger</span><span class="sxs-lookup"><span data-stu-id="26f81-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="26f81-168">Negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="26f81-168">Content negotiation</span></span>
* <span data-ttu-id="26f81-169">Injeção de dependência de Construtor</span><span class="sxs-lookup"><span data-stu-id="26f81-169">Constructor dependency injection</span></span>
* <span data-ttu-id="26f81-170">`ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))</span><span class="sxs-lookup"><span data-stu-id="26f81-170">`ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))</span></span>

<span data-ttu-id="26f81-171">Considere usar [ASP.NET Core API Web](xref:web-api/index) para criar uma API se ela exigir alguns dos recursos na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="26f81-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26f81-172">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="26f81-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
