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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552466"
---
> [!NOTE]
> <span data-ttu-id="7a6f2-101">Antes de ASP.NET Core os aplicativos Web 3,0 gravam um log do tipo `LogLevel.Warning` por solicitação se a cultura solicitada não for suportada.</span><span class="sxs-lookup"><span data-stu-id="7a6f2-101">Prior to ASP.NET Core 3.0 web apps write one log of type `LogLevel.Warning` per request if the requested culture is unsupported.</span></span> <span data-ttu-id="7a6f2-102">O registro `LogLevel.Warning` em log de um por solicitação é capaz de fazer grandes arquivos de log com informações redundantes.</span><span class="sxs-lookup"><span data-stu-id="7a6f2-102">Logging one `LogLevel.Warning` per request is can make large log files with redundant information.</span></span> <span data-ttu-id="7a6f2-103">Esse comportamento foi alterado no ASP.NET 3,0.</span><span class="sxs-lookup"><span data-stu-id="7a6f2-103">This behavior has been changed in ASP.NET 3.0.</span></span> <span data-ttu-id="7a6f2-104">O `RequestLocalizationMiddleware` grava um log do tipo `LogLevel.Debug` , o que reduz o tamanho dos logs de produção.</span><span class="sxs-lookup"><span data-stu-id="7a6f2-104">The `RequestLocalizationMiddleware` writes a log of type `LogLevel.Debug`, which reduces the size of production logs.</span></span>
