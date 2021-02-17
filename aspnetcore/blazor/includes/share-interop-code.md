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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551235"
---
## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="bea04-101">Compartilhar código de interoperabilidade em uma biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="bea04-101">Share interop code in a class library</span></span>

<span data-ttu-id="bea04-102">O código de interoperabilidade JS pode ser incluído em uma biblioteca de classes, que permite que você compartilhe o código em um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="bea04-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="bea04-103">A biblioteca de classes lida com a inserção de recursos JavaScript no assembly compilado.</span><span class="sxs-lookup"><span data-stu-id="bea04-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="bea04-104">Os arquivos JavaScript são colocados na `wwwroot` pasta.</span><span class="sxs-lookup"><span data-stu-id="bea04-104">The JavaScript files are placed in the `wwwroot` folder.</span></span> <span data-ttu-id="bea04-105">As ferramentas cuidam da inserção dos recursos quando a biblioteca é criada.</span><span class="sxs-lookup"><span data-stu-id="bea04-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="bea04-106">O pacote NuGet criado é referenciado no arquivo de projeto do aplicativo da mesma forma que qualquer pacote NuGet é referenciado.</span><span class="sxs-lookup"><span data-stu-id="bea04-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="bea04-107">Depois que o pacote é restaurado, o código do aplicativo pode chamar o JavaScript como se fosse o C#.</span><span class="sxs-lookup"><span data-stu-id="bea04-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="bea04-108">Para obter mais informações, consulte <xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="bea04-108">For more information, see <xref:blazor/components/class-libraries>.</span></span>
