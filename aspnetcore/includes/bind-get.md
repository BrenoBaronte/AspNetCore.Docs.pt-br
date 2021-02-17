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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551765"
---
> [!WARNING]
> Por motivos de segurança, você deve aceitar associar os dados da solicitação `GET` às propriedades do modelo de página. Verifique a entrada do usuário antes de mapeá-la para as propriedades. Optar `GET` pela associação é útil ao abordar cenários que dependem de cadeias de caracteres de consulta ou valores de rota.
>
> Para associar uma propriedade em `GET` solicitações, defina a [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) Propriedade do atributo `SupportsGet` como `true` :
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Para obter mais informações, consulte [ASP.NET Core Community encontros: associar em obter discussão (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
