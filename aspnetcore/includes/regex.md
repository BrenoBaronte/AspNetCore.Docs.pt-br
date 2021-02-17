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
ms.openlocfilehash: 73395ab632f38fef1348b4657770eb52db5778d9
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552770"
---
> [!WARNING]
> Ao usar <xref:System.Text.RegularExpressions> o para processar a entrada não confiável, passe um tempo limite. Um usuário mal-intencionado pode fornecer entrada para `RegularExpressions` causar um [ataque de negação de serviço](https://www.us-cert.gov/ncas/tips/ST04-015). ASP.NET Core APIs do Framework que usam `RegularExpressions` passar um tempo limite.