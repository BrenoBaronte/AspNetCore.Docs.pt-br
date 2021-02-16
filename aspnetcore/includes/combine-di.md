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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536267"
---
<a name="csc"></a>

<span data-ttu-id="a725f-101">Considere o seguinte `ConfigureServices` método, que registra os serviços e configura as opções:</span><span class="sxs-lookup"><span data-stu-id="a725f-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="a725f-102">Grupos relacionados de registros podem ser movidos para um método de extensão para registrar serviços.</span><span class="sxs-lookup"><span data-stu-id="a725f-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="a725f-103">Por exemplo, os serviços de configuração são adicionados à seguinte classe:</span><span class="sxs-lookup"><span data-stu-id="a725f-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="a725f-104">Os serviços restantes são registrados em uma classe semelhante.</span><span class="sxs-lookup"><span data-stu-id="a725f-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="a725f-105">O método a seguir `ConfigureServices` usa os novos métodos de extensão para registrar os serviços:</span><span class="sxs-lookup"><span data-stu-id="a725f-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="a725f-106">**_Observação:_** Cada `services.Add{GROUP_NAME}` método de extensão adiciona e potencialmente configura os serviços do.</span><span class="sxs-lookup"><span data-stu-id="a725f-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="a725f-107">Por exemplo, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adiciona os serviços que os controladores MVC com exibições exigem e <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adiciona os serviços que as Razor páginas exigem.</span><span class="sxs-lookup"><span data-stu-id="a725f-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="a725f-108">Recomendamos que os aplicativos sigam essa Convenção de nomenclatura.</span><span class="sxs-lookup"><span data-stu-id="a725f-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="a725f-109">Coloque os métodos de extensão no namespace <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="a725f-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
