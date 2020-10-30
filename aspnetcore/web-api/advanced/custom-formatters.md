---
title: Formatadores personalizados na API Web ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar formatadores personalizados para APIs Web no ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: e4d73fdc0db3faeace5d68b3d71718315e68cae3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058916"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Formatadores personalizados na API Web ASP.NET Core

Por [Kirk Larkin](https://twitter.com/serpent5) e [Tom Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC dá suporte à troca de dados em APIs Web usando formatadores de entrada e saída. Os formatadores de entrada são usados pela [Associação de modelo](xref:mvc/models/model-binding). Os formatadores de saída são usados para [Formatar respostas](xref:web-api/advanced/formatting).

A estrutura fornece formatadores de entrada e saída internos para JSON e XML. Ele fornece um formatador de saída interno para texto sem formatação, mas não fornece um formatador de entrada para texto sem formatação.

Este artigo mostra como adicionar suporte para formatos adicionais criando formatadores personalizados. Para obter um exemplo de um formatador de entrada de texto sem formatação personalizado, consulte [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) no github.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Quando usar formatadores personalizados

Use um formatador personalizado para adicionar suporte a um tipo de conteúdo que não é tratado pelos formatadores internos.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Visão geral de como usar um formatador personalizado

Para criar um formatador personalizado:

* Para serializar dados enviados ao cliente, crie uma classe de formatador de saída.
* Para desserializar os dados recebidos do cliente, crie uma classe de formatador de entrada.
* Adicione instâncias de classes de formatador `InputFormatters` às `OutputFormatters` coleções e no <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

## <a name="how-to-create-a-custom-formatter-class"></a>Como criar uma classe de formatador personalizado

Para criar um formatador:

* Derive a classe da classe base apropriada. O aplicativo de exemplo deriva de <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> e <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Especifique codificações e tipos de mídia válidos no construtor.
* Substitua os métodos <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> e <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.
* Substitua os métodos <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> e `WriteResponseBodyAsync`.

O código a seguir mostra a `VcardOutputFormatter` classe do [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Derivar da classe base apropriada

Para tipos de mídia de texto (por exemplo, vCard), derive da <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> classe ou base.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

Para tipos binários, derive <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> da <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> classe ou base.

### <a name="specify-valid-media-types-and-encodings"></a>Especifique codificações e tipos de mídia válidos

No construtor, especifique codificações e tipos de mídia válidos adicionando-os às coleções `SupportedMediaTypes` e `SupportedEncodings`.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

Uma classe de formatador **não** pode usar injeção de construtor para suas dependências. Por exemplo, `ILogger<VcardOutputFormatter>` não pode ser adicionado como um parâmetro ao construtor. Para acessar os serviços, use o objeto de contexto que é passado para os métodos. Um exemplo de código neste artigo e o [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) mostram como fazer isso.

### <a name="override-canreadtype-and-canwritetype"></a>Substituir canreadtype e canwritetype

Especifique o tipo para desserializar em ou serializar de substituindo `CanReadType` os `CanWriteType` métodos ou. Por exemplo, a criação de texto de vCard a partir de um `Contact` tipo e vice-versa.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>O método CanWriteResult

Em alguns cenários, `CanWriteResult` deve ser substituído em vez de `CanWriteType` . Use `CanWriteResult` se as condições a seguir forem verdadeiras:

* O método Action retorna uma classe de modelo.
* Há classes derivadas que podem ser retornadas em runtime.
* A classe derivada retornada pela ação deve ser conhecida em tempo de execução.

Por exemplo, suponha que o método de ação:

* A assinatura retorna um `Person` tipo.
* Pode retornar um `Student` `Instructor` tipo ou derivado de `Person` . 

Para que o formatador manipule somente `Student` objetos, verifique o tipo de <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> no objeto de contexto fornecido para o `CanWriteResult` método. Quando o método de ação retorna `IActionResult` :

* Não é necessário usar `CanWriteResult` .
* O `CanWriteType` método recebe o tipo de tempo de execução.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Substituir ReadRequestBodyAsync e WriteResponseBodyAsync

A desserialização ou a serialização é executada no `ReadRequestBodyAsync` ou no `WriteResponseBodyAsync` . O exemplo a seguir mostra como obter serviços do contêiner de injeção de dependência. Os serviços não podem ser obtidos dos parâmetros do construtor.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Como configurar o MVC para usar um formatador personalizado

Para usar um formatador personalizado, adicione uma instância da classe de formatador à coleção `InputFormatters` ou `OutputFormatters`.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Formatadores são avaliados na ordem em que você os insere. O primeiro deles tem precedência.

## <a name="the-complete-vcardinputformatter-class"></a>A `VcardInputFormatter` classe completa

O código a seguir mostra a `VcardInputFormatter` classe do [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>Testar o aplicativo

[Execute o aplicativo de exemplo deste artigo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), que implementa os formatadores de entrada e saída do vCard básico. O aplicativo lê e grava vCards semelhantes ao seguinte:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Para ver a saída do vCard, execute o aplicativo e envie uma solicitação get com o cabeçalho Accept `text/vcard` para `https://localhost:5001/api/contacts` .

Para adicionar um vCard à coleção de contatos na memória:

* Envie uma `Post` solicitação para `/api/contacts` com uma ferramenta como o postmaster.
* Defina o cabeçalho `Content-Type` como `text/vcard`.
* Defina o `vCard` texto no corpo, formatado como o exemplo anterior.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
