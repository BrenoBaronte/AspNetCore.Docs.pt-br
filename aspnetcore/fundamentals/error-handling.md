---
title: Tratar erros no ASP.NET Core
author: ardalis
description: Descubra como tratar erros em aplicativos ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 07/05/2018
uid: fundamentals/error-handling
ms.openlocfilehash: d7e60c0f615841461a17b093bffe5fb3f82f8616
ms.sourcegitcommit: 506a199274e9fe5fb4070b273ba94f29f14cb619
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2018
ms.locfileid: "39332269"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="4f343-103">Tratar erros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f343-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="4f343-104">Por [Steve Smith](https://ardalis.com/) e [Tom Dykstra](https://github.com/tdykstra/)</span><span class="sxs-lookup"><span data-stu-id="4f343-104">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra/)</span></span>

<span data-ttu-id="4f343-105">Este artigo apresenta abordagens comuns para o tratamento de erros em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f343-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="4f343-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/ErrorHandlingSample) ([como baixar](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4f343-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/ErrorHandlingSample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="the-developer-exception-page"></a><span data-ttu-id="4f343-107">A página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="4f343-107">The developer exception page</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4f343-108">Para configurar um aplicativo para exibir uma página que mostra informações detalhadas sobre exceções, use a *página de exceção do desenvolvedor*.</span><span class="sxs-lookup"><span data-stu-id="4f343-108">To configure an app to display a page that shows detailed information about exceptions, use the *developer exception page*.</span></span> <span data-ttu-id="4f343-109">A página é disponibilizada pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está disponível no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="4f343-109">The page made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="4f343-110">Adicione uma linha ao método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4f343-110">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="4f343-111">Para configurar um aplicativo para exibir uma página que mostra informações detalhadas sobre exceções, use a *página de exceção do desenvolvedor*.</span><span class="sxs-lookup"><span data-stu-id="4f343-111">To configure an app to display a page that shows detailed information about exceptions, use the *developer exception page*.</span></span> <span data-ttu-id="4f343-112">A página é disponibilizada pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está disponível no [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="4f343-112">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="4f343-113">Adicione uma linha ao método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4f343-113">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="4f343-114">Para configurar um aplicativo para exibir uma página que mostra informações detalhadas sobre exceções, use a *página de exceção do desenvolvedor*.</span><span class="sxs-lookup"><span data-stu-id="4f343-114">To configure an app to display a page that shows detailed information about exceptions, use the *developer exception page*.</span></span> <span data-ttu-id="4f343-115">A página é disponibilizada adicionando uma referência de pacote para o pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="4f343-115">The page is made available by adding a package reference for the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package in the project file.</span></span> <span data-ttu-id="4f343-116">Adicione uma linha ao método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4f343-116">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

<span data-ttu-id="4f343-117">Coloque a chamada para [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) na frente de qualquer middleware no qual você deseja capturar exceções, tais como `app.UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="4f343-117">Place the call to [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) in front of any middleware where you want to catch exceptions, such as `app.UseMvc`.</span></span>

>[!WARNING]
> <span data-ttu-id="4f343-118">Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de Desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="4f343-118">Enable the developer exception page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="4f343-119">Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção.</span><span class="sxs-lookup"><span data-stu-id="4f343-119">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="4f343-120">[Saiba mais sobre como configurar ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="4f343-120">[Learn more about configuring environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4f343-121">Para ver a página de exceção do desenvolvedor, execute o aplicativo de exemplo com o ambiente definido como `Development` e adicione `?throw=true` à URL base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4f343-121">To see the developer exception page, run the sample app with the environment set to `Development`, and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="4f343-122">A página inclui várias guias com informações sobre a exceção e a solicitação.</span><span class="sxs-lookup"><span data-stu-id="4f343-122">The page includes several tabs with information about the exception and the request.</span></span> <span data-ttu-id="4f343-123">A primeira guia inclui um rastreamento de pilha:</span><span class="sxs-lookup"><span data-stu-id="4f343-123">The first tab includes a stack trace:</span></span>

![Rastreamento de pilha](error-handling/_static/developer-exception-page.png)

<span data-ttu-id="4f343-125">A próxima guia mostra os parâmetros da cadeia de caracteres de consulta, se houver:</span><span class="sxs-lookup"><span data-stu-id="4f343-125">The next tab shows the query string parameters, if any:</span></span>

![Parâmetros de cadeia de caracteres de consulta](error-handling/_static/developer-exception-page-query.png)

<span data-ttu-id="4f343-127">Se a solicitação tem cookies, eles serão exibidos na guia **Cookies**. Os cabeçalhos são vistos na última guia:</span><span class="sxs-lookup"><span data-stu-id="4f343-127">If the request has cookies, they appear on the **Cookies** tab. Headers are seen in the last tab:</span></span>

![Cabeçalhos](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a><span data-ttu-id="4f343-129">Configurando uma página de tratamento de exceção personalizada</span><span class="sxs-lookup"><span data-stu-id="4f343-129">Configuring a custom exception handling page</span></span>

<span data-ttu-id="4f343-130">Configure uma página do manipulador de exceção para usar quando o aplicativo não estiver em execução no ambiente `Development`:</span><span class="sxs-lookup"><span data-stu-id="4f343-130">Configure an exception handler page to use when the app isn't running in the `Development` environment:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

<span data-ttu-id="4f343-131">Em um aplicativo Razor Pages, o modelo [dotnet novo](/dotnet/core/tools/dotnet-new) do Razor Pages fornece uma de erro e a classe de modelo de página `ErrorModel` na pasta *Páginas*.</span><span class="sxs-lookup"><span data-stu-id="4f343-131">In a Razor Pages app, the [dotnet new](/dotnet/core/tools/dotnet-new) Razor Pages template provides an Error page and `ErrorModel` page model class in the *Pages* folder.</span></span>

<span data-ttu-id="4f343-132">Em um aplicativo MVC, não decore o método de ação do manipulador de erro com atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="4f343-132">In an MVC app, don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="4f343-133">Verbos explícitos impedem algumas solicitações de chegar ao método.</span><span class="sxs-lookup"><span data-stu-id="4f343-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="4f343-134">Permita acesso anônimo ao método para que os usuários não autenticados possam capazes receber a exibição de erro.</span><span class="sxs-lookup"><span data-stu-id="4f343-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

<span data-ttu-id="4f343-135">Por exemplo, o seguinte método de manipulador de erro é fornecido pelo modelo MVC [dotnet novo](/dotnet/core/tools/dotnet-new) e aparece no controlador Home:</span><span class="sxs-lookup"><span data-stu-id="4f343-135">For example, the following error handler method is provided by the [dotnet new](/dotnet/core/tools/dotnet-new) MVC template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

## <a name="configuring-status-code-pages"></a><span data-ttu-id="4f343-136">Configurando páginas de código de status</span><span class="sxs-lookup"><span data-stu-id="4f343-136">Configuring status code pages</span></span>

<span data-ttu-id="4f343-137">Por padrão, o aplicativo não fornece uma página de código de status detalhada para códigos de status HTTP, como *404 Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="4f343-137">By default, an app doesn't provide a rich status code page for HTTP status codes, such as *404 Not Found*.</span></span> <span data-ttu-id="4f343-138">Para fornecer páginas de código de status, configure o middleware de páginas de código de status, adicionando uma linha ao método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4f343-138">To provide status code pages, configure the Status Code Pages Middleware by adding a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="4f343-139">Por padrão, o middleware de páginas de código de status adiciona manipuladores, somente de texto, para os códigos de status comuns, como o 404:</span><span class="sxs-lookup"><span data-stu-id="4f343-139">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as 404:</span></span>

![Página 404](error-handling/_static/default-404-status-code.png)

<span data-ttu-id="4f343-141">O middleware permite vários métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="4f343-141">The middleware supports several extension methods.</span></span> <span data-ttu-id="4f343-142">Um método usa uma expressão lambda:</span><span class="sxs-lookup"><span data-stu-id="4f343-142">One method takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="4f343-143">Outro método usa uma cadeia de caracteres de formato e de tipo de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="4f343-143">Another method takes a content type and format string:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

<span data-ttu-id="4f343-144">Há também métodos de extensão de redirecionamento e de nova execução.</span><span class="sxs-lookup"><span data-stu-id="4f343-144">There are also redirect and re-execute extension methods.</span></span> <span data-ttu-id="4f343-145">O método de redirecionamento envia um código de status *302 encontrado* para o cliente e o redireciona para o modelo de URL do local fornecido.</span><span class="sxs-lookup"><span data-stu-id="4f343-145">The redirect method sends a *302 Found* status code to the client and redirects the client to the provided location URL template.</span></span> <span data-ttu-id="4f343-146">O modelo pode incluir um espaço reservado de `{0}` para o código de status.</span><span class="sxs-lookup"><span data-stu-id="4f343-146">The template may include a `{0}` placeholder for the status code.</span></span> <span data-ttu-id="4f343-147">As URLs que começam com `~` têm o caminho base anexado.</span><span class="sxs-lookup"><span data-stu-id="4f343-147">URLs starting with `~` have the base path prepended.</span></span> <span data-ttu-id="4f343-148">Uma URL que não começa com `~` é usada como está.</span><span class="sxs-lookup"><span data-stu-id="4f343-148">A URL that doesn't start with `~` is used as is.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="4f343-149">O método de nova execução retorna o código de status original ao cliente e especifica que o corpo da resposta deve ser gerado executando novamente o pipeline de solicitação, utilizando um caminho alternativo.</span><span class="sxs-lookup"><span data-stu-id="4f343-149">The re-execute method returns the original status code to the client and specifies that the response body should be generated by re-executing the request pipeline using an alternate path.</span></span> <span data-ttu-id="4f343-150">Esse caminho pode conter um espaço reservado de `{0}` para o código de status:</span><span class="sxs-lookup"><span data-stu-id="4f343-150">This path may contain a `{0}` placeholder for the status code:</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

<span data-ttu-id="4f343-151">As páginas de código de status podem ser desabilitadas para solicitações específicas em um método de manipulador de Páginas Razor ou em um controlador do MVC.</span><span class="sxs-lookup"><span data-stu-id="4f343-151">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="4f343-152">Para desabilitar as páginas de código de status, tente recuperar o [IStatusCodePagesFeature](/dotnet/api/microsoft.aspnetcore.diagnostics.istatuscodepagesfeature) da coleção [HttpContext.Features](/dotnet/api/microsoft.aspnetcore.http.httpcontext.features) da solicitação e desabilitar o recurso se ele estiver disponível:</span><span class="sxs-lookup"><span data-stu-id="4f343-152">To disable status code pages, attempt to retrieve the [IStatusCodePagesFeature](/dotnet/api/microsoft.aspnetcore.diagnostics.istatuscodepagesfeature) from the request's [HttpContext.Features](/dotnet/api/microsoft.aspnetcore.http.httpcontext.features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="4f343-153">Se você está usando uma sobrecarga `UseStatusCodePages*` que aponta para um ponto de extremidade dentro do aplicativo, crie um modo de exibição do MVC ou Razor Page para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="4f343-153">If using a `UseStatusCodePages*` overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="4f343-154">Por exemplo, o modelo [dotnet novo](/dotnet/core/tools/dotnet-new) para um aplicativo Razor Pages produz a seguinte página e classe de modelo de página:</span><span class="sxs-lookup"><span data-stu-id="4f343-154">For example, the [dotnet new](/dotnet/core/tools/dotnet-new) template for a Razor Pages app produces the following page and page model class:</span></span>

<span data-ttu-id="4f343-155">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4f343-155">*Error.cshtml*:</span></span>

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed 
    information about the error that occurred.
</p>
<p>
    <strong>Development environment should not be enabled in deployed applications
    </strong>, as it can result in sensitive information from exceptions being 
    displayed to end users. For local debugging, development environment can be 
    enabled by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment 
    variable to <strong>Development</strong>, and restarting the application.
</p>
```

<span data-ttu-id="4f343-156">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f343-156">*Error.cshtml.cs*:</span></span>

```csharp
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, 
        NoStore = true)]
    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="4f343-157">Código de tratamento de exceção</span><span class="sxs-lookup"><span data-stu-id="4f343-157">Exception-handling code</span></span>

<span data-ttu-id="4f343-158">Código em páginas de tratamento de exceção pode gerar exceções.</span><span class="sxs-lookup"><span data-stu-id="4f343-158">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="4f343-159">Geralmente, é uma boa ideia que páginas de erro de produção sejam compostas por conteúdo puramente estático.</span><span class="sxs-lookup"><span data-stu-id="4f343-159">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="4f343-160">Além disso, esteja ciente de que, depois que os cabeçalhos de uma resposta forem enviados, você não poderá alterar o código de status da resposta e nenhuma página de exceção ou manipulador poderá ser executado.</span><span class="sxs-lookup"><span data-stu-id="4f343-160">Also, be aware that once the headers for a response have been sent, you can't change the response's status code, nor can any exception pages or handlers run.</span></span> <span data-ttu-id="4f343-161">A resposta deve ser concluída ou a conexão será anulada.</span><span class="sxs-lookup"><span data-stu-id="4f343-161">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="4f343-162">Tratamento de exceções do servidor</span><span class="sxs-lookup"><span data-stu-id="4f343-162">Server exception handling</span></span>

<span data-ttu-id="4f343-163">Além da lógica de tratamento de exceção no aplicativo, o [servidor](xref:fundamentals/servers/index) que hospeda o aplicativo executa uma parte do tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="4f343-163">In addition to the exception handling logic in your app, the [server](xref:fundamentals/servers/index) hosting your app performs some exception handling.</span></span> <span data-ttu-id="4f343-164">Se o servidor capturar uma exceção antes que os cabeçalhos sejam enviados, o servidor enviará uma resposta *500 Erro Interno do Servidor* sem corpo.</span><span class="sxs-lookup"><span data-stu-id="4f343-164">If the server catches an exception before the headers are sent, the server sends a *500 Internal Server Error* response with no body.</span></span> <span data-ttu-id="4f343-165">Se o servidor capturar uma exceção depois que os cabeçalhos forem enviados, o servidor fechará a conexão.</span><span class="sxs-lookup"><span data-stu-id="4f343-165">If the server catches an exception after the headers have been sent, the server closes the connection.</span></span> <span data-ttu-id="4f343-166">As solicitações que não são manipuladas pelo aplicativo são manipuladas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="4f343-166">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="4f343-167">Qualquer exceção ocorrida é tratada pelo tratamento de exceção do servidor.</span><span class="sxs-lookup"><span data-stu-id="4f343-167">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="4f343-168">As páginas de erro personalizadas, o middleware de tratamento de exceção ou os filtros configurados não afetam esse comportamento.</span><span class="sxs-lookup"><span data-stu-id="4f343-168">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="4f343-169">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="4f343-169">Startup exception handling</span></span>

<span data-ttu-id="4f343-170">Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4f343-170">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="4f343-171">Usando o [Host da Web](xref:fundamentals/host/web-host), você pode [configurar como o host se comporta em resposta a erros durante a inicialização](xref:fundamentals/host/web-host#detailed-errors) usando as chaves `captureStartupErrors` e `detailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="4f343-171">Using the [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="4f343-172">A hospedagem apenas poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta.</span><span class="sxs-lookup"><span data-stu-id="4f343-172">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="4f343-173">Se alguma associação falhar por algum motivo, a camada de hospedagem registrará uma exceção crítica em log, o processo do dotnet falhará e nenhuma página de erro será exibida quando o aplicativo estiver sendo executado no servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="4f343-173">If any binding fails for any reason, the hosting layer logs a critical exception, the dotnet process crashes, and no error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="4f343-174">Quando executado no [IIS](/iis) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) se o processo não puder ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="4f343-174">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 Process Failure* is returned by the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) if the process can't be started.</span></span> <span data-ttu-id="4f343-175">Para obter informações sobre como solucionar problemas de inicialização ao hospedar com o IIS, consulte <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="4f343-175">For information on troubleshooting startup issues when hosting with IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="4f343-176">Para obter informações sobre como solucionar problemas de inicialização com o Serviço de Aplicativo do Azure, consulte <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="4f343-176">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-mvc-error-handling"></a><span data-ttu-id="4f343-177">Tratamento de erro do ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="4f343-177">ASP.NET MVC error handling</span></span>

<span data-ttu-id="4f343-178">Os aplicativos [MVC](xref:mvc/overview) contêm algumas opções adicionais para o tratamento de erros, como configurar filtros de exceção e executar a validação do modelo.</span><span class="sxs-lookup"><span data-stu-id="4f343-178">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="4f343-179">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="4f343-179">Exception filters</span></span>

<span data-ttu-id="4f343-180">Os filtros de exceção podem ser configurados globalmente ou por controlador ou por ação em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="4f343-180">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="4f343-181">Esses filtros tratam qualquer exceção sem tratamento ocorrida durante a execução de uma ação do controlador ou de outro filtro.</span><span class="sxs-lookup"><span data-stu-id="4f343-181">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="4f343-182">Esses filtros não são chamados de outra forma.</span><span class="sxs-lookup"><span data-stu-id="4f343-182">These filters aren't called otherwise.</span></span> <span data-ttu-id="4f343-183">Para saber mais, consulte [Filtros](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="4f343-183">To learn more, see [Filters](xref:mvc/controllers/filters).</span></span>

> [!TIP]
> <span data-ttu-id="4f343-184">Filtros de exceção são bons para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="4f343-184">Exception filters are good for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="4f343-185">Dê preferência ao uso de middleware no geral e use filtros apenas quando precisar fazer o tratamento de erro de modo *diferente*, dependendo da ação do MVC escolhida.</span><span class="sxs-lookup"><span data-stu-id="4f343-185">Generally prefer the use of middleware, and use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handling-model-state-errors"></a><span data-ttu-id="4f343-186">Tratando erros do estado do modelo</span><span class="sxs-lookup"><span data-stu-id="4f343-186">Handling model state errors</span></span>

<span data-ttu-id="4f343-187">A [validação do modelo](xref:mvc/models/validation) ocorre antes da invocação de cada ação do controlador e é responsabilidade do método de ação inspecionar `ModelState.IsValid` e responder de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="4f343-187">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span>

<span data-ttu-id="4f343-188">Alguns aplicativos optam por seguir uma convenção padrão para lidar com erros de validação do modelo, caso em que um [filtro](xref:mvc/controllers/filters) pode ser um local adequado para implementar uma política como essa.</span><span class="sxs-lookup"><span data-stu-id="4f343-188">Some apps choose to follow a standard convention for dealing with model validation errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="4f343-189">É necessário testar o comportamento das ações com estados de modelo inválidos.</span><span class="sxs-lookup"><span data-stu-id="4f343-189">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="4f343-190">Saiba mais em [Testar a lógica do controlador](xref:mvc/controllers/testing).</span><span class="sxs-lookup"><span data-stu-id="4f343-190">Learn more in [Test controller logic](xref:mvc/controllers/testing).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f343-191">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4f343-191">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
