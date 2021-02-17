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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552553"
---
<span data-ttu-id="e81ee-101">O middleware de cabeçalhos encaminhados deve ser executado antes de outro middleware.</span><span class="sxs-lookup"><span data-stu-id="e81ee-101">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="e81ee-102">Essa ordenação garantirá que o middleware conte com informações de cabeçalhos encaminhadas que podem consumir os valores de cabeçalho para processamento.</span><span class="sxs-lookup"><span data-stu-id="e81ee-102">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="e81ee-103">Para executar o middleware de cabeçalhos encaminhados após o diagnóstico e o middleware de tratamento de erros, consulte [ordem de middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#fhmo).</span><span class="sxs-lookup"><span data-stu-id="e81ee-103">To run Forwarded Headers Middleware after diagnostics and error handling middleware, see [Forwarded Headers Middleware order](xref:host-and-deploy/proxy-load-balancer#fhmo).</span></span>