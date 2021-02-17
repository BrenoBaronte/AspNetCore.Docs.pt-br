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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552927"
---
<span data-ttu-id="d0a39-101">Redefinir permite que o servidor redefina uma solicitação HTTP/2 com um código de erro especificado.</span><span class="sxs-lookup"><span data-stu-id="d0a39-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="d0a39-102">Uma solicitação de redefinição é considerada anulada.</span><span class="sxs-lookup"><span data-stu-id="d0a39-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="d0a39-103">`Reset` no exemplo de código anterior, especifica o `INTERNAL_ERROR` código de erro.</span><span class="sxs-lookup"><span data-stu-id="d0a39-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="d0a39-104">Para obter mais informações sobre códigos de erro HTTP/2, visite a [seção código de erro de especificação http/2](https://tools.ietf.org/html/rfc7540#page-50).</span><span class="sxs-lookup"><span data-stu-id="d0a39-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>