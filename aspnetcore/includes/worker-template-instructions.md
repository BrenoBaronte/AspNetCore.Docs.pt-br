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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552464"
---
# <a name="visual-studio"></a>[<span data-ttu-id="89a64-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89a64-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="89a64-102">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="89a64-102">Create a new project.</span></span>
1. <span data-ttu-id="89a64-103">Selecione **serviço de trabalho**.</span><span class="sxs-lookup"><span data-stu-id="89a64-103">Select **Worker Service**.</span></span> <span data-ttu-id="89a64-104">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="89a64-104">Select **Next**.</span></span>
1. <span data-ttu-id="89a64-105">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="89a64-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="89a64-106">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="89a64-106">Select **Create**.</span></span>
1. <span data-ttu-id="89a64-107">Na caixa de diálogo **criar um novo serviço de trabalho** , selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="89a64-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89a64-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="89a64-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="89a64-109">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="89a64-109">Create a new project.</span></span>
1. <span data-ttu-id="89a64-110">Selecione **aplicativo** em **.NET Core** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="89a64-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="89a64-111">Selecione **trabalhador** em **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="89a64-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="89a64-112">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="89a64-112">Select **Next**.</span></span>
1. <span data-ttu-id="89a64-113">Selecione **.NET Core 3,0** ou posterior para a **estrutura de destino**.</span><span class="sxs-lookup"><span data-stu-id="89a64-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="89a64-114">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="89a64-114">Select **Next**.</span></span>
1. <span data-ttu-id="89a64-115">Forneça um nome no campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="89a64-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="89a64-116">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="89a64-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="89a64-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="89a64-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="89a64-118">Use o modelo de Serviço de Trabalho (`worker`) com o comando [dotnet novo](/dotnet/core/tools/dotnet-new) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="89a64-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="89a64-119">No exemplo a seguir, um aplicativo de Serviço de Trabalho é criado com o nome `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="89a64-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="89a64-120">Uma pasta para o aplicativo `ContosoWorker` é criada automaticamente quando o comando é executado.</span><span class="sxs-lookup"><span data-stu-id="89a64-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
