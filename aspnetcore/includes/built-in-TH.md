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
ms.openlocfilehash: 1cc2a01725a2af8f3dbfe1b23ea81e99bab9ef8e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552767"
---
## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="8479a-101">Auxiliares de marcação internos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8479a-101">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="8479a-102">**[Auxiliar de marcação de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-102">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span></span>

<span data-ttu-id="8479a-103">**[Auxiliar de marca de cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-103">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span></span>

<span data-ttu-id="8479a-104">**[Auxiliar de marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-104">**[Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)**</span></span>

<span data-ttu-id="8479a-105">**[Auxiliar de marca de cache distribuído](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-105">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span></span>

<span data-ttu-id="8479a-106">**[Auxiliar de marcação de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-106">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span></span>

<span data-ttu-id="8479a-107">**[Auxiliar de marca de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-107">**[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span></span>

<span data-ttu-id="8479a-108">**[Auxiliar de marcação de ação de formulário](xref:mvc/views/working-with-forms#the-form-action-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-108">**[Form Action Tag Helper](xref:mvc/views/working-with-forms#the-form-action-tag-helper)**</span></span>

<span data-ttu-id="8479a-109">**[Auxiliar de marcação de imagem](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-109">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span></span>

<span data-ttu-id="8479a-110">**[Auxiliar de marcação de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-110">**[Input Tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span></span>

<span data-ttu-id="8479a-111">**[Auxiliar de marcação de rótulo](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-111">**[Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span></span>

<span data-ttu-id="8479a-112">**[Auxiliar de marca de link](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-112">**[Link Tag Helper](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**</span></span>

<span data-ttu-id="8479a-113">**[Auxiliar de marca parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-113">**[Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span></span>

<span data-ttu-id="8479a-114">**[Auxiliar de marca de script](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-114">**[Script Tag Helper](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**</span></span>

<span data-ttu-id="8479a-115">**[Selecionar auxiliar de marca](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-115">**[Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span></span>

<span data-ttu-id="8479a-116">**[Auxiliar de marca de TextArea](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-116">**[Textarea Tag Helper](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span></span>

<span data-ttu-id="8479a-117">**[Auxiliar de marcação de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-117">**[Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span></span>

<span data-ttu-id="8479a-118">**[Auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="8479a-118">**[Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span></span>
