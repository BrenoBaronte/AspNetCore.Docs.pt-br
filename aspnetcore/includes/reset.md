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
Redefinir permite que o servidor redefina uma solicitação HTTP/2 com um código de erro especificado. Uma solicitação de redefinição é considerada anulada.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` no exemplo de código anterior, especifica o `INTERNAL_ERROR` código de erro. Para obter mais informações sobre códigos de erro HTTP/2, visite a [seção código de erro de especificação http/2](https://tools.ietf.org/html/rfc7540#page-50).