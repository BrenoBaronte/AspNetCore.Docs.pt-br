---
title: Auxiliar de marca de script no ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra os atributos auxiliares de marca de script ASP.NET Core e a função que cada atributo desempenha ao estender o comportamento da marca de script HTML.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053495"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="b682c-103">Auxiliar de marca de script no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b682c-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="b682c-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b682c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b682c-105">O [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) gera um link para um arquivo de script primário ou de retorno.</span><span class="sxs-lookup"><span data-stu-id="b682c-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="b682c-106">Normalmente, o arquivo de script primário está em uma CDN ( [rede de distribuição de conteúdo](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) ).</span><span class="sxs-lookup"><span data-stu-id="b682c-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="b682c-107">O auxiliar de marca de script permite que você especifique uma CDN para o arquivo de script e um fallback quando a CDN não estiver disponível.</span><span class="sxs-lookup"><span data-stu-id="b682c-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="b682c-108">O auxiliar de marca de script fornece a vantagem de desempenho de uma CDN com a robustez da hospedagem local.</span><span class="sxs-lookup"><span data-stu-id="b682c-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="b682c-109">A marcação a seguir :::no-loc(Razor)::: mostra um `script` elemento com um fallback:</span><span class="sxs-lookup"><span data-stu-id="b682c-109">The following :::no-loc(Razor)::: markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="b682c-110">Não use o `<script>` atributo [Defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) do elemento para adiar o carregamento do script CDN.</span><span class="sxs-lookup"><span data-stu-id="b682c-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="b682c-111">O auxiliar de marca de script processa o JavaScript que executa imediatamente a expressão [ASP-fallback-Test](#asp-fallback-test) .</span><span class="sxs-lookup"><span data-stu-id="b682c-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="b682c-112">A expressão falhará se o carregamento do script CDN for adiado.</span><span class="sxs-lookup"><span data-stu-id="b682c-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="b682c-113">Atributos auxiliares de marca de script comumente usados</span><span class="sxs-lookup"><span data-stu-id="b682c-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="b682c-114">Consulte [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) para todos os atributos, propriedades e métodos auxiliares de marca de script.</span><span class="sxs-lookup"><span data-stu-id="b682c-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="b682c-115">ASP – teste de fallback</span><span class="sxs-lookup"><span data-stu-id="b682c-115">asp-fallback-test</span></span>

<span data-ttu-id="b682c-116">O método de script definido no script primário a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="b682c-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="b682c-117">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="b682c-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="b682c-118">ASP-fallback-src</span><span class="sxs-lookup"><span data-stu-id="b682c-118">asp-fallback-src</span></span>

<span data-ttu-id="b682c-119">A URL de uma marca de script para fazer fallback no caso do primário falhar.</span><span class="sxs-lookup"><span data-stu-id="b682c-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="b682c-120">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="b682c-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b682c-121">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b682c-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
