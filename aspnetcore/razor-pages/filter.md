---
title: Métodos de filtro para Razor páginas no ASP.NET Core
author: Rick-Anderson
description: Saiba como criar métodos de filtro para Razor páginas no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: a6d25c1b88e09560c1aad9aefd9148f7fe293909
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056823"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a>Métodos de filtro para Razor páginas no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Os filtros de página [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Razor páginas executem o código antes e depois que um Razor manipulador de página é executado. Razor Os filtros de página são semelhantes aos [filtros de ação ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), exceto que não podem ser aplicados a métodos de manipuladores de páginas individuais.

Razor Filtros de página:

* Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.
* Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.
* Executam o código após a execução do método do manipulador.
* Podem ser implementados em uma única página ou globalmente.
* Não podem ser aplicados a métodos do manipulador de uma página específica.
* Pode haver dependências de Construtor preenchidas pela [injeção de dependência](xref:fundamentals/dependency-injection) (di). Para obter mais informações, [consulte](../mvc/controllers/filters.md#servicefilterattribute) [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute)e.

Embora os construtores de página e o middleware habilitem a execução do código personalizado antes que um método de manipulador seja executado, somente Razor os filtros de página habilitam o acesso à <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> página. O middleware tem acesso ao `HttpContext` , mas não ao "contexto da página". Os filtros têm um <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parâmetro derivado, que fornece acesso ao `HttpContext` . Aqui está um exemplo para um filtro de página: [implemente um atributo Filter](#ifa) que adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores ou middleware. O acesso ao contexto da página, que inclui acesso às instâncias da página e do modelo, só está disponível durante a execução de filtros, manipuladores ou o corpo de uma Razor página.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([como baixar](xref:index#how-to-download-a-sample))

Razor Os filtros de página fornecem os seguintes métodos, que podem ser aplicados globalmente ou no nível de página:

* Métodos síncronos:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.

* Métodos assíncronos:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.

Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas. Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama. Caso contrário, ela chama os métodos da interface síncrona. Se ambas as interfaces forem implementadas, somente os métodos assíncronos serão chamados. A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.

## <a name="implement-no-locrazor-page-filters-globally"></a>Implementar Razor filtros de página globalmente

O código a seguir implementa `IAsyncPageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

No código anterior, `ProcessUserAgent.Write` é um código fornecido pelo usuário que funciona com a cadeia de caracteres do agente do usuário.

O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

O código a seguir chama <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para aplicar as `SampleAsyncPageFilter` somente páginas em */Movies*:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

O código a seguir implementa o `IPageFilter` síncrono:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

O código a seguir habilita o `SamplePageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a>Implementar Razor filtros de página substituindo métodos de filtro

O código a seguir substitui os Razor filtros de página assíncrona:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementar um atributo de filtro

O filtro de filtro baseado em atributo interno <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> pode ser subclasse. O filtro a seguir adiciona um cabeçalho à resposta:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

O código a seguir se aplica ao atributo `AddHeader`:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

Use uma ferramenta como as ferramentas de desenvolvedor do navegador para examinar os cabeçalhos. Em **cabeçalhos de resposta**, `author: Rick` é exibido.

Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.

Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorizar o atributo de filtro

O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor Os filtros de página [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Razor páginas executem o código antes e depois que um Razor manipulador de página é executado. Razor Os filtros de página são semelhantes aos [filtros de ação ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), exceto que não podem ser aplicados a métodos de manipuladores de páginas individuais.

Razor Filtros de página:

* Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.
* Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.
* Executam o código após a execução do método do manipulador.
* Podem ser implementados em uma única página ou globalmente.
* Não podem ser aplicados a métodos do manipulador de uma página específica.

O código pode ser executado antes que um método de manipulador seja executado usando o construtor de página ou middleware, mas somente Razor filtros de página têm acesso ao [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Os filtros têm um parâmetro derivado [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0), que fornece acesso a `HttpContext`. Por exemplo, a amostra [Implementar um atributo de filtro](#ifa) adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores nem middlewares.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([como baixar](xref:index#how-to-download-a-sample))

Razor Os filtros de página fornecem os seguintes métodos, que podem ser aplicados globalmente ou no nível de página:

* Métodos síncronos:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.

* Métodos assíncronos:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.

> [!NOTE]
> Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, não ambas. Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama. Caso contrário, ela chama os métodos da interface síncrona. Se ambas as interfaces forem implementadas, somente os métodos assíncronos serão chamados. A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.

## <a name="implement-no-locrazor-page-filters-globally"></a>Implementar Razor filtros de página globalmente

O código a seguir implementa `IAsyncPageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

No código anterior, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) não é necessário. Ele é usado na amostra para fornecer informações de rastreamento do aplicativo.

O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

O código a seguir mostra a classe `Startup` completa:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

O código a seguir chama `AddFolderApplicationModelConvention` para aplicar o `SampleAsyncPageFilter` somente às páginas em */subFolder*:

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

O código a seguir implementa o `IPageFilter` síncrono:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

O código a seguir habilita o `SamplePageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a>Implementar Razor filtros de página substituindo métodos de filtro

O código a seguir substitui os Razor filtros de página síncrona:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementar um atributo de filtro

O filtro baseado em atributo interno [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) pode tornar-se uma subclasse. O filtro a seguir adiciona um cabeçalho à resposta:

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

O código a seguir se aplica ao atributo `AddHeader`:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.

Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro. 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorizar o atributo de filtro

O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end