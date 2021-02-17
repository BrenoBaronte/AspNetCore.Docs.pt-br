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
> <span data-ttu-id="3c00a-101">Por motivos de segurança, você deve aceitar associar os dados da solicitação `GET` às propriedades do modelo de página.</span><span class="sxs-lookup"><span data-stu-id="3c00a-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="3c00a-102">Verifique a entrada do usuário antes de mapeá-la para as propriedades.</span><span class="sxs-lookup"><span data-stu-id="3c00a-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="3c00a-103">Optar `GET` pela associação é útil ao abordar cenários que dependem de cadeias de caracteres de consulta ou valores de rota.</span><span class="sxs-lookup"><span data-stu-id="3c00a-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="3c00a-104">Para associar uma propriedade em `GET` solicitações, defina a [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) Propriedade do atributo `SupportsGet` como `true` :</span><span class="sxs-lookup"><span data-stu-id="3c00a-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="3c00a-105">Para obter mais informações, consulte [ASP.NET Core Community encontros: associar em obter discussão (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="3c00a-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
