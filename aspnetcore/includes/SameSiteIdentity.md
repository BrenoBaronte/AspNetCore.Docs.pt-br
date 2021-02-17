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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551705"
---
<span data-ttu-id="1b38b-101">O ASP.NET Core, [Identity](xref:security/authentication/identity) em grande parte, não é afetado pelo [SameSite cookie s](xref:security/samesite) , exceto por cenários avançados como o `IFrames` ou a `OpenIdConnect` integração.</span><span class="sxs-lookup"><span data-stu-id="1b38b-101">ASP.NET Core [Identity](xref:security/authentication/identity) is largely unaffected by [SameSite cookies](xref:security/samesite) except for advanced scenarios like `IFrames` or `OpenIdConnect` integration.</span></span>

<span data-ttu-id="1b38b-102">Ao usar o `Identity` , ***não*** adicione nenhum cookie provedor ou chamada ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` , cuida disso `Identity` .</span><span class="sxs-lookup"><span data-stu-id="1b38b-102">When using `Identity`, do ***not*** add any cookie providers or call ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)`, `Identity` takes care of that.</span></span>