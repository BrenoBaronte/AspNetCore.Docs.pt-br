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
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552611"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> <span data-ttu-id="96854-101">Um parâmetro **catch-all** pode corresponder a rotas incorretamente devido a um [bug](https://github.com/dotnet/aspnetcore/issues/18677) no roteamento.</span><span class="sxs-lookup"><span data-stu-id="96854-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="96854-102">Os aplicativos afetados por esse bug têm as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="96854-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="96854-103">Uma rota catch-all, por exemplo, `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="96854-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="96854-104">A rota capturar tudo falha ao corresponder às solicitações que devem corresponder.</span><span class="sxs-lookup"><span data-stu-id="96854-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="96854-105">A remoção de outras rotas faz com que a rota capturar tudo comece a funcionar.</span><span class="sxs-lookup"><span data-stu-id="96854-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="96854-106">Veja bugs do GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) e [16579](https://github.com/dotnet/aspnetcore/issues/16579) , por exemplo, casos que atingiram esse bug.</span><span class="sxs-lookup"><span data-stu-id="96854-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="96854-107">Uma correção de aceitação para esse bug está contida no [SDK do .NET Core 3.1.301 e posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="96854-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="96854-108">O código a seguir define uma opção interna que corrige esse bug:</span><span class="sxs-lookup"><span data-stu-id="96854-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end