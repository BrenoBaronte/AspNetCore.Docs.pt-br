---
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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552371"
---
Os trailers HTTP são semelhantes aos cabeçalhos HTTP, exceto que são enviados depois que o corpo da resposta é enviado. Para o IIS e HTTP.sys, há suporte apenas para os trailers de resposta HTTP/2.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

No código de exemplo anterior:

* `SupportsTrailers` garante que os trailers tenham suporte para a resposta.
* `DeclareTrailer` Adiciona o nome do trailer fornecido ao `Trailer` cabeçalho de resposta. Declarar os marcadores de resposta é opcional, mas recomendado. Se `DeclareTrailer` for chamado, ele deve ser antes que os cabeçalhos de resposta sejam enviados.
* `AppendTrailer` anexa o trailer.
