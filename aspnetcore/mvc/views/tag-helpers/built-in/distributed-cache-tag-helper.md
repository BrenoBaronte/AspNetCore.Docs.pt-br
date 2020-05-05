---
title: Auxiliar de Marca de Cache Distribuído no ASP.NET Core
author: pkellner
description: Saiba como usar o Auxiliar de marca de cache distribuído.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: df1daa68a3e18f7aad4507ce9526d76ff6a2114d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773910"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="eaa69-103">Auxiliar de Marca de Cache Distribuído no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaa69-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="eaa69-104">Por [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="eaa69-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="eaa69-105">O Auxiliar de Marca de Cache Distribuído fornece a capacidade de melhorar consideravelmente o desempenho do aplicativo ASP.NET Core armazenando seu conteúdo em cache em uma fonte de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="eaa69-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="eaa69-106">Para obter uma visão geral de Auxiliares de marcação, consulte <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="eaa69-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="eaa69-107">O Auxiliar de Marca de Cache Distribuído herda da mesma classe base do Auxiliar de Marca de Cache.</span><span class="sxs-lookup"><span data-stu-id="eaa69-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="eaa69-108">Todos os atributos de [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) estão disponíveis ao Auxiliar de Marca Distribuído.</span><span class="sxs-lookup"><span data-stu-id="eaa69-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="eaa69-109">O Auxiliar de Marca de Cache distribuído usa [injeção de construtor](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="eaa69-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="eaa69-110">A interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é passada para o construtor do Auxiliar de Marca de Cache Distribuído.</span><span class="sxs-lookup"><span data-stu-id="eaa69-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="eaa69-111">Se nenhuma implementação concreta de `IDistributedCache` for criada em `Startup.ConfigureServices` (*Startup.cs*), o Auxiliar de Marca de Cache Distribuído usará o mesmo provedor na memória para armazenar dados em cache como o [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="eaa69-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="eaa69-112">Atributos do auxiliar de marca de cache distribuído</span><span class="sxs-lookup"><span data-stu-id="eaa69-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="eaa69-113">Atributos compartilhados com o Auxiliar de Marca de Cache</span><span class="sxs-lookup"><span data-stu-id="eaa69-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="eaa69-114">O Auxiliar de Marca de Cache Distribuído herda da mesma classe que o Auxiliar de Marca de Cache.</span><span class="sxs-lookup"><span data-stu-id="eaa69-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="eaa69-115">Para obter descrições desses atributos, confira [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="eaa69-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="eaa69-116">name</span><span class="sxs-lookup"><span data-stu-id="eaa69-116">name</span></span>

| <span data-ttu-id="eaa69-117">Tipo de Atributo</span><span class="sxs-lookup"><span data-stu-id="eaa69-117">Attribute Type</span></span> | <span data-ttu-id="eaa69-118">Exemplo</span><span class="sxs-lookup"><span data-stu-id="eaa69-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="eaa69-119">String</span><span class="sxs-lookup"><span data-stu-id="eaa69-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="eaa69-120">`name` é obrigatório.</span><span class="sxs-lookup"><span data-stu-id="eaa69-120">`name` is required.</span></span> <span data-ttu-id="eaa69-121">O atributo `name` é usado como uma chave para cada instância de cache armazenada.</span><span class="sxs-lookup"><span data-stu-id="eaa69-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="eaa69-122">Ao contrário do auxiliar de marca de cache que atribui uma chave de cache para cada instância com Razor base no nome da página e Razor no local na página, o auxiliar da marca cache distribuído baseia-se `name`apenas em sua chave no atributo.</span><span class="sxs-lookup"><span data-stu-id="eaa69-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="eaa69-123">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="eaa69-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="eaa69-124">Implementações de IDistributedCache do Auxiliar de Marca de Cache Distribuído</span><span class="sxs-lookup"><span data-stu-id="eaa69-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="eaa69-125">Há duas implementações de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> internas do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa69-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="eaa69-126">Uma é baseada no SQL Server e a outra, no Redis.</span><span class="sxs-lookup"><span data-stu-id="eaa69-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="eaa69-127">Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span><span class="sxs-lookup"><span data-stu-id="eaa69-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="eaa69-128">Os detalhes dessas implementações podem ser encontrados em <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="eaa69-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="eaa69-129">Ambas as implementações envolvem configurar de uma instância do `IDistributedCache` em `Startup`.</span><span class="sxs-lookup"><span data-stu-id="eaa69-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="eaa69-130">Não há nenhum atributo de marca especificamente associado ao uso de uma implementação específica de `IDistributedCache`.</span><span class="sxs-lookup"><span data-stu-id="eaa69-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eaa69-131">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="eaa69-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
