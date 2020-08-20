---
title: Métodos de filtro para Razor páginas no ASP.NET Core
author: Rick-Anderson
description: Saiba como criar métodos de filtro para Razor páginas no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
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
uid: razor-pages/filter
ms.openlocfilehash: b6d6585c0cbd52715c4192d4ab3bee756dbb41b3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635041"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="e950e-103">Métodos de filtro para Razor páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e950e-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e950e-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e950e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e950e-105">Razor Os filtros de página [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Razor páginas executem o código antes e depois que um Razor manipulador de página é executado.</span><span class="sxs-lookup"><span data-stu-id="e950e-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="e950e-106">Razor Os filtros de página são semelhantes aos [filtros de ação ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), exceto que não podem ser aplicados a métodos de manipuladores de páginas individuais.</span><span class="sxs-lookup"><span data-stu-id="e950e-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="e950e-107">Razor Filtros de página:</span><span class="sxs-lookup"><span data-stu-id="e950e-107">Razor Page filters:</span></span>

* <span data-ttu-id="e950e-108">Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="e950e-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="e950e-109">Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="e950e-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="e950e-110">Executam o código após a execução do método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="e950e-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="e950e-111">Podem ser implementados em uma única página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="e950e-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="e950e-112">Não podem ser aplicados a métodos do manipulador de uma página específica.</span><span class="sxs-lookup"><span data-stu-id="e950e-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="e950e-113">Pode haver dependências de Construtor preenchidas pela [injeção de dependência](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="e950e-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="e950e-114">Para obter mais informações, [consulte](/aspnet/core/mvc/controllers/filters#servicefilterattribute) [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute)e.</span><span class="sxs-lookup"><span data-stu-id="e950e-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="e950e-115">Embora os construtores de página e o middleware habilitem a execução do código personalizado antes que um método de manipulador seja executado, somente Razor os filtros de página habilitam o acesso à <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> página.</span><span class="sxs-lookup"><span data-stu-id="e950e-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="e950e-116">O middleware tem acesso ao `HttpContext` , mas não ao "contexto da página".</span><span class="sxs-lookup"><span data-stu-id="e950e-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="e950e-117">Os filtros têm um <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parâmetro derivado, que fornece acesso ao `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="e950e-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="e950e-118">Aqui está um exemplo para um filtro de página: [implemente um atributo Filter](#ifa) que adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores ou middleware.</span><span class="sxs-lookup"><span data-stu-id="e950e-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="e950e-119">O acesso ao contexto da página, que inclui acesso às instâncias da página e do modelo, só está disponível durante a execução de filtros, manipuladores ou o corpo de uma Razor página.</span><span class="sxs-lookup"><span data-stu-id="e950e-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="e950e-120">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e950e-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e950e-121">Razor Os filtros de página fornecem os seguintes métodos, que podem ser aplicados globalmente ou no nível de página:</span><span class="sxs-lookup"><span data-stu-id="e950e-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="e950e-122">Métodos síncronos:</span><span class="sxs-lookup"><span data-stu-id="e950e-122">Synchronous methods:</span></span>

  * <span data-ttu-id="e950e-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="e950e-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="e950e-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="e950e-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="e950e-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="e950e-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="e950e-126">Métodos assíncronos:</span><span class="sxs-lookup"><span data-stu-id="e950e-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="e950e-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="e950e-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="e950e-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="e950e-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="e950e-129">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="e950e-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="e950e-130">Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="e950e-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="e950e-131">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="e950e-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="e950e-132">Se ambas as interfaces forem implementadas, somente os métodos assíncronos serão chamados.</span><span class="sxs-lookup"><span data-stu-id="e950e-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="e950e-133">A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.</span><span class="sxs-lookup"><span data-stu-id="e950e-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="e950e-134">Implementar Razor filtros de página globalmente</span><span class="sxs-lookup"><span data-stu-id="e950e-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="e950e-135">O código a seguir implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="e950e-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="e950e-136">No código anterior, `ProcessUserAgent.Write` é um código fornecido pelo usuário que funciona com a cadeia de caracteres do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="e950e-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="e950e-137">O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="e950e-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="e950e-138">O código a seguir chama <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para aplicar as `SampleAsyncPageFilter` somente páginas em */Movies*:</span><span class="sxs-lookup"><span data-stu-id="e950e-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="e950e-139">O código a seguir implementa o `IPageFilter` síncrono:</span><span class="sxs-lookup"><span data-stu-id="e950e-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="e950e-140">O código a seguir habilita o `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="e950e-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="e950e-141">Implementar Razor filtros de página substituindo métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="e950e-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="e950e-142">O código a seguir substitui os Razor filtros de página assíncrona:</span><span class="sxs-lookup"><span data-stu-id="e950e-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="e950e-143">Implementar um atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="e950e-143">Implement a filter attribute</span></span>

<span data-ttu-id="e950e-144">O filtro de filtro baseado em atributo interno <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> pode ser subclasse.</span><span class="sxs-lookup"><span data-stu-id="e950e-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="e950e-145">O filtro a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="e950e-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="e950e-146">O código a seguir se aplica ao atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="e950e-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="e950e-147">Use uma ferramenta como as ferramentas de desenvolvedor do navegador para examinar os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="e950e-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="e950e-148">Em **cabeçalhos de resposta**, `author: Rick` é exibido.</span><span class="sxs-lookup"><span data-stu-id="e950e-148">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="e950e-149">Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.</span><span class="sxs-lookup"><span data-stu-id="e950e-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="e950e-150">Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.</span><span class="sxs-lookup"><span data-stu-id="e950e-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="e950e-151">Autorizar o atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="e950e-151">Authorize filter attribute</span></span>

<span data-ttu-id="e950e-152">O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="e950e-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e950e-153">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e950e-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e950e-154">Razor Os filtros de página [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Razor páginas executem o código antes e depois que um Razor manipulador de página é executado.</span><span class="sxs-lookup"><span data-stu-id="e950e-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="e950e-155">Razor Os filtros de página são semelhantes aos [filtros de ação ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), exceto que não podem ser aplicados a métodos de manipuladores de páginas individuais.</span><span class="sxs-lookup"><span data-stu-id="e950e-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="e950e-156">Razor Filtros de página:</span><span class="sxs-lookup"><span data-stu-id="e950e-156">Razor Page filters:</span></span>

* <span data-ttu-id="e950e-157">Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="e950e-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="e950e-158">Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="e950e-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="e950e-159">Executam o código após a execução do método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="e950e-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="e950e-160">Podem ser implementados em uma única página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="e950e-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="e950e-161">Não podem ser aplicados a métodos do manipulador de uma página específica.</span><span class="sxs-lookup"><span data-stu-id="e950e-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="e950e-162">O código pode ser executado antes que um método de manipulador seja executado usando o construtor de página ou middleware, mas somente Razor filtros de página têm acesso ao [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="e950e-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="e950e-163">Os filtros têm um parâmetro derivado [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0), que fornece acesso a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="e950e-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="e950e-164">Por exemplo, a amostra [Implementar um atributo de filtro](#ifa) adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores nem middlewares.</span><span class="sxs-lookup"><span data-stu-id="e950e-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="e950e-165">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e950e-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e950e-166">Razor Os filtros de página fornecem os seguintes métodos, que podem ser aplicados globalmente ou no nível de página:</span><span class="sxs-lookup"><span data-stu-id="e950e-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="e950e-167">Métodos síncronos:</span><span class="sxs-lookup"><span data-stu-id="e950e-167">Synchronous methods:</span></span>

  * <span data-ttu-id="e950e-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="e950e-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="e950e-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="e950e-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="e950e-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="e950e-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="e950e-171">Métodos assíncronos:</span><span class="sxs-lookup"><span data-stu-id="e950e-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="e950e-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="e950e-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="e950e-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="e950e-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="e950e-174">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, não ambas.</span><span class="sxs-lookup"><span data-stu-id="e950e-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="e950e-175">Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="e950e-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="e950e-176">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="e950e-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="e950e-177">Se ambas as interfaces forem implementadas, somente os métodos assíncronos serão chamados.</span><span class="sxs-lookup"><span data-stu-id="e950e-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="e950e-178">A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.</span><span class="sxs-lookup"><span data-stu-id="e950e-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="e950e-179">Implementar Razor filtros de página globalmente</span><span class="sxs-lookup"><span data-stu-id="e950e-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="e950e-180">O código a seguir implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="e950e-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="e950e-181">No código anterior, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) não é necessário.</span><span class="sxs-lookup"><span data-stu-id="e950e-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="e950e-182">Ele é usado na amostra para fornecer informações de rastreamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e950e-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="e950e-183">O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="e950e-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="e950e-184">O código a seguir mostra a classe `Startup` completa:</span><span class="sxs-lookup"><span data-stu-id="e950e-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="e950e-185">O código a seguir chama `AddFolderApplicationModelConvention` para aplicar o `SampleAsyncPageFilter` somente às páginas em */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="e950e-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="e950e-186">O código a seguir implementa o `IPageFilter` síncrono:</span><span class="sxs-lookup"><span data-stu-id="e950e-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="e950e-187">O código a seguir habilita o `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="e950e-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="e950e-188">Implementar Razor filtros de página substituindo métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="e950e-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="e950e-189">O código a seguir substitui os Razor filtros de página síncrona:</span><span class="sxs-lookup"><span data-stu-id="e950e-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="e950e-190">Implementar um atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="e950e-190">Implement a filter attribute</span></span>

<span data-ttu-id="e950e-191">O filtro baseado em atributo interno [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) pode tornar-se uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="e950e-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="e950e-192">O filtro a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="e950e-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="e950e-193">O código a seguir se aplica ao atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="e950e-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="e950e-194">Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.</span><span class="sxs-lookup"><span data-stu-id="e950e-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="e950e-195">Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.</span><span class="sxs-lookup"><span data-stu-id="e950e-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="e950e-196">Autorizar o atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="e950e-196">Authorize filter attribute</span></span>

<span data-ttu-id="e950e-197">O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="e950e-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
