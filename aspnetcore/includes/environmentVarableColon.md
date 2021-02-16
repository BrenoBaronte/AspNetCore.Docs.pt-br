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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536266"
---
<span data-ttu-id="e2290-101">O `:` separador não funciona com chaves hierárquicas de variáveis de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="e2290-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="e2290-102">`__`, o sublinhado duplo é:</span><span class="sxs-lookup"><span data-stu-id="e2290-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="e2290-103">Compatível com todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="e2290-103">Supported by all platforms.</span></span> <span data-ttu-id="e2290-104">Por exemplo, o `:` separador não tem suporte do [bash](https://linuxhint.com/bash-environment-variables/), mas sim `__` .</span><span class="sxs-lookup"><span data-stu-id="e2290-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="e2290-105">Substituído automaticamente por um `:`</span><span class="sxs-lookup"><span data-stu-id="e2290-105">Automatically replaced by a `:`</span></span>