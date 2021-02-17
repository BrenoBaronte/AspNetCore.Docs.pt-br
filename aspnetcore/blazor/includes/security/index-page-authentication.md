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
ms.openlocfilehash: b2519eaab7a83d66e60be05b1c2deeb8094b016f
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551762"
---
<span data-ttu-id="da388-101">A página de índice ( `wwwroot/index.html` ) inclui um script que define o `AuthenticationService` em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="da388-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="da388-102">`AuthenticationService` manipula os detalhes de baixo nível do protocolo OIDC.</span><span class="sxs-lookup"><span data-stu-id="da388-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="da388-103">O aplicativo chama internamente os métodos definidos no script para executar as operações de autenticação.</span><span class="sxs-lookup"><span data-stu-id="da388-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
