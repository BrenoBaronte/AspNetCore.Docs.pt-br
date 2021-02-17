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
<span data-ttu-id="186b2-101">Os trailers HTTP são semelhantes aos cabeçalhos HTTP, exceto que são enviados depois que o corpo da resposta é enviado.</span><span class="sxs-lookup"><span data-stu-id="186b2-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="186b2-102">Para o IIS e HTTP.sys, há suporte apenas para os trailers de resposta HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="186b2-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="186b2-103">No código de exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="186b2-103">In the preceding example code:</span></span>

* <span data-ttu-id="186b2-104">`SupportsTrailers` garante que os trailers tenham suporte para a resposta.</span><span class="sxs-lookup"><span data-stu-id="186b2-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="186b2-105">`DeclareTrailer` Adiciona o nome do trailer fornecido ao `Trailer` cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="186b2-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="186b2-106">Declarar os marcadores de resposta é opcional, mas recomendado.</span><span class="sxs-lookup"><span data-stu-id="186b2-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="186b2-107">Se `DeclareTrailer` for chamado, ele deve ser antes que os cabeçalhos de resposta sejam enviados.</span><span class="sxs-lookup"><span data-stu-id="186b2-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="186b2-108">`AppendTrailer` anexa o trailer.</span><span class="sxs-lookup"><span data-stu-id="186b2-108">`AppendTrailer` appends the trailer.</span></span>
