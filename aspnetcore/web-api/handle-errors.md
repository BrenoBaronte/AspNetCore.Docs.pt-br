---
title: Tratar erros em APIs da Web ASP.NET Core
author: pranavkm
description: Saiba mais sobre o tratamento de erros com as APIs da Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: 92e9350a7892f8f38f64d4ebd68d54a97ec7e994
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058370"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="cd926-103">Tratar erros em APIs da Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd926-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="cd926-104">Este artigo descreve como lidar e personalizar o tratamento de erros com as APIs da Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd926-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="cd926-105">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd926-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="cd926-106">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="cd926-106">Developer Exception Page</span></span>

<span data-ttu-id="cd926-107">A [página de exceção do desenvolvedor](xref:fundamentals/error-handling) é uma ferramenta útil para obter rastreamentos de pilha detalhados para erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="cd926-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="cd926-108">Ele usa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> para capturar exceções síncronas e assíncronas do pipeline http e gerar respostas de erro.</span><span class="sxs-lookup"><span data-stu-id="cd926-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="cd926-109">Para ilustrar, considere a seguinte ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="cd926-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="cd926-110">Execute o seguinte `curl` comando para testar a ação anterior:</span><span class="sxs-lookup"><span data-stu-id="cd926-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd926-111">No ASP.NET Core 3,0 e posterior, a página de exceção do desenvolvedor exibirá uma resposta de texto sem formatação se o cliente não solicitar saída formatada em HTML.</span><span class="sxs-lookup"><span data-stu-id="cd926-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="cd926-112">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="cd926-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="cd926-113">Para exibir uma resposta formatada em HTML, defina o `Accept` cabeçalho da solicitação HTTP para o `text/html` tipo de mídia.</span><span class="sxs-lookup"><span data-stu-id="cd926-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="cd926-114">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd926-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="cd926-115">Considere o seguinte trecho da resposta HTTP:</span><span class="sxs-lookup"><span data-stu-id="cd926-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="cd926-116">No ASP.NET Core 2,2 e anteriores, a página de exceção do desenvolvedor exibe uma resposta formatada em HTML.</span><span class="sxs-lookup"><span data-stu-id="cd926-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="cd926-117">Por exemplo, considere o seguinte trecho da resposta HTTP:</span><span class="sxs-lookup"><span data-stu-id="cd926-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd926-118">A resposta formatada em HTML se torna útil durante o teste por meio de ferramentas como o postmaster.</span><span class="sxs-lookup"><span data-stu-id="cd926-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="cd926-119">A captura de tela a seguir mostra o texto sem formatação e as respostas formatadas em HTML no postmaster:</span><span class="sxs-lookup"><span data-stu-id="cd926-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Teste de página de exceção do desenvolvedor no postmaster](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="cd926-121">Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="cd926-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="cd926-122">Não compartilhe informações de exceção detalhadas publicamente quando o aplicativo for executado em produção.</span><span class="sxs-lookup"><span data-stu-id="cd926-122">Don't share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="cd926-123">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="cd926-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>
>
> <span data-ttu-id="cd926-124">Não marque o método de ação do manipulador de erros com atributos do método HTTP, como `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="cd926-124">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="cd926-125">Os verbos explícitos impedem que algumas solicitações atinjam o método de ação.</span><span class="sxs-lookup"><span data-stu-id="cd926-125">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="cd926-126">Permitir acesso anônimo ao método se os usuários não autenticados tiverem que ver o erro.</span><span class="sxs-lookup"><span data-stu-id="cd926-126">Allow anonymous access to the method if unauthenticated users should see the error.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="cd926-127">Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="cd926-127">Exception handler</span></span>

<span data-ttu-id="cd926-128">Em ambientes que não são de desenvolvimento, o [middleware de manipulação de exceção](xref:fundamentals/error-handling) pode ser usado para produzir uma carga de erro:</span><span class="sxs-lookup"><span data-stu-id="cd926-128">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="cd926-129">No `Startup.Configure` , invoque <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> para usar o middleware:</span><span class="sxs-lookup"><span data-stu-id="cd926-129">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="cd926-130">Configure uma ação do controlador para responder à `/error` rota:</span><span class="sxs-lookup"><span data-stu-id="cd926-130">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="cd926-131">A `Error` ação anterior envia uma carga compatível com [RFC 7807](https://tools.ietf.org/html/rfc7807)para o cliente.</span><span class="sxs-lookup"><span data-stu-id="cd926-131">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="cd926-132">O middleware de manipulação de exceção também pode fornecer uma saída de negociação de conteúdo mais detalhada no ambiente de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="cd926-132">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="cd926-133">Use as etapas a seguir para produzir um formato de carga consistente entre ambientes de desenvolvimento e produção:</span><span class="sxs-lookup"><span data-stu-id="cd926-133">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="cd926-134">No `Startup.Configure` , registre as instâncias de middleware de manipulação de exceção específicas do ambiente:</span><span class="sxs-lookup"><span data-stu-id="cd926-134">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="cd926-135">No código anterior, o middleware é registrado com:</span><span class="sxs-lookup"><span data-stu-id="cd926-135">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="cd926-136">Uma rota do `/error-local-development` no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cd926-136">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="cd926-137">Uma rota do `/error` em ambientes que não são de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cd926-137">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="cd926-138">Aplicar roteamento de atributo a ações do controlador:</span><span class="sxs-lookup"><span data-stu-id="cd926-138">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    <span data-ttu-id="cd926-139">O código anterior chama [ControllerBase. problema](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) para criar uma <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> resposta.</span><span class="sxs-lookup"><span data-stu-id="cd926-139">The preceding code calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response.</span></span>

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="cd926-140">Usar exceções para modificar a resposta</span><span class="sxs-lookup"><span data-stu-id="cd926-140">Use exceptions to modify the response</span></span>

<span data-ttu-id="cd926-141">O conteúdo da resposta pode ser modificado de fora do controlador.</span><span class="sxs-lookup"><span data-stu-id="cd926-141">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="cd926-142">Na API Web do ASP.NET 4. x, uma maneira de fazer isso era usando o <xref:System.Web.Http.HttpResponseException> tipo.</span><span class="sxs-lookup"><span data-stu-id="cd926-142">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="cd926-143">ASP.NET Core não inclui um tipo equivalente.</span><span class="sxs-lookup"><span data-stu-id="cd926-143">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="cd926-144">O suporte para `HttpResponseException` pode ser adicionado com as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="cd926-144">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="cd926-145">Crie um tipo de exceção conhecido chamado `HttpResponseException` :</span><span class="sxs-lookup"><span data-stu-id="cd926-145">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="cd926-146">Crie um filtro de ação chamado `HttpResponseExceptionFilter` :</span><span class="sxs-lookup"><span data-stu-id="cd926-146">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="cd926-147">O filtro anterior especifica um `Order` do valor inteiro máximo menos 10.</span><span class="sxs-lookup"><span data-stu-id="cd926-147">The preceding filter specifies an `Order` of the maximum integer value minus 10.</span></span> <span data-ttu-id="cd926-148">Isso permite que outros filtros sejam executados no final do pipeline.</span><span class="sxs-lookup"><span data-stu-id="cd926-148">This allows other filters to run at the end of the pipeline.</span></span>

1. <span data-ttu-id="cd926-149">No `Startup.ConfigureServices` , adicione o filtro de ação à coleção de filtros:</span><span class="sxs-lookup"><span data-stu-id="cd926-149">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="cd926-150">Resposta de erro de falha na validação</span><span class="sxs-lookup"><span data-stu-id="cd926-150">Validation failure error response</span></span>

<span data-ttu-id="cd926-151">Para controladores de API Web, o MVC responde com um <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> tipo de resposta quando a validação do modelo falha.</span><span class="sxs-lookup"><span data-stu-id="cd926-151">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="cd926-152">O MVC usa os resultados de <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> para construir a resposta de erro para uma falha de validação.</span><span class="sxs-lookup"><span data-stu-id="cd926-152">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="cd926-153">O exemplo a seguir usa a fábrica para alterar o tipo de resposta padrão para <xref:Microsoft.AspNetCore.Mvc.SerializableError> em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cd926-153">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="cd926-154">Resposta de erro do cliente</span><span class="sxs-lookup"><span data-stu-id="cd926-154">Client error response</span></span>

<span data-ttu-id="cd926-155">Um *resultado de erro* é definido como resultado com um código de status HTTP de 400 ou superior.</span><span class="sxs-lookup"><span data-stu-id="cd926-155">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="cd926-156">Para controladores de API Web, o MVC transforma um resultado de erro em um resultado com <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="cd926-156">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="cd926-157">ASP.NET Core 2,1 gera uma resposta de detalhes do problema que é quase compatível com RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="cd926-157">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="cd926-158">Se a conformidade com o percentual de 100 for importante, atualize o projeto para ASP.NET Core 2,2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="cd926-158">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd926-159">A resposta de erro pode ser configurada de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="cd926-159">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="cd926-160">Implementar ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="cd926-160">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="cd926-161">Usar ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="cd926-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="cd926-162">Implementa `ProblemDetailsFactory`</span><span class="sxs-lookup"><span data-stu-id="cd926-162">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="cd926-163">O MVC usa <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> para produzir todas as instâncias do <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> e do <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="cd926-163">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="cd926-164">Isso inclui respostas de erro de cliente, respostas de erro de falha de validação e os <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> métodos auxiliares and.</span><span class="sxs-lookup"><span data-stu-id="cd926-164">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="cd926-165">Para personalizar a resposta dos detalhes do problema, registre uma implementação personalizada do <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cd926-165">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="cd926-166">A resposta de erro pode ser configurada conforme descrito na seção [usar ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="cd926-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="cd926-167">Usar ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="cd926-167">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="cd926-168">Use a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> para configurar o conteúdo da resposta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="cd926-168">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="cd926-169">Por exemplo, o código a seguir em `Startup.ConfigureServices` atualiza a `type` propriedade para 404 respostas:</span><span class="sxs-lookup"><span data-stu-id="cd926-169">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a><span data-ttu-id="cd926-170">Middleware personalizado para manipular exceções</span><span class="sxs-lookup"><span data-stu-id="cd926-170">Custom Middleware to handle exceptions</span></span>

<span data-ttu-id="cd926-171">Os padrões no middleware de manipulação de exceção funcionam bem para a maioria dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="cd926-171">The defaults in the exception handling middleware works well for most apps.</span></span> <span data-ttu-id="cd926-172">Para aplicativos que exigem manipulação de exceção especializada, considere [Personalizar o middleware de manipulação de exceção](xref:fundamentals/error-handling#exception-handler-lambda).</span><span class="sxs-lookup"><span data-stu-id="cd926-172">For apps that require specialized exception handling, consider [customizing the exception handling middleware](xref:fundamentals/error-handling#exception-handler-lambda).</span></span>
