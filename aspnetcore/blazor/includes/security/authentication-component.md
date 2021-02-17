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
ms.openlocfilehash: 7f147e29040b16966af1de8130ac36ff83c2a796
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551419"
---
<span data-ttu-id="612d1-101">A página produzida pelo `Authentication` componente ( `Pages/Authentication.razor` ) define as rotas necessárias para lidar com estágios de autenticação diferentes.</span><span class="sxs-lookup"><span data-stu-id="612d1-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="612d1-102">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> componente:</span><span class="sxs-lookup"><span data-stu-id="612d1-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="612d1-103">É fornecido pelo [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote.</span><span class="sxs-lookup"><span data-stu-id="612d1-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="612d1-104">Gerencia a execução das ações apropriadas em cada estágio de autenticação.</span><span class="sxs-lookup"><span data-stu-id="612d1-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
