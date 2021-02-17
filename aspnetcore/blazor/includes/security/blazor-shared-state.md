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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552797"
---
<span data-ttu-id="c7de7-101">Blazor aplicativos de servidor Live na memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="c7de7-101">Blazor server apps live in server memory.</span></span> <span data-ttu-id="c7de7-102">Isso significa que há vários aplicativos hospedados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="c7de7-102">That means that there are multiple apps hosted within the same process.</span></span> <span data-ttu-id="c7de7-103">Para cada sessão de aplicativo, Blazor inicia um circuito com seu próprio escopo de contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="c7de7-103">For each app session, Blazor starts a circuit with its own DI container scope.</span></span> <span data-ttu-id="c7de7-104">Isso significa que os serviços com escopo são exclusivos por Blazor sessão.</span><span class="sxs-lookup"><span data-stu-id="c7de7-104">That means that scoped services are unique per Blazor session.</span></span>

> [!WARNING]
> <span data-ttu-id="c7de7-105">Não recomendamos aplicativos no mesmo estado de compartilhamento do servidor usando serviços singleton, a menos que seja feito um tratamento extremo, pois isso pode introduzir vulnerabilidades de segurança, como o vazamento do estado do usuário entre circuitos.</span><span class="sxs-lookup"><span data-stu-id="c7de7-105">We don't recommend apps on the same server share state using singleton services unless extreme care is taken, as this can introduce security vulnerabilities, such as leaking user state across circuits.</span></span>

<span data-ttu-id="c7de7-106">Você pode usar serviços singleton com estado em Blazor aplicativos se eles forem especificamente projetados para ele.</span><span class="sxs-lookup"><span data-stu-id="c7de7-106">You can use stateful singleton services in Blazor apps if they are specifically designed for it.</span></span> <span data-ttu-id="c7de7-107">Por exemplo, é possível usar um cache de memória como um singleton porque ele requer uma chave para acessar uma determinada entrada, supondo que os usuários não tenham controle de quais chaves de cache são usadas.</span><span class="sxs-lookup"><span data-stu-id="c7de7-107">For example, it's ok to use a memory cache as a singleton because it requires a key to access a given entry, assuming users don't have control of what cache keys are used.</span></span>

<span data-ttu-id="c7de7-108">**Além disso, por motivos de segurança, você não deve usar <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> em Blazor aplicativos.**</span><span class="sxs-lookup"><span data-stu-id="c7de7-108">**Additionally, again for security reasons, you must not use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> within Blazor apps.**</span></span> <span data-ttu-id="c7de7-109">Blazor os aplicativos são executados fora do contexto do pipeline de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7de7-109">Blazor apps run outside of the context of the ASP.NET Core pipeline.</span></span> <span data-ttu-id="c7de7-110">Não há garantia de que <xref:Microsoft.AspNetCore.Http.HttpContext> esteja disponível dentro do <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , nem é garantido que esteja segurando o contexto que iniciou o Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c7de7-110">The <xref:Microsoft.AspNetCore.Http.HttpContext> isn't guaranteed to be available within the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, nor is it guaranteed to be holding the context that started the Blazor app.</span></span>

<span data-ttu-id="c7de7-111">A maneira recomendada de passar o estado de solicitação para o Blazor aplicativo é por meio de parâmetros para o componente raiz na renderização inicial do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c7de7-111">The recommended way to pass request state to the Blazor app is through parameters to the root component in the initial rendering of the app:</span></span>

* <span data-ttu-id="c7de7-112">Defina uma classe com todos os dados que você deseja passar para o Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c7de7-112">Define a class with all the data you want to pass to the Blazor app.</span></span>
* <span data-ttu-id="c7de7-113">Preencha esses dados da Razor página usando o <xref:Microsoft.AspNetCore.Http.HttpContext> disponível naquele momento.</span><span class="sxs-lookup"><span data-stu-id="c7de7-113">Populate that data from the Razor page using the <xref:Microsoft.AspNetCore.Http.HttpContext> available at that time.</span></span>
* <span data-ttu-id="c7de7-114">Passe os dados para o Blazor aplicativo como um parâmetro para o componente raiz (aplicativo).</span><span class="sxs-lookup"><span data-stu-id="c7de7-114">Pass the data to the Blazor app as a parameter to the root component (App).</span></span>
* <span data-ttu-id="c7de7-115">Defina um parâmetro no componente raiz para manter os dados que estão sendo passados para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c7de7-115">Define a parameter in the root component to hold the data being passed to the app.</span></span>
* <span data-ttu-id="c7de7-116">Usar os dados específicos do usuário dentro do aplicativo; ou, como alternativa, copie esses dados em um serviço com escopo dentro <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> para que ele possa ser usado em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c7de7-116">Use the user-specific data within the app; or alternatively, copy that data into a scoped service within <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> so that it can be used across the app.</span></span>

<span data-ttu-id="c7de7-117">Para obter mais informações e um código de exemplo, consulte <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="c7de7-117">For more information and example code, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>
