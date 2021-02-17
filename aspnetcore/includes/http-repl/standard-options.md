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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551295"
---
* `-F|--no-formatting`

  <span data-ttu-id="a5052-101">Um sinalizador cuja presença suprime a formatação da resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a5052-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="a5052-102">Define um cabeçalho para a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a5052-102">Sets an HTTP request header.</span></span> <span data-ttu-id="a5052-103">Os dois formatos de valor a seguir são compatíveis:</span><span class="sxs-lookup"><span data-stu-id="a5052-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="a5052-104">Especifica um arquivo em que o corpo da resposta HTTP deve ser gravado.</span><span class="sxs-lookup"><span data-stu-id="a5052-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="a5052-105">Por exemplo, `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="a5052-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="a5052-106">Se ainda não existir, o arquivo será criado.</span><span class="sxs-lookup"><span data-stu-id="a5052-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="a5052-107">Especifica um arquivo em que os cabeçalhos da resposta HTTP devem ser gravados.</span><span class="sxs-lookup"><span data-stu-id="a5052-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="a5052-108">Por exemplo, `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="a5052-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="a5052-109">Se ainda não existir, o arquivo será criado.</span><span class="sxs-lookup"><span data-stu-id="a5052-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="a5052-110">Um sinalizador cuja presença habilita o streaming da resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a5052-110">A flag whose presence enables streaming of the HTTP response.</span></span>
