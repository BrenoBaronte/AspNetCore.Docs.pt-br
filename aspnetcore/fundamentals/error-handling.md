---
title: Tratar erros no ASP.NET Core
author: rick-anderson
description: Descubra como tratar erros em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: 7bc21901fe1e9ddf604abf3b5bfecdb8a319f12c
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444104"
---
# <a name="handle-errors-in-aspnet-core"></a>Tratar erros no ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)

Este artigo aborda abordagens comuns para lidar com erros em ASP.NET Core aplicativos Web. Consulte <xref:web-api/handle-errors> para APIs da Web.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Como baixar](xref:index#how-to-download-a-sample).) O artigo inclui instruções sobre como definir diretivas de pré-processador ( `#if` , `#endif` , `#define` ) no aplicativo de exemplo para habilitar cenários diferentes.

## <a name="developer-exception-page"></a>Página de exceção do desenvolvedor

A *Página de exceção do desenvolvedor* exibe informações detalhadas sobre as exceções de solicitação. A página é disponibilizada pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Adicione código ao método `Startup.Configure` para habilitar a página quando o aplicativo estiver sendo executado no [ambiente](xref:fundamentals/environments) de desenvolvimento:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Coloque a chamada para <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> antes de qualquer middleware no qual você deseja capturar exceções.

> [!WARNING]
> Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**. Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção. Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.

A página inclui as seguintes informações sobre a exceção e a solicitação:

* Rastreamento de pilha
* Parâmetros de cadeia de caracteres de consulta (se houver algum)
* Cookies (se houver algum)
* Cabeçalhos

Para ver a Página de exceção do desenvolvedor no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use a diretiva de pré-processador `DevEnvironment` e selecione **Disparar uma exceção** na página inicial.

## <a name="exception-handler-page"></a>Página do Manipulador de exceção

Use o Middleware de tratamento de exceção para configurar uma página personalizada de tratamento de erro para o ambiente de produção. O middleware:

* Captura e registra em log as exceções.
* Executa novamente a solicitação em um pipeline alternativo para a página ou controlador indicado. A solicitação não será executada novamente se a resposta tiver sido iniciada.

No exemplo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> inclui o Middleware de Manipulação de Exceções em ambientes que não são de desenvolvimento:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

O Razor modelo de aplicativo pages fornece uma página de erro (*. cshtml*) e uma <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) na pasta *páginas* . Para um aplicativo MVC, o modelo de projeto inclui um Método de ação de erro e uma Exibição de erro. Este é o método de ação:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Não marque o método de ação do manipulador de erros com atributos do método HTTP, como `HttpGet` . Verbos explícitos impedem algumas solicitações de chegar ao método. Permita acesso anônimo ao método para que os usuários não autenticados possam capazes receber a exibição de erro.

### <a name="access-the-exception"></a>Acessar a exceção

Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acessar a exceção e o caminho de solicitação original em uma página ou controlador de manipulador de erro:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Não** forneça informações de erro confidenciais aos clientes. Fornecer erros é um risco à segurança.

Para ver a página de tratamento de exceções no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use as diretivas de pré-processador `ProdEnvironment` e `ErrorHandlerPage` e selecione **Disparar uma exceção** na página inicial.

## <a name="exception-handler-lambda"></a>Lambda do Manipulador de exceção

Uma alternativa a uma [página personalizada de manipulador de exceção](#exception-handler-page) é fornecer um lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Usar um lambda permite acessar o erro antes de retornar a resposta.

Este é um exemplo de como usar um lambda para a manipulação de exceção:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

No código anterior, `await context.Response.WriteAsync(new string(' ', 512));` é adicionado para que o navegador Internet Explorer exiba a mensagem de erro em vez de uma mensagem de erro do IE. Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes. Fornecer erros é um risco à segurança.

Para ver o resultado do lambda de tratamento de exceções no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use as diretivas de pré-processador `ProdEnvironment` e `ErrorHandlerLambda` e selecione **Disparar uma exceção** na página inicial.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Por padrão, o aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status HTTP, como *404 - Não Encontrado*. O aplicativo retornar um código de status e um corpo de resposta vazio. Use o middleware das Páginas de código de status para fornecer páginas de código de status.

O middleware é disponibilizado pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Para habilitar os manipuladores padrão somente texto para os códigos de status de erros comuns, chame <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> no método `Startup.Configure`:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Chame `UseStatusCodePages` antes do middleware de tratamento da solicitação (por exemplo, o middleware de arquivos estáticos e o middleware MVC).

Este é um exemplo de texto exibido pelos manipuladores padrão:

```
Status Code: 404; Not Found
```

Para ver um dos diversos formatos de páginas de código de status no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use uma das diretivas de pré-processador que começam com `StatusCodePages` e selecione **Disparar um 404** na página inicial.

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages com cadeia de caracteres de formato

Para personalizar o texto e o tipo de conteúdo de resposta, use uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva um tipo de conteúdo e uma cadeia de caracteres de formato:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages com lambda

Para especificar a manipulação de erro personalizada e o código de gravação de resposta, use a sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva uma expressão lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:

* Envia um código de status *302 – Encontrado* ao cliente.
* Redireciona o cliente para o local fornecido no modelo de URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

O modelo de URL pode incluir um espaço reservado `{0}` para o código de status, conforme mostrado no exemplo. Se o modelo de URL começar com um til (~), este será substituído pelo `PathBase` do aplicativo. Se você apontar para um ponto de extremidade dentro do aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade. Para obter um Razor exemplo de páginas, consulte *páginas/StatusCode. cshtml* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método normalmente é usado quando o aplicativo:

* Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro. Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.
* Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:

* Retorna o código de status original ao cliente.
* Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Se você apontar para um ponto de extremidade dentro do aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade. Certifique-se `UseStatusCodePagesWithReExecute` de que é colocado antes `UseRouting` para que a solicitação possa ser redirecionada para a página de status. Para obter um Razor exemplo de páginas, consulte *páginas/StatusCode. cshtml* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método normalmente é usado quando o aplicativo tem que:

* Processar a solicitação sem redirecionar para um ponto de extremidade diferente. Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.
* Preservar e retornar o código de status original com a resposta.

Os modelos de URL e cadeia de caracteres de consulta podem incluir um espaço reservado (`{0}`) para o código de status. O modelo de URL deve começar com uma barra (`/`). Ao usar um espaço reservado no caminho, verifique se o ponto de extremidade (página ou controlador) pode processar o segmento de linha. Por exemplo, uma Razor página de erros deve aceitar o valor do segmento de caminho opcional com a `@page` diretiva:

```cshtml
@page "{code?}"
```

O ponto de extremidade que processa o erro pode obter a URL original que gerou o erro, conforme mostrado no exemplo a seguir:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Desabilitar páginas de código de status

Para desabilitar páginas de código de status para um controlador MVC ou um método de ação, use o [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atributo.

Para desabilitar as páginas de código de status para solicitações específicas em um Razor método de manipulador de páginas ou em um controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Código de tratamento de exceção

Código em páginas de tratamento de exceção pode gerar exceções. Geralmente, é uma boa ideia que páginas de erro de produção sejam compostas por conteúdo puramente estático.

### <a name="response-headers"></a>Cabeçalhos de resposta

Depois que os cabeçalhos de resposta são enviados:

* O aplicativo já não consegue alterar o código de status da resposta.
* As páginas de exceção ou manipuladores não podem ser executados. A resposta deve ser concluída ou a conexão será anulada.

## <a name="server-exception-handling"></a>Tratamento de exceções do servidor

Além da lógica de tratamento de exceção no aplicativo, a [implementação do servidor HTTP](xref:fundamentals/servers/index) pode lidar com algumas exceções. Se o servidor capturar uma exceção antes que os cabeçalhos de resposta sejam enviados, o servidor enviará uma resposta *500 Erro Interno do Servidor* sem um corpo de resposta. Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão. As solicitações que não são manipuladas pelo aplicativo são manipuladas pelo servidor. Qualquer exceção que ocorrer quando o servidor estiver tratando a solicitação será tratada pelo tratamento de exceção do servidor. As páginas de erro personalizadas do aplicativo, o middleware de tratamento de exceção e os filtros configurados não afetam esse comportamento.

## <a name="startup-exception-handling"></a>Tratamento de exceção na inicialização

Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo. O host pode ser configurado para [capturar erros de inicialização](xref:fundamentals/host/web-host#capture-startup-errors) e [capturar erros detalhados](xref:fundamentals/host/web-host#detailed-errors).

A camada de hospedagem só poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta. Se a associação falhar:

* A camada de hospedagem registrará uma exceção crítica.
* O processo dotnet falhará.
* Nenhuma página de erro é exibida quando o servidor HTTP é [Kestrel](xref:fundamentals/servers/kestrel).

Quando executado no [IIS](/iis) (ou no Serviço de Aplicativo do Azure) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Página de erro do banco de dados

O middleware da página de erro do banco de dados captura as exceções relacionadas ao banco de dados que podem ser resolvidas usando Entity Framework migrações. Quando estas exceções ocorrem, é gerada uma resposta HTML com detalhes das ações possíveis para resolver o problema. Esta página só deve ser habilitada no Ambiente de desenvolvimento. Habilite a página adicionando código a `Startup.Configure`:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>requer o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtros de exceção

Em aplicativos MVC, os filtros de exceção podem ser configurados globalmente, por controlador ou por ação. Em Razor páginas aplicativos, eles podem ser configurados globalmente ou por modelo de página. Esses filtros tratam qualquer exceção sem tratamento ocorrida durante a execução de uma ação do controlador ou de outro filtro. Para obter mais informações, consulte <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Os filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de tratamento de exceção. É recomendável usar o middleware. Use filtros somente onde você precisar fazer o tratamento de erro de forma diferente com base na ação de MVC escolhida.

## <a name="model-state-errors"></a>Erros de estado do modelo

Confira informações sobre como lidar com erros de estado de modelo em [model binding](xref:mvc/models/model-binding) e [Validação de modelos](xref:mvc/models/validation).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
