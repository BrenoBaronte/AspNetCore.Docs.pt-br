---
title: Tratar erros no ASP.NET Core
author: rick-anderson
description: Descubra como tratar erros em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: ba44479707f526d5aeb1e8d74ced4f0b4996d915
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504743"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="5ea62-103">Tratar erros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ea62-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="5ea62-104">Por [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5ea62-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5ea62-105">Este artigo aborda abordagens comuns para lidar com erros em ASP.NET Core aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="5ea62-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="5ea62-106">Consulte <xref:web-api/handle-errors> para APIs da Web.</span><span class="sxs-lookup"><span data-stu-id="5ea62-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="5ea62-107">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="5ea62-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="5ea62-108">([Como baixar](xref:index#how-to-download-a-sample).) O artigo inclui instruções sobre como definir diretivas de pré-processador ( `#if` , `#endif` , `#define` ) no aplicativo de exemplo para habilitar cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="5ea62-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="5ea62-109">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="5ea62-109">Developer Exception Page</span></span>

<span data-ttu-id="5ea62-110">A *Página de exceção do desenvolvedor* exibe informações detalhadas sobre as exceções de solicitação.</span><span class="sxs-lookup"><span data-stu-id="5ea62-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="5ea62-111">A página é disponibilizada pelo `Microsoft.AspNetCore.Diagnostics` assembly, que está na [ `Microsoft.AspNetCore.App` estrutura compartilhada](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5ea62-111">The page is made available by the `Microsoft.AspNetCore.Diagnostics` assembly, which is in the [`Microsoft.AspNetCore.App` shared framework](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="5ea62-112">Adicione código ao método `Startup.Configure` para habilitar a página quando o aplicativo estiver sendo executado no [ambiente](xref:fundamentals/environments) de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="5ea62-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="5ea62-113">Coloque a chamada para <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> antes de qualquer middleware no qual você deseja capturar exceções.</span><span class="sxs-lookup"><span data-stu-id="5ea62-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="5ea62-114">Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="5ea62-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="5ea62-115">Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção.</span><span class="sxs-lookup"><span data-stu-id="5ea62-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="5ea62-116">Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5ea62-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5ea62-117">A página inclui as seguintes informações sobre a exceção e a solicitação:</span><span class="sxs-lookup"><span data-stu-id="5ea62-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="5ea62-118">Rastreamento de pilha</span><span class="sxs-lookup"><span data-stu-id="5ea62-118">Stack trace</span></span>
* <span data-ttu-id="5ea62-119">Parâmetros de cadeia de caracteres de consulta (se houver algum)</span><span class="sxs-lookup"><span data-stu-id="5ea62-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="5ea62-120">Cookies (se houver)</span><span class="sxs-lookup"><span data-stu-id="5ea62-120">Cookies (if any)</span></span>
* <span data-ttu-id="5ea62-121">Cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="5ea62-121">Headers</span></span>

<span data-ttu-id="5ea62-122">Para ver a Página de exceção do desenvolvedor no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use a diretiva de pré-processador `DevEnvironment` e selecione **Disparar uma exceção** na página inicial.</span><span class="sxs-lookup"><span data-stu-id="5ea62-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="5ea62-123">Página do Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="5ea62-123">Exception handler page</span></span>

<span data-ttu-id="5ea62-124">Use o Middleware de tratamento de exceção para configurar uma página personalizada de tratamento de erro para o ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="5ea62-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="5ea62-125">O middleware:</span><span class="sxs-lookup"><span data-stu-id="5ea62-125">The middleware:</span></span>

* <span data-ttu-id="5ea62-126">Captura e registra em log as exceções.</span><span class="sxs-lookup"><span data-stu-id="5ea62-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="5ea62-127">Executa novamente a solicitação em um pipeline alternativo para a página ou controlador indicado.</span><span class="sxs-lookup"><span data-stu-id="5ea62-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="5ea62-128">A solicitação não será executada novamente se a resposta tiver sido iniciada.</span><span class="sxs-lookup"><span data-stu-id="5ea62-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="5ea62-129">No exemplo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> inclui o Middleware de Manipulação de Exceções em ambientes que não são de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="5ea62-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="5ea62-130">O Razor modelo de aplicativo pages fornece uma página de erro (*. cshtml*) e uma <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="5ea62-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="5ea62-131">Para um aplicativo MVC, o modelo de projeto inclui um Método de ação de erro e uma Exibição de erro.</span><span class="sxs-lookup"><span data-stu-id="5ea62-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="5ea62-132">Este é o método de ação:</span><span class="sxs-lookup"><span data-stu-id="5ea62-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="5ea62-133">Não marque o método de ação do manipulador de erros com atributos do método HTTP, como `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="5ea62-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="5ea62-134">Verbos explícitos impedem algumas solicitações de chegar ao método.</span><span class="sxs-lookup"><span data-stu-id="5ea62-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="5ea62-135">Permita acesso anônimo ao método para que os usuários não autenticados possam capazes receber a exibição de erro.</span><span class="sxs-lookup"><span data-stu-id="5ea62-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="5ea62-136">Acessar a exceção</span><span class="sxs-lookup"><span data-stu-id="5ea62-136">Access the exception</span></span>

<span data-ttu-id="5ea62-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acessar a exceção e o caminho de solicitação original em uma página ou controlador de manipulador de erro:</span><span class="sxs-lookup"><span data-stu-id="5ea62-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="5ea62-138">**Não** forneça informações de erro confidenciais aos clientes.</span><span class="sxs-lookup"><span data-stu-id="5ea62-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="5ea62-139">Fornecer erros é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="5ea62-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="5ea62-140">Para ver a página de tratamento de exceções no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use as diretivas de pré-processador `ProdEnvironment` e `ErrorHandlerPage` e selecione **Disparar uma exceção** na página inicial.</span><span class="sxs-lookup"><span data-stu-id="5ea62-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="5ea62-141">Lambda do Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="5ea62-141">Exception handler lambda</span></span>

<span data-ttu-id="5ea62-142">Uma alternativa a uma [página personalizada de manipulador de exceção](#exception-handler-page) é fornecer um lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="5ea62-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="5ea62-143">Usar um lambda permite acessar o erro antes de retornar a resposta.</span><span class="sxs-lookup"><span data-stu-id="5ea62-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="5ea62-144">Este é um exemplo de como usar um lambda para a manipulação de exceção:</span><span class="sxs-lookup"><span data-stu-id="5ea62-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="5ea62-145">No código anterior, `await context.Response.WriteAsync(new string(' ', 512));` é adicionado para que o navegador Internet Explorer exiba a mensagem de erro em vez de uma mensagem de erro do IE.</span><span class="sxs-lookup"><span data-stu-id="5ea62-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="5ea62-146">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="5ea62-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="5ea62-147">**Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes.</span><span class="sxs-lookup"><span data-stu-id="5ea62-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="5ea62-148">Fornecer erros é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="5ea62-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="5ea62-149">Para ver o resultado do lambda de tratamento de exceções no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use as diretivas de pré-processador `ProdEnvironment` e `ErrorHandlerLambda` e selecione **Disparar uma exceção** na página inicial.</span><span class="sxs-lookup"><span data-stu-id="5ea62-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="5ea62-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="5ea62-150">UseStatusCodePages</span></span>

<span data-ttu-id="5ea62-151">Por padrão, o aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status HTTP, como *404 - Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="5ea62-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="5ea62-152">O aplicativo retornar um código de status e um corpo de resposta vazio.</span><span class="sxs-lookup"><span data-stu-id="5ea62-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="5ea62-153">Use o middleware das Páginas de código de status para fornecer páginas de código de status.</span><span class="sxs-lookup"><span data-stu-id="5ea62-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="5ea62-154">O middleware é disponibilizado pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5ea62-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5ea62-155">Para habilitar os manipuladores padrão somente texto para os códigos de status de erros comuns, chame <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> no método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5ea62-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="5ea62-156">Chame `UseStatusCodePages` antes do middleware de tratamento da solicitação (por exemplo, o middleware de arquivos estáticos e o middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="5ea62-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="5ea62-157">Este é um exemplo de texto exibido pelos manipuladores padrão:</span><span class="sxs-lookup"><span data-stu-id="5ea62-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="5ea62-158">Para ver um dos diversos formatos de páginas de código de status no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use uma das diretivas de pré-processador que começam com `StatusCodePages` e selecione **Disparar um 404** na página inicial.</span><span class="sxs-lookup"><span data-stu-id="5ea62-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="5ea62-159">UseStatusCodePages com cadeia de caracteres de formato</span><span class="sxs-lookup"><span data-stu-id="5ea62-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="5ea62-160">Para personalizar o texto e o tipo de conteúdo de resposta, use uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva um tipo de conteúdo e uma cadeia de caracteres de formato:</span><span class="sxs-lookup"><span data-stu-id="5ea62-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="5ea62-161">UseStatusCodePages com lambda</span><span class="sxs-lookup"><span data-stu-id="5ea62-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="5ea62-162">Para especificar a manipulação de erro personalizada e o código de gravação de resposta, use a sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva uma expressão lambda:</span><span class="sxs-lookup"><span data-stu-id="5ea62-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="5ea62-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="5ea62-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="5ea62-164">O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="5ea62-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="5ea62-165">Envia um código de status *302 – Encontrado* ao cliente.</span><span class="sxs-lookup"><span data-stu-id="5ea62-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="5ea62-166">Redireciona o cliente para o local fornecido no modelo de URL.</span><span class="sxs-lookup"><span data-stu-id="5ea62-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="5ea62-167">O modelo de URL pode incluir um espaço reservado `{0}` para o código de status, conforme mostrado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="5ea62-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="5ea62-168">Se o modelo de URL começar com um til (~), este será substituído pelo `PathBase` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5ea62-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="5ea62-169">Se você apontar para um ponto de extremidade dentro do aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="5ea62-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="5ea62-170">Para obter um Razor exemplo de páginas, consulte *páginas/StatusCode. cshtml* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="5ea62-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="5ea62-171">Este método normalmente é usado quando o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5ea62-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="5ea62-172">Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro.</span><span class="sxs-lookup"><span data-stu-id="5ea62-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="5ea62-173">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.</span><span class="sxs-lookup"><span data-stu-id="5ea62-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="5ea62-174">Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.</span><span class="sxs-lookup"><span data-stu-id="5ea62-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="5ea62-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="5ea62-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="5ea62-176">O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="5ea62-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="5ea62-177">Retorna o código de status original ao cliente.</span><span class="sxs-lookup"><span data-stu-id="5ea62-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="5ea62-178">Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.</span><span class="sxs-lookup"><span data-stu-id="5ea62-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="5ea62-179">Se você apontar para um ponto de extremidade dentro do aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="5ea62-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="5ea62-180">Certifique-se `UseStatusCodePagesWithReExecute` de que é colocado antes `UseRouting` para que a solicitação possa ser redirecionada para a página de status.</span><span class="sxs-lookup"><span data-stu-id="5ea62-180">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="5ea62-181">Para obter um Razor exemplo de páginas, consulte *páginas/StatusCode. cshtml* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="5ea62-181">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="5ea62-182">Este método normalmente é usado quando o aplicativo tem que:</span><span class="sxs-lookup"><span data-stu-id="5ea62-182">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="5ea62-183">Processar a solicitação sem redirecionar para um ponto de extremidade diferente.</span><span class="sxs-lookup"><span data-stu-id="5ea62-183">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="5ea62-184">Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.</span><span class="sxs-lookup"><span data-stu-id="5ea62-184">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="5ea62-185">Preservar e retornar o código de status original com a resposta.</span><span class="sxs-lookup"><span data-stu-id="5ea62-185">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="5ea62-186">Os modelos de URL e cadeia de caracteres de consulta podem incluir um espaço reservado (`{0}`) para o código de status.</span><span class="sxs-lookup"><span data-stu-id="5ea62-186">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="5ea62-187">O modelo de URL deve começar com uma barra (`/`).</span><span class="sxs-lookup"><span data-stu-id="5ea62-187">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="5ea62-188">Ao usar um espaço reservado no caminho, verifique se o ponto de extremidade (página ou controlador) pode processar o segmento de linha.</span><span class="sxs-lookup"><span data-stu-id="5ea62-188">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="5ea62-189">Por exemplo, uma Razor página de erros deve aceitar o valor do segmento de caminho opcional com a `@page` diretiva:</span><span class="sxs-lookup"><span data-stu-id="5ea62-189">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="5ea62-190">O ponto de extremidade que processa o erro pode obter a URL original que gerou o erro, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5ea62-190">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="5ea62-191">Desabilitar páginas de código de status</span><span class="sxs-lookup"><span data-stu-id="5ea62-191">Disable status code pages</span></span>

<span data-ttu-id="5ea62-192">Para desabilitar páginas de código de status para um controlador MVC ou um método de ação, use o [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="5ea62-192">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="5ea62-193">Para desabilitar as páginas de código de status para solicitações específicas em um Razor método de manipulador de páginas ou em um controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="5ea62-193">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="5ea62-194">Código de tratamento de exceção</span><span class="sxs-lookup"><span data-stu-id="5ea62-194">Exception-handling code</span></span>

<span data-ttu-id="5ea62-195">Código em páginas de tratamento de exceção pode gerar exceções.</span><span class="sxs-lookup"><span data-stu-id="5ea62-195">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="5ea62-196">Geralmente, é uma boa ideia que páginas de erro de produção sejam compostas por conteúdo puramente estático.</span><span class="sxs-lookup"><span data-stu-id="5ea62-196">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="5ea62-197">Cabeçalhos de resposta</span><span class="sxs-lookup"><span data-stu-id="5ea62-197">Response headers</span></span>

<span data-ttu-id="5ea62-198">Depois que os cabeçalhos de resposta são enviados:</span><span class="sxs-lookup"><span data-stu-id="5ea62-198">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="5ea62-199">O aplicativo já não consegue alterar o código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="5ea62-199">The app can't change the response's status code.</span></span>
* <span data-ttu-id="5ea62-200">As páginas de exceção ou manipuladores não podem ser executados.</span><span class="sxs-lookup"><span data-stu-id="5ea62-200">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="5ea62-201">A resposta deve ser concluída ou a conexão será anulada.</span><span class="sxs-lookup"><span data-stu-id="5ea62-201">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="5ea62-202">Tratamento de exceções do servidor</span><span class="sxs-lookup"><span data-stu-id="5ea62-202">Server exception handling</span></span>

<span data-ttu-id="5ea62-203">Além da lógica de tratamento de exceção no aplicativo, a [implementação do servidor HTTP](xref:fundamentals/servers/index) pode lidar com algumas exceções.</span><span class="sxs-lookup"><span data-stu-id="5ea62-203">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="5ea62-204">Se o servidor capturar uma exceção antes que os cabeçalhos de resposta sejam enviados, o servidor enviará uma resposta *500 Erro Interno do Servidor* sem um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="5ea62-204">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="5ea62-205">Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão.</span><span class="sxs-lookup"><span data-stu-id="5ea62-205">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="5ea62-206">As solicitações que não são manipuladas pelo aplicativo são manipuladas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="5ea62-206">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="5ea62-207">Qualquer exceção que ocorrer quando o servidor estiver tratando a solicitação será tratada pelo tratamento de exceção do servidor.</span><span class="sxs-lookup"><span data-stu-id="5ea62-207">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="5ea62-208">As páginas de erro personalizadas do aplicativo, o middleware de tratamento de exceção e os filtros configurados não afetam esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="5ea62-208">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="5ea62-209">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="5ea62-209">Startup exception handling</span></span>

<span data-ttu-id="5ea62-210">Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5ea62-210">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="5ea62-211">O host pode ser configurado para [capturar erros de inicialização](xref:fundamentals/host/web-host#capture-startup-errors) e [capturar erros detalhados](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="5ea62-211">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="5ea62-212">A camada de hospedagem só poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta.</span><span class="sxs-lookup"><span data-stu-id="5ea62-212">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="5ea62-213">Se a associação falhar:</span><span class="sxs-lookup"><span data-stu-id="5ea62-213">If binding fails:</span></span>

* <span data-ttu-id="5ea62-214">A camada de hospedagem registrará uma exceção crítica.</span><span class="sxs-lookup"><span data-stu-id="5ea62-214">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="5ea62-215">O processo dotnet falhará.</span><span class="sxs-lookup"><span data-stu-id="5ea62-215">The dotnet process crashes.</span></span>
* <span data-ttu-id="5ea62-216">Nenhuma página de erro é exibida quando o servidor HTTP é [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="5ea62-216">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5ea62-217">Quando executado no [IIS](/iis) (ou no Serviço de Aplicativo do Azure) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="5ea62-217">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="5ea62-218">Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5ea62-218">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="5ea62-219">Página de erro do banco de dados</span><span class="sxs-lookup"><span data-stu-id="5ea62-219">Database error page</span></span>

<span data-ttu-id="5ea62-220">O middleware da página de erro do banco de dados captura as exceções relacionadas ao banco de dados que podem ser resolvidas usando Entity Framework migrações.</span><span class="sxs-lookup"><span data-stu-id="5ea62-220">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="5ea62-221">Quando estas exceções ocorrem, é gerada uma resposta HTML com detalhes das ações possíveis para resolver o problema.</span><span class="sxs-lookup"><span data-stu-id="5ea62-221">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="5ea62-222">Esta página só deve ser habilitada no Ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5ea62-222">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="5ea62-223">Habilite a página adicionando código a `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5ea62-223">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="5ea62-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requer o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="5ea62-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="5ea62-225">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="5ea62-225">Exception filters</span></span>

<span data-ttu-id="5ea62-226">Em aplicativos MVC, os filtros de exceção podem ser configurados globalmente, por controlador ou por ação.</span><span class="sxs-lookup"><span data-stu-id="5ea62-226">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="5ea62-227">Em Razor páginas aplicativos, eles podem ser configurados globalmente ou por modelo de página.</span><span class="sxs-lookup"><span data-stu-id="5ea62-227">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="5ea62-228">Esses filtros tratam qualquer exceção sem tratamento ocorrida durante a execução de uma ação do controlador ou de outro filtro.</span><span class="sxs-lookup"><span data-stu-id="5ea62-228">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="5ea62-229">Para obter mais informações, consulte <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="5ea62-229">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="5ea62-230">Os filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="5ea62-230">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="5ea62-231">É recomendável usar o middleware.</span><span class="sxs-lookup"><span data-stu-id="5ea62-231">We recommend using the middleware.</span></span> <span data-ttu-id="5ea62-232">Use filtros somente onde você precisar fazer o tratamento de erro de forma diferente com base na ação de MVC escolhida.</span><span class="sxs-lookup"><span data-stu-id="5ea62-232">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="5ea62-233">Erros de estado do modelo</span><span class="sxs-lookup"><span data-stu-id="5ea62-233">Model state errors</span></span>

<span data-ttu-id="5ea62-234">Confira informações sobre como lidar com erros de estado de modelo em [model binding](xref:mvc/models/model-binding) e [Validação de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5ea62-234">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5ea62-235">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5ea62-235">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
