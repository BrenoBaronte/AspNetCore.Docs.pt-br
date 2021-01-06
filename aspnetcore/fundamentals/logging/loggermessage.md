---
title: Registro em log de alto desempenho com o LoggerMessage no ASP.NET Core
author: rick-anderson
description: Saiba como usar o LoggerMessage para criar representantes que podem ser armazenados em cache e exigem menos alocações de objeto para cenários de registro em log de alto desempenho.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
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
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 0224e768bd0e016eac5165dc4d9745f4b0867094
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060450"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="c0a15-103">Registro em log de alto desempenho com o LoggerMessage no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0a15-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c0a15-104">Os recursos do <xref:Microsoft.Extensions.Logging.LoggerMessage> criam delegados armazenáveis em cache que exigem menos alocações de objeto e sobrecarga de computação reduzida em comparação aos [métodos de extensão do agente](xref:Microsoft.Extensions.Logging.LoggerExtensions), como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="c0a15-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="c0a15-105">Para cenários de registro em log de alto desempenho, use o padrão <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="c0a15-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="c0a15-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> fornece as seguintes vantagens de desempenho em relação aos métodos de extensão do Agente:</span><span class="sxs-lookup"><span data-stu-id="c0a15-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="c0a15-107">Métodos de extensão do agente exigem tipos de valor de conversão boxing, como `int`, em `object`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="c0a15-108">O padrão <xref:Microsoft.Extensions.Logging.LoggerMessage> evita a conversão boxing usando campos <xref:System.Action> estáticos e métodos de extensão com parâmetros fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="c0a15-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="c0a15-109">Os métodos de extensão do agente precisam analisar o modelo de mensagem (cadeia de caracteres de formato nomeada) sempre que uma mensagem de log é gravada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="c0a15-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> exige apenas a análise de um modelo uma vez quando a mensagem é definida.</span><span class="sxs-lookup"><span data-stu-id="c0a15-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="c0a15-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c0a15-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c0a15-112">O aplicativo de exemplo demonstra recursos do <xref:Microsoft.Extensions.Logging.LoggerMessage> com um sistema básico de acompanhamento de aspas.</span><span class="sxs-lookup"><span data-stu-id="c0a15-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="c0a15-113">O aplicativo adiciona e exclui aspas usando um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="c0a15-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="c0a15-114">Conforme ocorrem essas operações, são geradas mensagens de log usando o padrão <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="c0a15-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="c0a15-115">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="c0a15-115">LoggerMessage.Define</span></span>

<span data-ttu-id="c0a15-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) cria um delegado <xref:System.Action> para registrar uma mensagem em log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="c0a15-117">Sobrecargas de <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permitem passar até seis parâmetros de tipo para uma cadeia de caracteres de formato nomeada (modelo).</span><span class="sxs-lookup"><span data-stu-id="c0a15-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="c0a15-118">A cadeia de caracteres fornecida para o método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> é um modelo e não uma cadeia de caracteres interpolada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="c0a15-119">Os espaços reservados são preenchidos na ordem em que os tipos são especificados.</span><span class="sxs-lookup"><span data-stu-id="c0a15-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="c0a15-120">Os nomes do espaço reservado no modelo devem ser descritivos e consistentes em todos os modelos.</span><span class="sxs-lookup"><span data-stu-id="c0a15-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="c0a15-121">Eles servem como nomes de propriedade em dados de log estruturado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="c0a15-122">Recomendamos o uso da [formatação Pascal Case](/dotnet/standard/design-guidelines/capitalization-conventions) para nomes de espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="c0a15-123">Por exemplo, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="c0a15-124">Cada mensagem de log é uma <xref:System.Action> mantida em um campo estático criado por [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="c0a15-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="c0a15-125">Por exemplo, o aplicativo de exemplo cria um campo para descrever uma mensagem de log para uma solicitação GET para a página de Índice (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0a15-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="c0a15-126">Para a <xref:System.Action>, especifique:</span><span class="sxs-lookup"><span data-stu-id="c0a15-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="c0a15-127">O nível de log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-127">The log level.</span></span>
* <span data-ttu-id="c0a15-128">Um identificador de evento exclusivo (<xref:Microsoft.Extensions.Logging.EventId>) com o nome do método de extensão estático.</span><span class="sxs-lookup"><span data-stu-id="c0a15-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="c0a15-129">O modelo de mensagem (cadeia de caracteres de formato nomeada).</span><span class="sxs-lookup"><span data-stu-id="c0a15-129">The message template (named format string).</span></span> 

<span data-ttu-id="c0a15-130">Uma solicitação para a página de Índice do aplicativo de exemplo define:</span><span class="sxs-lookup"><span data-stu-id="c0a15-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="c0a15-131">O nível de log como `Information`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-131">Log level to `Information`.</span></span>
* <span data-ttu-id="c0a15-132">A ID do evento como `1` com o nome do método `IndexPageRequested`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="c0a15-133">Modelo de mensagem (cadeia de caracteres de formato nomeada) como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="c0a15-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="c0a15-134">Repositórios de log estruturado podem usar o nome do evento quando recebem a ID do evento para enriquecer o log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="c0a15-135">Por exemplo, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa o nome do evento.</span><span class="sxs-lookup"><span data-stu-id="c0a15-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="c0a15-136">A <xref:System.Action> é invocada por meio de um método de extensão fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="c0a15-137">O método `IndexPageRequested` registra uma mensagem para uma solicitação GET da página de Índice no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="c0a15-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="c0a15-138">`IndexPageRequested` é chamado no agente no método `OnGetAsync` em *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0a15-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="c0a15-139">Inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c0a15-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="c0a15-140">Para passar parâmetros para uma mensagem de log, defina até seis tipos ao criar o campo estático.</span><span class="sxs-lookup"><span data-stu-id="c0a15-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="c0a15-141">O aplicativo de exemplo registra uma cadeia de caracteres em log ao adicionar aspas definindo um tipo `string` para o campo <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="c0a15-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="c0a15-142">O modelo de mensagem de log do delegado recebe seus valores de espaço reservado dos tipos fornecidos.</span><span class="sxs-lookup"><span data-stu-id="c0a15-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="c0a15-143">O aplicativo de exemplo define um delegado para adicionar aspas quando o parâmetro de aspas é uma `string`:</span><span class="sxs-lookup"><span data-stu-id="c0a15-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="c0a15-144">O método de extensão estático para adicionar aspas, `QuoteAdded`, recebe o valor do argumento de aspas e passa-o para o delegado <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="c0a15-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="c0a15-145">No modelo de página da página de índice (*pages/index. cshtml. cs*), `QuoteAdded` é chamado para registrar a mensagem:</span><span class="sxs-lookup"><span data-stu-id="c0a15-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="c0a15-146">Inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c0a15-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="c0a15-147">O aplicativo de exemplo implementa um padrão [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para exclusão de cotação.</span><span class="sxs-lookup"><span data-stu-id="c0a15-147">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="c0a15-148">Uma mensagem informativa é registrada em log para uma operação de exclusão bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="c0a15-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="c0a15-149">Uma mensagem de erro é registrada em log para uma operação de exclusão quando uma exceção é gerada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="c0a15-150">A mensagem de log para a operação de exclusão sem êxito inclui o rastreamento de pilha da exceção (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0a15-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="c0a15-151">Observe como a exceção é passada para o delegado em `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="c0a15-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="c0a15-152">No modelo da página de Índice, uma exclusão de aspas bem-sucedida chama o método `QuoteDeleted` no agente.</span><span class="sxs-lookup"><span data-stu-id="c0a15-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="c0a15-153">Quando as aspas não são encontradas para exclusão, uma <xref:System.ArgumentNullException> é gerada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="c0a15-154">A exceção é interceptada pela instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) e registrada chamando o `QuoteDeleteFailed` método no agente de log no bloco [Catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="c0a15-154">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="c0a15-155">Quando as aspas forem excluídas com êxito, inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c0a15-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="c0a15-156">Quando a exclusão de aspas falha, inspecione a saída do console do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c0a15-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="c0a15-157">Observe que a exceção é incluída na mensagem de log:</span><span class="sxs-lookup"><span data-stu-id="c0a15-157">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="c0a15-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="c0a15-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="c0a15-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) cria um delegado <xref:System.Func%601> para definir um [escopo de log](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="c0a15-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="c0a15-160">Sobrecargas de <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permitem passar até três parâmetros de tipo para uma cadeia de caracteres de formato nomeada (modelo).</span><span class="sxs-lookup"><span data-stu-id="c0a15-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="c0a15-161">Como é o caso com o método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, a cadeia de caracteres fornecida ao método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> é um modelo e não uma cadeia de caracteres interpolada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="c0a15-162">Os espaços reservados são preenchidos na ordem em que os tipos são especificados.</span><span class="sxs-lookup"><span data-stu-id="c0a15-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="c0a15-163">Os nomes do espaço reservado no modelo devem ser descritivos e consistentes em todos os modelos.</span><span class="sxs-lookup"><span data-stu-id="c0a15-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="c0a15-164">Eles servem como nomes de propriedade em dados de log estruturado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="c0a15-165">Recomendamos o uso da [formatação Pascal Case](/dotnet/standard/design-guidelines/capitalization-conventions) para nomes de espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="c0a15-166">Por exemplo, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="c0a15-167">Defina um [escopo de log](xref:fundamentals/logging/index#log-scopes) a ser aplicado a uma série de mensagens de log usando o método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="c0a15-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="c0a15-168">O aplicativo de exemplo tem um botão **Limpar Tudo** para excluir todas as aspas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c0a15-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="c0a15-169">As aspas são excluídas com a remoção das aspas individualmente, uma por vez.</span><span class="sxs-lookup"><span data-stu-id="c0a15-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="c0a15-170">Sempre que aspas são excluídas, o método `QuoteDeleted` é chamado no agente.</span><span class="sxs-lookup"><span data-stu-id="c0a15-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="c0a15-171">Um escopo de log é adicionado a essas mensagens de log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="c0a15-172">Habilitar `IncludeScopes` na seção de agente do console do *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="c0a15-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="c0a15-173">Para criar um escopo de log, adicione um campo para conter um delegado <xref:System.Func%601> para o escopo.</span><span class="sxs-lookup"><span data-stu-id="c0a15-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="c0a15-174">O aplicativo de exemplo cria um campo chamado `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0a15-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="c0a15-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> para criar o delegado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="c0a15-176">Até três tipos podem ser especificados para uso como argumentos de modelo quando o delegado é invocado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="c0a15-177">O aplicativo de exemplo usa um modelo de mensagem que inclui o número de aspas excluídas (um tipo `int`):</span><span class="sxs-lookup"><span data-stu-id="c0a15-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="c0a15-178">Forneça um método de extensão estático para a mensagem de log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="c0a15-179">Inclua os parâmetros de tipo para propriedades nomeadas exibidos no modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="c0a15-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="c0a15-180">O aplicativo de exemplo usa uma `count` de aspas a ser excluída e retorna `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="c0a15-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="c0a15-181">O escopo encapsula as chamadas de extensão de log em um bloco [using](/dotnet/csharp/language-reference/keywords/using-statement):</span><span class="sxs-lookup"><span data-stu-id="c0a15-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="c0a15-182">Inspecione as mensagens de log na saída do console do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c0a15-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="c0a15-183">O seguinte resultado mostra três aspas excluídas com a mensagem de escopo de log incluída:</span><span class="sxs-lookup"><span data-stu-id="c0a15-183">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c0a15-184">Os recursos do <xref:Microsoft.Extensions.Logging.LoggerMessage> criam delegados armazenáveis em cache que exigem menos alocações de objeto e sobrecarga de computação reduzida em comparação aos [métodos de extensão do agente](xref:Microsoft.Extensions.Logging.LoggerExtensions), como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="c0a15-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="c0a15-185">Para cenários de registro em log de alto desempenho, use o padrão <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="c0a15-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="c0a15-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> fornece as seguintes vantagens de desempenho em relação aos métodos de extensão do Agente:</span><span class="sxs-lookup"><span data-stu-id="c0a15-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="c0a15-187">Métodos de extensão do agente exigem tipos de valor de conversão boxing, como `int`, em `object`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="c0a15-188">O padrão <xref:Microsoft.Extensions.Logging.LoggerMessage> evita a conversão boxing usando campos <xref:System.Action> estáticos e métodos de extensão com parâmetros fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="c0a15-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="c0a15-189">Os métodos de extensão do agente precisam analisar o modelo de mensagem (cadeia de caracteres de formato nomeada) sempre que uma mensagem de log é gravada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="c0a15-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> exige apenas a análise de um modelo uma vez quando a mensagem é definida.</span><span class="sxs-lookup"><span data-stu-id="c0a15-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="c0a15-191">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c0a15-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c0a15-192">O aplicativo de exemplo demonstra recursos do <xref:Microsoft.Extensions.Logging.LoggerMessage> com um sistema básico de acompanhamento de aspas.</span><span class="sxs-lookup"><span data-stu-id="c0a15-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="c0a15-193">O aplicativo adiciona e exclui aspas usando um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="c0a15-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="c0a15-194">Conforme ocorrem essas operações, são geradas mensagens de log usando o padrão <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="c0a15-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="c0a15-195">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="c0a15-195">LoggerMessage.Define</span></span>

<span data-ttu-id="c0a15-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) cria um delegado <xref:System.Action> para registrar uma mensagem em log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="c0a15-197">Sobrecargas de <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permitem passar até seis parâmetros de tipo para uma cadeia de caracteres de formato nomeada (modelo).</span><span class="sxs-lookup"><span data-stu-id="c0a15-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="c0a15-198">A cadeia de caracteres fornecida para o método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> é um modelo e não uma cadeia de caracteres interpolada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="c0a15-199">Os espaços reservados são preenchidos na ordem em que os tipos são especificados.</span><span class="sxs-lookup"><span data-stu-id="c0a15-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="c0a15-200">Os nomes do espaço reservado no modelo devem ser descritivos e consistentes em todos os modelos.</span><span class="sxs-lookup"><span data-stu-id="c0a15-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="c0a15-201">Eles servem como nomes de propriedade em dados de log estruturado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="c0a15-202">Recomendamos o uso da [formatação Pascal Case](/dotnet/standard/design-guidelines/capitalization-conventions) para nomes de espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="c0a15-203">Por exemplo, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="c0a15-204">Cada mensagem de log é uma <xref:System.Action> mantida em um campo estático criado por [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="c0a15-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="c0a15-205">Por exemplo, o aplicativo de exemplo cria um campo para descrever uma mensagem de log para uma solicitação GET para a página de Índice (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0a15-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="c0a15-206">Para a <xref:System.Action>, especifique:</span><span class="sxs-lookup"><span data-stu-id="c0a15-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="c0a15-207">O nível de log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-207">The log level.</span></span>
* <span data-ttu-id="c0a15-208">Um identificador de evento exclusivo (<xref:Microsoft.Extensions.Logging.EventId>) com o nome do método de extensão estático.</span><span class="sxs-lookup"><span data-stu-id="c0a15-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="c0a15-209">O modelo de mensagem (cadeia de caracteres de formato nomeada).</span><span class="sxs-lookup"><span data-stu-id="c0a15-209">The message template (named format string).</span></span> 

<span data-ttu-id="c0a15-210">Uma solicitação para a página de Índice do aplicativo de exemplo define:</span><span class="sxs-lookup"><span data-stu-id="c0a15-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="c0a15-211">O nível de log como `Information`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-211">Log level to `Information`.</span></span>
* <span data-ttu-id="c0a15-212">A ID do evento como `1` com o nome do método `IndexPageRequested`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="c0a15-213">Modelo de mensagem (cadeia de caracteres de formato nomeada) como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="c0a15-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="c0a15-214">Repositórios de log estruturado podem usar o nome do evento quando recebem a ID do evento para enriquecer o log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="c0a15-215">Por exemplo, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa o nome do evento.</span><span class="sxs-lookup"><span data-stu-id="c0a15-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="c0a15-216">A <xref:System.Action> é invocada por meio de um método de extensão fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="c0a15-217">O método `IndexPageRequested` registra uma mensagem para uma solicitação GET da página de Índice no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="c0a15-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="c0a15-218">`IndexPageRequested` é chamado no agente no método `OnGetAsync` em *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0a15-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="c0a15-219">Inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c0a15-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="c0a15-220">Para passar parâmetros para uma mensagem de log, defina até seis tipos ao criar o campo estático.</span><span class="sxs-lookup"><span data-stu-id="c0a15-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="c0a15-221">O aplicativo de exemplo registra uma cadeia de caracteres em log ao adicionar aspas definindo um tipo `string` para o campo <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="c0a15-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="c0a15-222">O modelo de mensagem de log do delegado recebe seus valores de espaço reservado dos tipos fornecidos.</span><span class="sxs-lookup"><span data-stu-id="c0a15-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="c0a15-223">O aplicativo de exemplo define um delegado para adicionar aspas quando o parâmetro de aspas é uma `string`:</span><span class="sxs-lookup"><span data-stu-id="c0a15-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="c0a15-224">O método de extensão estático para adicionar aspas, `QuoteAdded`, recebe o valor do argumento de aspas e passa-o para o delegado <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="c0a15-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="c0a15-225">No modelo de página da página de índice (*pages/index. cshtml. cs*), `QuoteAdded` é chamado para registrar a mensagem:</span><span class="sxs-lookup"><span data-stu-id="c0a15-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="c0a15-226">Inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c0a15-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="c0a15-227">O aplicativo de exemplo implementa um padrão [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para exclusão de cotação.</span><span class="sxs-lookup"><span data-stu-id="c0a15-227">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="c0a15-228">Uma mensagem informativa é registrada em log para uma operação de exclusão bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="c0a15-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="c0a15-229">Uma mensagem de erro é registrada em log para uma operação de exclusão quando uma exceção é gerada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="c0a15-230">A mensagem de log para a operação de exclusão sem êxito inclui o rastreamento de pilha da exceção (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0a15-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="c0a15-231">Observe como a exceção é passada para o delegado em `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="c0a15-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="c0a15-232">No modelo da página de Índice, uma exclusão de aspas bem-sucedida chama o método `QuoteDeleted` no agente.</span><span class="sxs-lookup"><span data-stu-id="c0a15-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="c0a15-233">Quando as aspas não são encontradas para exclusão, uma <xref:System.ArgumentNullException> é gerada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="c0a15-234">A exceção é interceptada pela instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) e registrada chamando o `QuoteDeleteFailed` método no agente de log no bloco [Catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="c0a15-234">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="c0a15-235">Quando as aspas forem excluídas com êxito, inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c0a15-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="c0a15-236">Quando a exclusão de aspas falha, inspecione a saída do console do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c0a15-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="c0a15-237">Observe que a exceção é incluída na mensagem de log:</span><span class="sxs-lookup"><span data-stu-id="c0a15-237">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="c0a15-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="c0a15-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="c0a15-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) cria um delegado <xref:System.Func%601> para definir um [escopo de log](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="c0a15-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="c0a15-240">Sobrecargas de <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permitem passar até três parâmetros de tipo para uma cadeia de caracteres de formato nomeada (modelo).</span><span class="sxs-lookup"><span data-stu-id="c0a15-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="c0a15-241">Como é o caso com o método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, a cadeia de caracteres fornecida ao método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> é um modelo e não uma cadeia de caracteres interpolada.</span><span class="sxs-lookup"><span data-stu-id="c0a15-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="c0a15-242">Os espaços reservados são preenchidos na ordem em que os tipos são especificados.</span><span class="sxs-lookup"><span data-stu-id="c0a15-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="c0a15-243">Os nomes do espaço reservado no modelo devem ser descritivos e consistentes em todos os modelos.</span><span class="sxs-lookup"><span data-stu-id="c0a15-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="c0a15-244">Eles servem como nomes de propriedade em dados de log estruturado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="c0a15-245">Recomendamos o uso da [formatação Pascal Case](/dotnet/standard/design-guidelines/capitalization-conventions) para nomes de espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="c0a15-246">Por exemplo, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="c0a15-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="c0a15-247">Defina um [escopo de log](xref:fundamentals/logging/index#log-scopes) a ser aplicado a uma série de mensagens de log usando o método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="c0a15-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="c0a15-248">O aplicativo de exemplo tem um botão **Limpar Tudo** para excluir todas as aspas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c0a15-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="c0a15-249">As aspas são excluídas com a remoção das aspas individualmente, uma por vez.</span><span class="sxs-lookup"><span data-stu-id="c0a15-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="c0a15-250">Sempre que aspas são excluídas, o método `QuoteDeleted` é chamado no agente.</span><span class="sxs-lookup"><span data-stu-id="c0a15-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="c0a15-251">Um escopo de log é adicionado a essas mensagens de log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="c0a15-252">Habilitar `IncludeScopes` na seção de agente do console do *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="c0a15-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="c0a15-253">Para criar um escopo de log, adicione um campo para conter um delegado <xref:System.Func%601> para o escopo.</span><span class="sxs-lookup"><span data-stu-id="c0a15-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="c0a15-254">O aplicativo de exemplo cria um campo chamado `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0a15-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="c0a15-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> para criar o delegado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="c0a15-256">Até três tipos podem ser especificados para uso como argumentos de modelo quando o delegado é invocado.</span><span class="sxs-lookup"><span data-stu-id="c0a15-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="c0a15-257">O aplicativo de exemplo usa um modelo de mensagem que inclui o número de aspas excluídas (um tipo `int`):</span><span class="sxs-lookup"><span data-stu-id="c0a15-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="c0a15-258">Forneça um método de extensão estático para a mensagem de log.</span><span class="sxs-lookup"><span data-stu-id="c0a15-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="c0a15-259">Inclua os parâmetros de tipo para propriedades nomeadas exibidos no modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="c0a15-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="c0a15-260">O aplicativo de exemplo usa uma `count` de aspas a ser excluída e retorna `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="c0a15-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="c0a15-261">O escopo encapsula as chamadas de extensão de log em um bloco [using](/dotnet/csharp/language-reference/keywords/using-statement):</span><span class="sxs-lookup"><span data-stu-id="c0a15-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="c0a15-262">Inspecione as mensagens de log na saída do console do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c0a15-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="c0a15-263">O seguinte resultado mostra três aspas excluídas com a mensagem de escopo de log incluída:</span><span class="sxs-lookup"><span data-stu-id="c0a15-263">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c0a15-264">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c0a15-264">Additional resources</span></span>

* [<span data-ttu-id="c0a15-265">Registro em log</span><span class="sxs-lookup"><span data-stu-id="c0a15-265">Logging</span></span>](xref:fundamentals/logging/index)
