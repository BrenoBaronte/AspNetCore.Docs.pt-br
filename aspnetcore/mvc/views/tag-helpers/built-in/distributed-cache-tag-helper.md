---
title: Auxiliar de Marca de Cache Distribuído no ASP.NET Core
author: pkellner
description: Saiba como usar o Auxiliar de marca de cache distribuído.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 4b8e393542c56502a825000773bbf714d91e4128
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399225"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="df2a1-103">Auxiliar de Marca de Cache Distribuído no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df2a1-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="df2a1-104">Por [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="df2a1-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="df2a1-105">O Auxiliar de Marca de Cache Distribuído fornece a capacidade de melhorar consideravelmente o desempenho do aplicativo ASP.NET Core armazenando seu conteúdo em cache em uma fonte de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="df2a1-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="df2a1-106">Para obter uma visão geral de Auxiliares de marcação, consulte <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="df2a1-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="df2a1-107">O Auxiliar de Marca de Cache Distribuído herda da mesma classe base do Auxiliar de Marca de Cache.</span><span class="sxs-lookup"><span data-stu-id="df2a1-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="df2a1-108">Todos os atributos de [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) estão disponíveis ao Auxiliar de Marca Distribuído.</span><span class="sxs-lookup"><span data-stu-id="df2a1-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="df2a1-109">O Auxiliar de Marca de Cache distribuído usa [injeção de construtor](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="df2a1-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="df2a1-110">A interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é passada para o construtor do Auxiliar de Marca de Cache Distribuído.</span><span class="sxs-lookup"><span data-stu-id="df2a1-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="df2a1-111">Se nenhuma implementação concreta de `IDistributedCache` for criada em `Startup.ConfigureServices` (*Startup.cs*), o Auxiliar de Marca de Cache Distribuído usará o mesmo provedor na memória para armazenar dados em cache como o [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="df2a1-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="df2a1-112">Atributos do auxiliar de marca de cache distribuído</span><span class="sxs-lookup"><span data-stu-id="df2a1-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="df2a1-113">Atributos compartilhados com o Auxiliar de Marca de Cache</span><span class="sxs-lookup"><span data-stu-id="df2a1-113">Attributes shared with the Cache Tag Helper</span></span>

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

<span data-ttu-id="df2a1-114">O Auxiliar de Marca de Cache Distribuído herda da mesma classe que o Auxiliar de Marca de Cache.</span><span class="sxs-lookup"><span data-stu-id="df2a1-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="df2a1-115">Para obter descrições desses atributos, confira [Auxiliar de Marca de Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="df2a1-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="df2a1-116">name</span><span class="sxs-lookup"><span data-stu-id="df2a1-116">name</span></span>

| <span data-ttu-id="df2a1-117">Tipo de Atributo</span><span class="sxs-lookup"><span data-stu-id="df2a1-117">Attribute Type</span></span> | <span data-ttu-id="df2a1-118">Exemplo</span><span class="sxs-lookup"><span data-stu-id="df2a1-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="df2a1-119">Cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="df2a1-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="df2a1-120">`name` é obrigatório.</span><span class="sxs-lookup"><span data-stu-id="df2a1-120">`name` is required.</span></span> <span data-ttu-id="df2a1-121">O atributo `name` é usado como uma chave para cada instância de cache armazenada.</span><span class="sxs-lookup"><span data-stu-id="df2a1-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="df2a1-122">Ao contrário do auxiliar de marca de cache que atribui uma chave de cache para cada instância com base no Razor nome da página e no local na Razor página, o auxiliar da marca cache distribuído baseia-se apenas em sua chave no atributo `name` .</span><span class="sxs-lookup"><span data-stu-id="df2a1-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="df2a1-123">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="df2a1-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="df2a1-124">Implementações de IDistributedCache do Auxiliar de Marca de Cache Distribuído</span><span class="sxs-lookup"><span data-stu-id="df2a1-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="df2a1-125">Há duas implementações de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> internas do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df2a1-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="df2a1-126">Uma é baseada no SQL Server e a outra, no Redis.</span><span class="sxs-lookup"><span data-stu-id="df2a1-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="df2a1-127">Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span><span class="sxs-lookup"><span data-stu-id="df2a1-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="df2a1-128">Os detalhes dessas implementações podem ser encontrados em <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="df2a1-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="df2a1-129">Ambas as implementações envolvem configurar de uma instância do `IDistributedCache` em `Startup`.</span><span class="sxs-lookup"><span data-stu-id="df2a1-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="df2a1-130">Não há nenhum atributo de marca especificamente associado ao uso de uma implementação específica de `IDistributedCache`.</span><span class="sxs-lookup"><span data-stu-id="df2a1-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df2a1-131">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="df2a1-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
