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
ms.openlocfilehash: 087c5a7dd20bc653a05c337dde905716032e00b3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552614"
---
## <a name="debug-diagnostics"></a><span data-ttu-id="e7f24-101">Diagnóstico de depuração</span><span class="sxs-lookup"><span data-stu-id="e7f24-101">Debug diagnostics</span></span>

<span data-ttu-id="e7f24-102">Para obter uma saída de diagnóstico de roteamento detalhada, defina `Logging:LogLevel:Microsoft` como `Debug` .</span><span class="sxs-lookup"><span data-stu-id="e7f24-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="e7f24-103">No ambiente de desenvolvimento, defina o nível de log em *appsettings.Development.jsem*:</span><span class="sxs-lookup"><span data-stu-id="e7f24-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
