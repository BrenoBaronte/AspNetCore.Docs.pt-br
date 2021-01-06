---
title: Tratar erros no ASP.NET Core
author: rick-anderson
description: Descubra como tratar erros em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: ad9920ccd830b93d083f3c5ede03702164842b6e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753108"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="f7159-103">Tratar erros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7159-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f7159-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f7159-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f7159-105">Este artigo aborda abordagens comuns para lidar com erros em ASP.NET Core aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="f7159-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="f7159-106">Consulte <xref:web-api/handle-errors> para APIs da Web.</span><span class="sxs-lookup"><span data-stu-id="f7159-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="f7159-107">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f7159-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="f7159-108">([Como baixar](xref:index#how-to-download-a-sample).) A guia rede nas ferramentas de desenvolvedor de navegador F12 é útil ao testar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="f7159-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f7159-109">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="f7159-109">Developer Exception Page</span></span>

<span data-ttu-id="f7159-110">A *Página de exceção do desenvolvedor* exibe informações detalhadas sobre as exceções de solicitação.</span><span class="sxs-lookup"><span data-stu-id="f7159-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="f7159-111">Os modelos de ASP.NET Core geram o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f7159-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="f7159-112">O código realçado anterior habilita a página de exceção do desenvolvedor quando o aplicativo está em execução no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f7159-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f7159-113">Os modelos colocam no <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> início do pipeline de middleware para que ele possa detectar exceções lançadas no middleware a seguir.</span><span class="sxs-lookup"><span data-stu-id="f7159-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="f7159-114">O código anterior habilita a página de exceção do desenvolvedor \***apenas** _ quando o aplicativo é executado no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f7159-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="f7159-115">As informações detalhadas de exceção não devem ser exibidas publicamente quando o aplicativo é executado no ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="f7159-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="f7159-116">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f7159-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f7159-117">A página de exceção do desenvolvedor inclui as seguintes informações sobre a exceção e a solicitação:</span><span class="sxs-lookup"><span data-stu-id="f7159-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="f7159-118">_ Rastreamento de pilha</span><span class="sxs-lookup"><span data-stu-id="f7159-118">_ Stack trace</span></span>
* <span data-ttu-id="f7159-119">Parâmetros de cadeia de caracteres de consulta, se houver</span><span class="sxs-lookup"><span data-stu-id="f7159-119">Query string parameters if any</span></span>
* <span data-ttu-id="f7159-120">Cookies se houver</span><span class="sxs-lookup"><span data-stu-id="f7159-120">Cookies if any</span></span>
* <span data-ttu-id="f7159-121">Cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="f7159-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="f7159-122">Página do Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-122">Exception handler page</span></span>

<span data-ttu-id="f7159-123">Para configurar uma página de tratamento de erros personalizada para o [ambiente de produção](xref:fundamentals/environments), chame <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="f7159-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f7159-124">Este middleware de manipulação de exceção:</span><span class="sxs-lookup"><span data-stu-id="f7159-124">This exception handling middleware:</span></span>

* <span data-ttu-id="f7159-125">Captura e registra em log as exceções.</span><span class="sxs-lookup"><span data-stu-id="f7159-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="f7159-126">Executa novamente a solicitação em um pipeline alternativo usando o caminho indicado.</span><span class="sxs-lookup"><span data-stu-id="f7159-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="f7159-127">A solicitação não será executada novamente se a resposta tiver sido iniciada.</span><span class="sxs-lookup"><span data-stu-id="f7159-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="f7159-128">O código gerado pelo modelo executa novamente a solicitação usando o `/Error` caminho.</span><span class="sxs-lookup"><span data-stu-id="f7159-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="f7159-129">No exemplo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adiciona o middleware de manipulação de exceção em ambientes que não são de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="f7159-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="f7159-130">O Razor modelo de aplicativo pages fornece uma página de erro (*. cshtml*) e uma <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="f7159-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="f7159-131">Para um aplicativo MVC, o modelo de projeto inclui um `Error` método de ação e uma exibição de erro para o controlador inicial.</span><span class="sxs-lookup"><span data-stu-id="f7159-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="f7159-132">Não marque o método de ação do manipulador de erros com atributos do método HTTP, como `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="f7159-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="f7159-133">Os verbos explícitos impedem que algumas solicitações atinjam o método de ação.</span><span class="sxs-lookup"><span data-stu-id="f7159-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="f7159-134">Permitir acesso anônimo ao método se usuários não autenticados devem ver o modo de exibição de erro.</span><span class="sxs-lookup"><span data-stu-id="f7159-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="f7159-135">Acessar a exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-135">Access the exception</span></span>

<span data-ttu-id="f7159-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acessar a exceção e o caminho de solicitação original em um manipulador de erro.</span><span class="sxs-lookup"><span data-stu-id="f7159-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="f7159-137">O código a seguir adiciona `ExceptionMessage` as *páginas padrão/Error. cshtml. cs* geradas pelos modelos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f7159-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="f7159-138">**Não** forneça informações de erro confidenciais aos clientes.</span><span class="sxs-lookup"><span data-stu-id="f7159-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="f7159-139">Fornecer erros é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="f7159-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="f7159-140">Para testar a exceção no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="f7159-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f7159-141">Defina o ambiente como produção.</span><span class="sxs-lookup"><span data-stu-id="f7159-141">Set the environment to production.</span></span>
* <span data-ttu-id="f7159-142">Remova os comentários de `webBuilder.UseStartup<Startup>();` em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7159-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f7159-143">Selecione **disparar uma exceção** no Home Page.</span><span class="sxs-lookup"><span data-stu-id="f7159-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="f7159-144">Lambda do Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-144">Exception handler lambda</span></span>

<span data-ttu-id="f7159-145">Uma alternativa a uma [página personalizada de manipulador de exceção](#exception-handler-page) é fornecer um lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="f7159-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f7159-146">Usar um lambda permite acessar o erro antes de retornar a resposta.</span><span class="sxs-lookup"><span data-stu-id="f7159-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="f7159-147">O código a seguir usa um lambda para manipulação de exceção:</span><span class="sxs-lookup"><span data-stu-id="f7159-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="f7159-148">**Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes.</span><span class="sxs-lookup"><span data-stu-id="f7159-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="f7159-149">Fornecer erros é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="f7159-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="f7159-150">Para testar o lambda de manipulação de exceção no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="f7159-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f7159-151">Defina o ambiente como produção.</span><span class="sxs-lookup"><span data-stu-id="f7159-151">Set the environment to production.</span></span>
* <span data-ttu-id="f7159-152">Remova os comentários de `webBuilder.UseStartup<StartupLambda>();` em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7159-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f7159-153">Selecione **disparar uma exceção** no Home Page.</span><span class="sxs-lookup"><span data-stu-id="f7159-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="f7159-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f7159-154">UseStatusCodePages</span></span>

<span data-ttu-id="f7159-155">Por padrão, um aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status de erro HTTP, como *404-não encontrado*.</span><span class="sxs-lookup"><span data-stu-id="f7159-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="f7159-156">Quando o aplicativo encontra um código de status de erro HTTP 400-599 que não tem um corpo, ele retorna o código de status e um corpo de resposta vazio.</span><span class="sxs-lookup"><span data-stu-id="f7159-156">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="f7159-157">Para fornecer páginas de código de status, use o middleware de páginas de código de status.</span><span class="sxs-lookup"><span data-stu-id="f7159-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="f7159-158">Para habilitar os manipuladores padrão somente texto para os códigos de status de erros comuns, chame <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> no método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f7159-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="f7159-159">Chame `UseStatusCodePages` antes do middleware de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="f7159-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="f7159-160">Por exemplo, chame `UseStatusCodePages` antes do middleware de arquivo estático e do middleware de pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="f7159-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="f7159-161">Quando `UseStatusCodePages` não é usado, navegar para uma URL sem um ponto de extremidade retorna uma mensagem de erro dependente do navegador indicando que o ponto de extremidade não foi encontrado.</span><span class="sxs-lookup"><span data-stu-id="f7159-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="f7159-162">Por exemplo, navegando até `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="f7159-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="f7159-163">Quando `UseStatusCodePages` é chamado, o navegador retorna:</span><span class="sxs-lookup"><span data-stu-id="f7159-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="f7159-164">`UseStatusCodePages` Normalmente, não é usado em produção porque retorna uma mensagem que não é útil para os usuários.</span><span class="sxs-lookup"><span data-stu-id="f7159-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f7159-165">Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="f7159-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f7159-166">Defina o ambiente como produção.</span><span class="sxs-lookup"><span data-stu-id="f7159-166">Set the environment to production.</span></span>
* <span data-ttu-id="f7159-167">Remova os comentários de `webBuilder.UseStartup<StartupUseStatusCodePages>();` em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7159-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f7159-168">Selecione os links no home page na home page.</span><span class="sxs-lookup"><span data-stu-id="f7159-168">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="f7159-169">O middleware de páginas de código de status **não** captura exceções.</span><span class="sxs-lookup"><span data-stu-id="f7159-169">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="f7159-170">Para fornecer uma página de tratamento de erro personalizada, use a [página manipulador de exceção](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="f7159-170">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="f7159-171">UseStatusCodePages com cadeia de caracteres de formato</span><span class="sxs-lookup"><span data-stu-id="f7159-171">UseStatusCodePages with format string</span></span>

<span data-ttu-id="f7159-172">Para personalizar o texto e o tipo de conteúdo de resposta, use uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva um tipo de conteúdo e uma cadeia de caracteres de formato:</span><span class="sxs-lookup"><span data-stu-id="f7159-172">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="f7159-173">No código anterior, `{0}` é um espaço reservado para o código de erro.</span><span class="sxs-lookup"><span data-stu-id="f7159-173">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="f7159-174">`UseStatusCodePages` com uma cadeia de caracteres de formato geralmente não é usada na produção porque ela retorna uma mensagem que não é útil para os usuários.</span><span class="sxs-lookup"><span data-stu-id="f7159-174">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f7159-175">Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remova os comentários de `webBuilder.UseStartup<StartupFormat>();` em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7159-175">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="f7159-176">UseStatusCodePages com lambda</span><span class="sxs-lookup"><span data-stu-id="f7159-176">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="f7159-177">Para especificar a manipulação de erro personalizada e o código de gravação de resposta, use a sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva uma expressão lambda:</span><span class="sxs-lookup"><span data-stu-id="f7159-177">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="f7159-178">`UseStatusCodePages` com um lambda normalmente não é usado em produção porque retorna uma mensagem que não é útil para os usuários.</span><span class="sxs-lookup"><span data-stu-id="f7159-178">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f7159-179">Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remova os comentários de `webBuilder.UseStartup<StartupStatusLambda>();` em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7159-179">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="f7159-180">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="f7159-180">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="f7159-181">O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="f7159-181">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="f7159-182">Envia um código de status [302 – Encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/302) ao cliente.</span><span class="sxs-lookup"><span data-stu-id="f7159-182">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="f7159-183">Redireciona o cliente para o ponto de extremidade de tratamento de erros fornecido no modelo de URL.</span><span class="sxs-lookup"><span data-stu-id="f7159-183">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="f7159-184">O ponto de extremidade de tratamento de erros normalmente exibe informações de erro e retorna HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="f7159-184">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="f7159-185">O modelo de URL pode incluir um `{0}` espaço reservado para o código de status, conforme mostrado no código anterior.</span><span class="sxs-lookup"><span data-stu-id="f7159-185">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="f7159-186">Se o modelo de URL começar com `~` (til), o `~` será substituído pelo aplicativo `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="f7159-186">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="f7159-187">Ao especificar um ponto de extremidade no aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="f7159-187">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f7159-188">Para obter um Razor exemplo de páginas, consulte [páginas/mystatuscode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="f7159-188">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f7159-189">Este método normalmente é usado quando o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f7159-189">This method is commonly used when the app:</span></span>

* <span data-ttu-id="f7159-190">Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro.</span><span class="sxs-lookup"><span data-stu-id="f7159-190">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="f7159-191">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.</span><span class="sxs-lookup"><span data-stu-id="f7159-191">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="f7159-192">Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.</span><span class="sxs-lookup"><span data-stu-id="f7159-192">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="f7159-193">Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remova os comentários de `webBuilder.UseStartup<StartupSCredirect>();` em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7159-193">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="f7159-194">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="f7159-194">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="f7159-195">O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="f7159-195">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="f7159-196">Retorna o código de status original ao cliente.</span><span class="sxs-lookup"><span data-stu-id="f7159-196">Returns the original status code to the client.</span></span>
* <span data-ttu-id="f7159-197">Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.</span><span class="sxs-lookup"><span data-stu-id="f7159-197">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="f7159-198">Se um ponto de extremidade dentro do aplicativo for especificado, crie uma exibição ou Razor página do MVC para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="f7159-198">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f7159-199">Certifique-se `UseStatusCodePagesWithReExecute` de que é colocado antes `UseRouting` para que a solicitação possa ser redirecionada para a página de status.</span><span class="sxs-lookup"><span data-stu-id="f7159-199">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="f7159-200">Para obter um Razor exemplo de páginas, consulte [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="f7159-200">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f7159-201">Este método normalmente é usado quando o aplicativo tem que:</span><span class="sxs-lookup"><span data-stu-id="f7159-201">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="f7159-202">Processar a solicitação sem redirecionar para um ponto de extremidade diferente.</span><span class="sxs-lookup"><span data-stu-id="f7159-202">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="f7159-203">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.</span><span class="sxs-lookup"><span data-stu-id="f7159-203">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="f7159-204">Preservar e retornar o código de status original com a resposta.</span><span class="sxs-lookup"><span data-stu-id="f7159-204">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="f7159-205">A URL e os modelos de cadeia de caracteres de consulta podem incluir um espaço reservado `{0}` para o código de status.</span><span class="sxs-lookup"><span data-stu-id="f7159-205">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="f7159-206">O modelo de URL deve começar com `/` .</span><span class="sxs-lookup"><span data-stu-id="f7159-206">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="f7159-207">O ponto de extremidade que processa o erro pode obter a URL original que gerou o erro, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="f7159-207">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="f7159-208">Para obter um Razor exemplo de páginas, consulte [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="f7159-208">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f7159-209">Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remova os comentários de `webBuilder.UseStartup<StartupSCreX>();` em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f7159-209">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="f7159-210">Desabilitar páginas de código de status</span><span class="sxs-lookup"><span data-stu-id="f7159-210">Disable status code pages</span></span>

<span data-ttu-id="f7159-211">Para desabilitar as páginas de código de status de um método de ação ou controlador MVC, use o atributo [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="f7159-211">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="f7159-212">Para desabilitar as páginas de código de status para solicitações específicas em um Razor método de manipulador de páginas ou em um controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="f7159-212">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="f7159-213">Código de tratamento de exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-213">Exception-handling code</span></span>

<span data-ttu-id="f7159-214">O código nas páginas de manipulação de exceção também pode gerar exceções.</span><span class="sxs-lookup"><span data-stu-id="f7159-214">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="f7159-215">As páginas de erro de produção devem ser testadas minuciosamente e tomar cuidado adicional para evitar gerar exceções próprias.</span><span class="sxs-lookup"><span data-stu-id="f7159-215">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="f7159-216">Cabeçalhos de resposta</span><span class="sxs-lookup"><span data-stu-id="f7159-216">Response headers</span></span>

<span data-ttu-id="f7159-217">Depois que os cabeçalhos de resposta são enviados:</span><span class="sxs-lookup"><span data-stu-id="f7159-217">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="f7159-218">O aplicativo já não consegue alterar o código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="f7159-218">The app can't change the response's status code.</span></span>
* <span data-ttu-id="f7159-219">As páginas de exceção ou manipuladores não podem ser executados.</span><span class="sxs-lookup"><span data-stu-id="f7159-219">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="f7159-220">A resposta deve ser concluída ou a conexão será anulada.</span><span class="sxs-lookup"><span data-stu-id="f7159-220">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="f7159-221">Tratamento de exceções do servidor</span><span class="sxs-lookup"><span data-stu-id="f7159-221">Server exception handling</span></span>

<span data-ttu-id="f7159-222">Além da lógica de tratamento de exceção em um aplicativo, a [implementação do servidor http](xref:fundamentals/servers/index) pode lidar com algumas exceções.</span><span class="sxs-lookup"><span data-stu-id="f7159-222">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="f7159-223">Se o servidor capturar uma exceção antes de os cabeçalhos de resposta serem enviados, o servidor enviará uma `500 - Internal Server Error` resposta sem um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="f7159-223">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="f7159-224">Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão.</span><span class="sxs-lookup"><span data-stu-id="f7159-224">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="f7159-225">As solicitações que não são tratadas pelo aplicativo são manipuladas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="f7159-225">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="f7159-226">Qualquer exceção que ocorrer quando o servidor estiver tratando a solicitação será tratada pelo tratamento de exceção do servidor.</span><span class="sxs-lookup"><span data-stu-id="f7159-226">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="f7159-227">As páginas de erro personalizadas do aplicativo, o middleware de tratamento de exceção e os filtros configurados não afetam esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="f7159-227">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="f7159-228">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="f7159-228">Startup exception handling</span></span>

<span data-ttu-id="f7159-229">Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7159-229">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="f7159-230">O host pode ser configurado para [capturar erros de inicialização](xref:fundamentals/host/web-host#capture-startup-errors) e [capturar erros detalhados](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="f7159-230">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="f7159-231">A camada de hospedagem só poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta.</span><span class="sxs-lookup"><span data-stu-id="f7159-231">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="f7159-232">Se a associação falhar:</span><span class="sxs-lookup"><span data-stu-id="f7159-232">If binding fails:</span></span>

* <span data-ttu-id="f7159-233">A camada de hospedagem registrará uma exceção crítica.</span><span class="sxs-lookup"><span data-stu-id="f7159-233">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="f7159-234">O processo dotnet falhará.</span><span class="sxs-lookup"><span data-stu-id="f7159-234">The dotnet process crashes.</span></span>
* <span data-ttu-id="f7159-235">Nenhuma página de erro é exibida quando o servidor HTTP é [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f7159-235">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f7159-236">Quando executado no [IIS](/iis) (ou no Serviço de Aplicativo do Azure) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="f7159-236">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="f7159-237">Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f7159-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="f7159-238">Página de erro do banco de dados</span><span class="sxs-lookup"><span data-stu-id="f7159-238">Database error page</span></span>

<span data-ttu-id="f7159-239">O filtro de exceção da página do desenvolvedor de banco de dados `AddDatabaseDeveloperPageExceptionFilter` captura as exceções relacionadas ao banco de dados que podem ser resolvidas usando Entity Framework Core migrações.</span><span class="sxs-lookup"><span data-stu-id="f7159-239">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="f7159-240">Quando essas exceções ocorrem, uma resposta HTML é gerada com detalhes de possíveis ações para resolver o problema.</span><span class="sxs-lookup"><span data-stu-id="f7159-240">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="f7159-241">Esta página é habilitada apenas no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f7159-241">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="f7159-242">O código a seguir foi gerado pelo ASP.NET Core Razor modelos de páginas quando contas de usuário individuais foram especificadas:</span><span class="sxs-lookup"><span data-stu-id="f7159-242">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="f7159-243">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-243">Exception filters</span></span>

<span data-ttu-id="f7159-244">Em aplicativos MVC, os filtros de exceção podem ser configurados globalmente, por controlador ou por ação.</span><span class="sxs-lookup"><span data-stu-id="f7159-244">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="f7159-245">Em Razor páginas aplicativos, eles podem ser configurados globalmente ou por modelo de página.</span><span class="sxs-lookup"><span data-stu-id="f7159-245">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="f7159-246">Esses filtros manipulam quaisquer exceções sem tratamento que ocorrem durante a execução de uma ação do controlador ou outro filtro.</span><span class="sxs-lookup"><span data-stu-id="f7159-246">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="f7159-247">Para obter mais informações, consulte <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="f7159-247">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="f7159-248">Filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de [manipulação de exceção](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)interno, `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="f7159-248">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="f7159-249">É recomendável usar `UseExceptionHandler` , a menos que você precise executar o tratamento de erros de forma diferente com base em qual ação MVC é escolhida.</span><span class="sxs-lookup"><span data-stu-id="f7159-249">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="f7159-250">Erros de estado do modelo</span><span class="sxs-lookup"><span data-stu-id="f7159-250">Model state errors</span></span>

<span data-ttu-id="f7159-251">Confira informações sobre como lidar com erros de estado de modelo em [model binding](xref:mvc/models/model-binding) e [Validação de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="f7159-251">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7159-252">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f7159-252">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f7159-253">Por  [Tom Dykstra](https://github.com/tdykstra/)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f7159-253">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f7159-254">Este artigo aborda abordagens comuns para lidar com erros em ASP.NET Core aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="f7159-254">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="f7159-255">Consulte <xref:web-api/handle-errors> para APIs da Web.</span><span class="sxs-lookup"><span data-stu-id="f7159-255">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="f7159-256">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f7159-256">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="f7159-257">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="f7159-257">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f7159-258">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="f7159-258">Developer Exception Page</span></span>

<span data-ttu-id="f7159-259">A *Página de exceção do desenvolvedor* exibe informações detalhadas sobre as exceções de solicitação.</span><span class="sxs-lookup"><span data-stu-id="f7159-259">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="f7159-260">Os modelos de ASP.NET Core geram o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f7159-260">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="f7159-261">O código anterior habilita a página de exceção do desenvolvedor quando o aplicativo está em execução no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f7159-261">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f7159-262">Os modelos <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> são colocados antes de qualquer middleware, portanto, as exceções são capturadas no middleware a seguir.</span><span class="sxs-lookup"><span data-stu-id="f7159-262">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="f7159-263">O código anterior habilita a página de exceção do desenvolvedor **somente quando o aplicativo está em execução no ambiente de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="f7159-263">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="f7159-264">As informações detalhadas de exceção não devem ser exibidas publicamente quando o aplicativo é executado na produção.</span><span class="sxs-lookup"><span data-stu-id="f7159-264">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="f7159-265">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f7159-265">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f7159-266">A página de exceção do desenvolvedor inclui as seguintes informações sobre a exceção e a solicitação:</span><span class="sxs-lookup"><span data-stu-id="f7159-266">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="f7159-267">Rastreamento de pilha</span><span class="sxs-lookup"><span data-stu-id="f7159-267">Stack trace</span></span>
* <span data-ttu-id="f7159-268">Parâmetros de cadeia de caracteres de consulta, se houver</span><span class="sxs-lookup"><span data-stu-id="f7159-268">Query string parameters if any</span></span>
* <span data-ttu-id="f7159-269">Cookies se houver</span><span class="sxs-lookup"><span data-stu-id="f7159-269">Cookies if any</span></span>
* <span data-ttu-id="f7159-270">Cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="f7159-270">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="f7159-271">Página do Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-271">Exception handler page</span></span>

<span data-ttu-id="f7159-272">Use o Middleware de tratamento de exceção para configurar uma página personalizada de tratamento de erro para o ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="f7159-272">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="f7159-273">O middleware:</span><span class="sxs-lookup"><span data-stu-id="f7159-273">The middleware:</span></span>

* <span data-ttu-id="f7159-274">Captura e registra em log as exceções.</span><span class="sxs-lookup"><span data-stu-id="f7159-274">Catches and logs exceptions.</span></span>
* <span data-ttu-id="f7159-275">Executa novamente a solicitação em um pipeline alternativo para a página ou controlador indicado.</span><span class="sxs-lookup"><span data-stu-id="f7159-275">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="f7159-276">A solicitação não será executada novamente se a resposta tiver sido iniciada.</span><span class="sxs-lookup"><span data-stu-id="f7159-276">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="f7159-277">O código gerado pelo modelo executa novamente a solicitação para `/Error` .</span><span class="sxs-lookup"><span data-stu-id="f7159-277">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="f7159-278">No exemplo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> inclui o Middleware de Manipulação de Exceções em ambientes que não são de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="f7159-278">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="f7159-279">O Razor modelo de aplicativo pages fornece uma página de erro (*. cshtml*) e uma <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="f7159-279">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="f7159-280">Para um aplicativo MVC, o modelo de projeto inclui um método de ação de erro e uma exibição de erro no controlador inicial.</span><span class="sxs-lookup"><span data-stu-id="f7159-280">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="f7159-281">Não marque o método de ação do manipulador de erros com atributos do método HTTP, como `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="f7159-281">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="f7159-282">Verbos explícitos impedem algumas solicitações de chegar ao método.</span><span class="sxs-lookup"><span data-stu-id="f7159-282">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="f7159-283">Permitir acesso anônimo ao método se usuários não autenticados devem ver o modo de exibição de erro.</span><span class="sxs-lookup"><span data-stu-id="f7159-283">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="f7159-284">Acessar a exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-284">Access the exception</span></span>

<span data-ttu-id="f7159-285">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acessar a exceção e o caminho de solicitação original em uma página ou controlador de manipulador de erro:</span><span class="sxs-lookup"><span data-stu-id="f7159-285">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="f7159-286">**Não** forneça informações de erro confidenciais aos clientes.</span><span class="sxs-lookup"><span data-stu-id="f7159-286">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="f7159-287">Fornecer erros é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="f7159-287">Serving errors is a security risk.</span></span>

<span data-ttu-id="f7159-288">Para disparar a página de tratamento de exceção anterior, defina o ambiente como produções e Force uma exceção.</span><span class="sxs-lookup"><span data-stu-id="f7159-288">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="f7159-289">Lambda do Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-289">Exception handler lambda</span></span>

<span data-ttu-id="f7159-290">Uma alternativa a uma [página personalizada de manipulador de exceção](#exception-handler-page) é fornecer um lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="f7159-290">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f7159-291">Usar um lambda permite acessar o erro antes de retornar a resposta.</span><span class="sxs-lookup"><span data-stu-id="f7159-291">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="f7159-292">Este é um exemplo de como usar um lambda para a manipulação de exceção:</span><span class="sxs-lookup"><span data-stu-id="f7159-292">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="f7159-293">No código anterior, `await context.Response.WriteAsync(new string(' ', 512));` é adicionado para que o navegador Internet Explorer exiba a mensagem de erro em vez de uma mensagem de erro do IE.</span><span class="sxs-lookup"><span data-stu-id="f7159-293">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="f7159-294">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="f7159-294">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="f7159-295">**Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes.</span><span class="sxs-lookup"><span data-stu-id="f7159-295">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="f7159-296">Fornecer erros é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="f7159-296">Serving errors is a security risk.</span></span>

<span data-ttu-id="f7159-297">Para ver o resultado do lambda de tratamento de exceções no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use as diretivas de pré-processador `ProdEnvironment` e `ErrorHandlerLambda` e selecione **Disparar uma exceção** na página inicial.</span><span class="sxs-lookup"><span data-stu-id="f7159-297">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="f7159-298">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f7159-298">UseStatusCodePages</span></span>

<span data-ttu-id="f7159-299">Por padrão, o aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status HTTP, como *404 - Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="f7159-299">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="f7159-300">O aplicativo retornar um código de status e um corpo de resposta vazio.</span><span class="sxs-lookup"><span data-stu-id="f7159-300">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="f7159-301">Use o middleware das Páginas de código de status para fornecer páginas de código de status.</span><span class="sxs-lookup"><span data-stu-id="f7159-301">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="f7159-302">O middleware é disponibilizado pelo pacote [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .</span><span class="sxs-lookup"><span data-stu-id="f7159-302">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="f7159-303">Para habilitar os manipuladores padrão somente texto para os códigos de status de erros comuns, chame <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> no método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f7159-303">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="f7159-304">Chame `UseStatusCodePages` antes do middleware de tratamento da solicitação (por exemplo, o middleware de arquivos estáticos e o middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="f7159-304">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="f7159-305">Quando `UseStatusCodePages` não é usado, navegar para uma URL sem um ponto de extremidade retorna uma mensagem de erro dependente do navegador indicando que o ponto de extremidade não foi encontrado.</span><span class="sxs-lookup"><span data-stu-id="f7159-305">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="f7159-306">Por exemplo, navegando até `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="f7159-306">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="f7159-307">Quando `UseStatusCodePages` é chamado, o navegador retorna:</span><span class="sxs-lookup"><span data-stu-id="f7159-307">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="f7159-308">UseStatusCodePages com cadeia de caracteres de formato</span><span class="sxs-lookup"><span data-stu-id="f7159-308">UseStatusCodePages with format string</span></span>

<span data-ttu-id="f7159-309">Para personalizar o texto e o tipo de conteúdo de resposta, use uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva um tipo de conteúdo e uma cadeia de caracteres de formato:</span><span class="sxs-lookup"><span data-stu-id="f7159-309">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="f7159-310">UseStatusCodePages com lambda</span><span class="sxs-lookup"><span data-stu-id="f7159-310">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="f7159-311">Para especificar a manipulação de erro personalizada e o código de gravação de resposta, use a sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva uma expressão lambda:</span><span class="sxs-lookup"><span data-stu-id="f7159-311">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="f7159-312">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="f7159-312">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="f7159-313">O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="f7159-313">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="f7159-314">Envia um código de status *302 – Encontrado* ao cliente.</span><span class="sxs-lookup"><span data-stu-id="f7159-314">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="f7159-315">Redireciona o cliente para o local fornecido no modelo de URL.</span><span class="sxs-lookup"><span data-stu-id="f7159-315">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="f7159-316">O modelo de URL pode incluir um espaço reservado `{0}` para o código de status, conforme mostrado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="f7159-316">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="f7159-317">Se o modelo de URL começar com `~` (til), o `~` será substituído pelo aplicativo `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="f7159-317">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="f7159-318">Se você apontar para um ponto de extremidade dentro do aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="f7159-318">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f7159-319">Para obter um Razor exemplo de páginas, consulte *páginas/StatusCode. cshtml* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f7159-319">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f7159-320">Este método normalmente é usado quando o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f7159-320">This method is commonly used when the app:</span></span>

* <span data-ttu-id="f7159-321">Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro.</span><span class="sxs-lookup"><span data-stu-id="f7159-321">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="f7159-322">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.</span><span class="sxs-lookup"><span data-stu-id="f7159-322">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="f7159-323">Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.</span><span class="sxs-lookup"><span data-stu-id="f7159-323">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="f7159-324">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="f7159-324">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="f7159-325">O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="f7159-325">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="f7159-326">Retorna o código de status original ao cliente.</span><span class="sxs-lookup"><span data-stu-id="f7159-326">Returns the original status code to the client.</span></span>
* <span data-ttu-id="f7159-327">Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.</span><span class="sxs-lookup"><span data-stu-id="f7159-327">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="f7159-328">Se você apontar para um ponto de extremidade dentro do aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="f7159-328">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f7159-329">Certifique-se `UseStatusCodePagesWithReExecute` de que é colocado antes `UseRouting` para que a solicitação possa ser redirecionada para a página de status.</span><span class="sxs-lookup"><span data-stu-id="f7159-329">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="f7159-330">Para obter um Razor exemplo de páginas, consulte *páginas/StatusCode. cshtml* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f7159-330">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f7159-331">Este método normalmente é usado quando o aplicativo tem que:</span><span class="sxs-lookup"><span data-stu-id="f7159-331">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="f7159-332">Processar a solicitação sem redirecionar para um ponto de extremidade diferente.</span><span class="sxs-lookup"><span data-stu-id="f7159-332">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="f7159-333">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.</span><span class="sxs-lookup"><span data-stu-id="f7159-333">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="f7159-334">Preservar e retornar o código de status original com a resposta.</span><span class="sxs-lookup"><span data-stu-id="f7159-334">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="f7159-335">Os modelos de URL e cadeia de caracteres de consulta podem incluir um espaço reservado (`{0}`) para o código de status.</span><span class="sxs-lookup"><span data-stu-id="f7159-335">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="f7159-336">O modelo de URL deve começar com uma barra (`/`).</span><span class="sxs-lookup"><span data-stu-id="f7159-336">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="f7159-337">Ao usar um espaço reservado no caminho, verifique se o ponto de extremidade (página ou controlador) pode processar o segmento de linha.</span><span class="sxs-lookup"><span data-stu-id="f7159-337">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="f7159-338">Por exemplo, uma Razor página de erros deve aceitar o valor do segmento de caminho opcional com a `@page` diretiva:</span><span class="sxs-lookup"><span data-stu-id="f7159-338">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="f7159-339">O ponto de extremidade que processa o erro pode obter a URL original que gerou o erro, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="f7159-339">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="f7159-340">Desabilitar páginas de código de status</span><span class="sxs-lookup"><span data-stu-id="f7159-340">Disable status code pages</span></span>

<span data-ttu-id="f7159-341">Para desabilitar páginas de código de status para um controlador MVC ou um método de ação, use o [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="f7159-341">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="f7159-342">Para desabilitar as páginas de código de status para solicitações específicas em um Razor método de manipulador de páginas ou em um controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="f7159-342">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="f7159-343">Código de tratamento de exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-343">Exception-handling code</span></span>

<span data-ttu-id="f7159-344">Código em páginas de tratamento de exceção pode gerar exceções.</span><span class="sxs-lookup"><span data-stu-id="f7159-344">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="f7159-345">Geralmente, é uma boa ideia que páginas de erro de produção sejam compostas por conteúdo puramente estático.</span><span class="sxs-lookup"><span data-stu-id="f7159-345">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="f7159-346">Cabeçalhos de resposta</span><span class="sxs-lookup"><span data-stu-id="f7159-346">Response headers</span></span>

<span data-ttu-id="f7159-347">Depois que os cabeçalhos de resposta são enviados:</span><span class="sxs-lookup"><span data-stu-id="f7159-347">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="f7159-348">O aplicativo já não consegue alterar o código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="f7159-348">The app can't change the response's status code.</span></span>
* <span data-ttu-id="f7159-349">As páginas de exceção ou manipuladores não podem ser executados.</span><span class="sxs-lookup"><span data-stu-id="f7159-349">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="f7159-350">A resposta deve ser concluída ou a conexão será anulada.</span><span class="sxs-lookup"><span data-stu-id="f7159-350">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="f7159-351">Tratamento de exceções do servidor</span><span class="sxs-lookup"><span data-stu-id="f7159-351">Server exception handling</span></span>

<span data-ttu-id="f7159-352">Além da lógica de tratamento de exceção no aplicativo, a [implementação do servidor HTTP](xref:fundamentals/servers/index) pode lidar com algumas exceções.</span><span class="sxs-lookup"><span data-stu-id="f7159-352">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="f7159-353">Se o servidor capturar uma exceção antes que os cabeçalhos de resposta sejam enviados, o servidor enviará uma resposta *500 Erro Interno do Servidor* sem um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="f7159-353">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="f7159-354">Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão.</span><span class="sxs-lookup"><span data-stu-id="f7159-354">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="f7159-355">As solicitações que não são manipuladas pelo aplicativo são manipuladas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="f7159-355">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="f7159-356">Qualquer exceção que ocorrer quando o servidor estiver tratando a solicitação será tratada pelo tratamento de exceção do servidor.</span><span class="sxs-lookup"><span data-stu-id="f7159-356">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="f7159-357">As páginas de erro personalizadas do aplicativo, o middleware de tratamento de exceção e os filtros configurados não afetam esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="f7159-357">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="f7159-358">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="f7159-358">Startup exception handling</span></span>

<span data-ttu-id="f7159-359">Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7159-359">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="f7159-360">O host pode ser configurado para [capturar erros de inicialização](xref:fundamentals/host/web-host#capture-startup-errors) e [capturar erros detalhados](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="f7159-360">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="f7159-361">A camada de hospedagem só poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta.</span><span class="sxs-lookup"><span data-stu-id="f7159-361">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="f7159-362">Se a associação falhar:</span><span class="sxs-lookup"><span data-stu-id="f7159-362">If binding fails:</span></span>

* <span data-ttu-id="f7159-363">A camada de hospedagem registrará uma exceção crítica.</span><span class="sxs-lookup"><span data-stu-id="f7159-363">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="f7159-364">O processo dotnet falhará.</span><span class="sxs-lookup"><span data-stu-id="f7159-364">The dotnet process crashes.</span></span>
* <span data-ttu-id="f7159-365">Nenhuma página de erro é exibida quando o servidor HTTP é [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f7159-365">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f7159-366">Quando executado no [IIS](/iis) (ou no Serviço de Aplicativo do Azure) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="f7159-366">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="f7159-367">Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f7159-367">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="f7159-368">Página de erro do banco de dados</span><span class="sxs-lookup"><span data-stu-id="f7159-368">Database error page</span></span>

<span data-ttu-id="f7159-369">O middleware da página de erro do banco de dados captura as exceções relacionadas ao banco de dados que podem ser resolvidas usando Entity Framework migrações.</span><span class="sxs-lookup"><span data-stu-id="f7159-369">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="f7159-370">Quando estas exceções ocorrem, é gerada uma resposta HTML com detalhes das ações possíveis para resolver o problema.</span><span class="sxs-lookup"><span data-stu-id="f7159-370">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="f7159-371">Esta página só deve ser habilitada no Ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f7159-371">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="f7159-372">Habilite a página adicionando código a `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f7159-372">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="f7159-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requer o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="f7159-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="f7159-374">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="f7159-374">Exception filters</span></span>

<span data-ttu-id="f7159-375">Em aplicativos MVC, os filtros de exceção podem ser configurados globalmente, por controlador ou por ação.</span><span class="sxs-lookup"><span data-stu-id="f7159-375">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="f7159-376">Em Razor páginas aplicativos, eles podem ser configurados globalmente ou por modelo de página.</span><span class="sxs-lookup"><span data-stu-id="f7159-376">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="f7159-377">Esses filtros tratam qualquer exceção sem tratamento ocorrida durante a execução de uma ação do controlador ou de outro filtro.</span><span class="sxs-lookup"><span data-stu-id="f7159-377">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="f7159-378">Para obter mais informações, consulte <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="f7159-378">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="f7159-379">Os filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="f7159-379">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="f7159-380">É recomendável usar o middleware.</span><span class="sxs-lookup"><span data-stu-id="f7159-380">We recommend using the middleware.</span></span> <span data-ttu-id="f7159-381">Use filtros somente onde você precisar fazer o tratamento de erro de forma diferente com base na ação de MVC escolhida.</span><span class="sxs-lookup"><span data-stu-id="f7159-381">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="f7159-382">Erros de estado do modelo</span><span class="sxs-lookup"><span data-stu-id="f7159-382">Model state errors</span></span>

<span data-ttu-id="f7159-383">Confira informações sobre como lidar com erros de estado de modelo em [model binding](xref:mvc/models/model-binding) e [Validação de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="f7159-383">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7159-384">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f7159-384">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
