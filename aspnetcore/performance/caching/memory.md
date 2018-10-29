---
title: Memória de cache no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar dados em cache na memória no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2018
uid: performance/caching/memory
ms.openlocfilehash: 54b4029362c6b26254cb08397ef2e9131f6291d4
ms.sourcegitcommit: 375e9a67f5e1f7b0faaa056b4b46294cc70f55b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50207245"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="dd698-103">Memória de cache no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd698-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="dd698-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dd698-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dd698-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd698-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="dd698-106">Noções básicas de cache</span><span class="sxs-lookup"><span data-stu-id="dd698-106">Caching basics</span></span>

<span data-ttu-id="dd698-107">O cache pode melhorar significativamente o desempenho e a escalabilidade de um aplicativo, reduzindo o trabalho necessário para gerar o conteúdo.</span><span class="sxs-lookup"><span data-stu-id="dd698-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="dd698-108">O armazenamento em cache funciona melhor com dados que raramente são alterados.</span><span class="sxs-lookup"><span data-stu-id="dd698-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="dd698-109">O cache faz uma cópia dos dados que pode ser retornada muito mais rapidamente do que a partir da origem.</span><span class="sxs-lookup"><span data-stu-id="dd698-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="dd698-110">Você deve escrever e testar seu aplicativo para que ele nunca dependa de dados armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-110">You should write and test your app to never depend on cached data.</span></span>

<span data-ttu-id="dd698-111">O ASP.NET Core dá suporte a vários caches diferentes.</span><span class="sxs-lookup"><span data-stu-id="dd698-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="dd698-112">O cache mais simples se baseia o [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), que representa um cache armazenado na memória do servidor web.</span><span class="sxs-lookup"><span data-stu-id="dd698-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="dd698-113">Aplicativos que são executados em um farm de servidores devem garantir que as sessões sejam aderentes ao usar o cache na memória.</span><span class="sxs-lookup"><span data-stu-id="dd698-113">Apps which run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="dd698-114">Sessões aderentes garantem que todas as solicitações subsequentes de um cliente vão para o mesmo servidor.</span><span class="sxs-lookup"><span data-stu-id="dd698-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="dd698-115">Por exemplo, uso de aplicativos da Web do Azure [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) para rotear todas as solicitações subsequentes para o mesmo servidor.</span><span class="sxs-lookup"><span data-stu-id="dd698-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="dd698-116">Não adesivo sessões em um farm da web exigem uma [cache distribuído](distributed.md) para evitar problemas de consistência de cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="dd698-117">Para alguns aplicativos, um cache distribuído pode dar suporte a mais alta escalabilidade horizontal que um cache na memória.</span><span class="sxs-lookup"><span data-stu-id="dd698-117">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="dd698-118">Usando um cache distribuído libera a memória de cache para um processo externo.</span><span class="sxs-lookup"><span data-stu-id="dd698-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="dd698-119">O cache `IMemoryCache` removerá entradas de cache sob pressão de memória, a menos que a [prioridade de cache](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) seja definida como `CacheItemPriority.NeverRemove`.</span><span class="sxs-lookup"><span data-stu-id="dd698-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="dd698-120">Você pode definir o `CacheItemPriority` para ajustar a prioridade com que o cache remove itens sob pressão de memória.</span><span class="sxs-lookup"><span data-stu-id="dd698-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

::: moniker-end

<span data-ttu-id="dd698-121">O cache de memória pode armazenar qualquer objeto, enquanto a interface de cache distribuída é limitada a`byte[]`.</span><span class="sxs-lookup"><span data-stu-id="dd698-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="dd698-122">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="dd698-122">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="dd698-123"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Pacote do NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) pode ser usado com:</span><span class="sxs-lookup"><span data-stu-id="dd698-123"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="dd698-124">.NET standard 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dd698-124">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="dd698-125">Qualquer [implementação do .NET](/dotnet/standard/net-standard#net-implementation-support) que tem como alvo o .NET Standard 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dd698-125">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="dd698-126">Por exemplo, ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dd698-126">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="dd698-127">.NET framework 4.5 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dd698-127">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="dd698-128">[Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (descrita neste tópico) é mais recomendada `System.Runtime.Caching` / `MemoryCache` porque ele é melhor integrado ao ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd698-128">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this topic) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="dd698-129">Por exemplo, `IMemoryCache` funciona nativamente com o ASP.NET Core [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dd698-129">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="dd698-130">Use `System.Runtime.Caching` / `MemoryCache` como uma ponte de compatibilidade ao portar código do ASP.NET 4.x ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd698-130">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="dd698-131">Diretrizes de cache</span><span class="sxs-lookup"><span data-stu-id="dd698-131">Cache guidelines</span></span>

* <span data-ttu-id="dd698-132">Código deveria ter sempre uma opção de fallback para buscar dados e **não** dependem de um valor em cache que está sendo disponível.</span><span class="sxs-lookup"><span data-stu-id="dd698-132">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="dd698-133">O cache usa um recurso escasso, de memória.</span><span class="sxs-lookup"><span data-stu-id="dd698-133">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="dd698-134">Limitar o crescimento de cache:</span><span class="sxs-lookup"><span data-stu-id="dd698-134">Limit cache growth:</span></span>
  * <span data-ttu-id="dd698-135">Fazer **não** usar entrada externo como chaves de cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-135">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="dd698-136">Use as expirações para limitar o crescimento de cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-136">Use expirations to limit cache growth.</span></span>
  * [<span data-ttu-id="dd698-137">Usar SetSize, tamanho e SizeLimit para limitar o tamanho do cache</span><span class="sxs-lookup"><span data-stu-id="dd698-137">Use SetSize, Size, and SizeLimit to limit cache size</span></span>](#use-setsize-size-and-sizelimit-to-limit-cache-size)

## <a name="using-imemorycache"></a><span data-ttu-id="dd698-138">Usando IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="dd698-138">Using IMemoryCache</span></span>

<span data-ttu-id="dd698-139">O cache na memória é um *service* que é referenciado em seu aplicativo usando [injeção de dependência](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="dd698-139">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="dd698-140">Chame `AddMemoryCache` em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="dd698-140">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="dd698-141">Solicitar o `IMemoryCache` instância no construtor:</span><span class="sxs-lookup"><span data-stu-id="dd698-141">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="dd698-142">`IMemoryCache` requer o pacote NuGet [Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span><span class="sxs-lookup"><span data-stu-id="dd698-142">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="dd698-143">`IMemoryCache` requer o pacote NuGet [Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), que está disponível na [Microsoft.AspNetCore.All metapacote](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="dd698-143">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="dd698-144">`IMemoryCache` requer o pacote NuGet [Extensions](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), que está disponível na [metapacote Microsoft](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="dd698-144">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="dd698-145">O seguinte código usa [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) para verificar se um horário está no cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-145">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="dd698-146">Se não estiver armazenado em cache um tempo, uma nova entrada é criada e adicionada ao cache com [definir](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span><span class="sxs-lookup"><span data-stu-id="dd698-146">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="dd698-147">A hora atual e o tempo em cache são exibidos:</span><span class="sxs-lookup"><span data-stu-id="dd698-147">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="dd698-148">O valor `DateTime` em cache permanecerá no cache enquanto houver solicitações dentro do tempo limite (e nenhuma remoção devido à pressão de memória).</span><span class="sxs-lookup"><span data-stu-id="dd698-148">The cached `DateTime` value remains in the cache while there are requests within the timeout period (and no eviction due to memory pressure).</span></span> <span data-ttu-id="dd698-149">A imagem abaixo mostra a hora atual e uma hora mais antiga recuperada do cache:</span><span class="sxs-lookup"><span data-stu-id="dd698-149">The following image shows the current time and an older time retrieved from the cache:</span></span>

![Exibição de índice com dois horários diferentes exibidos](memory/_static/time.png)

<span data-ttu-id="dd698-151">O código a seguir usa [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) e [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) para fazer o cache dos dados. </span><span class="sxs-lookup"><span data-stu-id="dd698-151">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span> 

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="dd698-152">O código a seguir chama o método [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) para buscar a hora em cache:</span><span class="sxs-lookup"><span data-stu-id="dd698-152">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="dd698-153">Ver [métodos IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) e [CacheExtensions métodos](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) para obter uma descrição dos métodos de cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-153">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of the cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="dd698-154">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="dd698-154">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="dd698-155">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="dd698-155">The following sample:</span></span>

- <span data-ttu-id="dd698-156">Define o tempo de expiração absoluta.</span><span class="sxs-lookup"><span data-stu-id="dd698-156">Sets the absolute expiration time.</span></span> <span data-ttu-id="dd698-157">Isso é o tempo máximo que a entrada pode ser armazenado em cache e impede que o item se torne muito desatualizado quando a expiração deslizante continuamente é renovada.</span><span class="sxs-lookup"><span data-stu-id="dd698-157">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
- <span data-ttu-id="dd698-158">Define um tempo de expiração deslizante.</span><span class="sxs-lookup"><span data-stu-id="dd698-158">Sets a sliding expiration time.</span></span> <span data-ttu-id="dd698-159">Solicitações que acessam esse item em cache redefinirá o relógio de expiração deslizante.</span><span class="sxs-lookup"><span data-stu-id="dd698-159">Requests that access this cached item will reset the sliding expiration clock.</span></span>
- <span data-ttu-id="dd698-160">Define a prioridade de cache para `CacheItemPriority.NeverRemove`.</span><span class="sxs-lookup"><span data-stu-id="dd698-160">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
- <span data-ttu-id="dd698-161">Define uma [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) que será chamado depois que a entrada é removida do cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-161">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="dd698-162">O retorno de chamada é executado em um thread diferente do código que remove o item do cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-162">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="dd698-163">Usar SetSize, tamanho e SizeLimit para limitar o tamanho do cache</span><span class="sxs-lookup"><span data-stu-id="dd698-163">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="dd698-164">Um `MemoryCache` instância pode, opcionalmente, especificar e impor um limite de tamanho.</span><span class="sxs-lookup"><span data-stu-id="dd698-164">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="dd698-165">O limite de tamanho de memória não tem uma unidade de medida de definido porque o cache não tem nenhum mecanismo para medir o tamanho de entradas.</span><span class="sxs-lookup"><span data-stu-id="dd698-165">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="dd698-166">Se o limite de tamanho de memória de cache for definido, todas as entradas devem especificar o tamanho.</span><span class="sxs-lookup"><span data-stu-id="dd698-166">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="dd698-167">O tamanho especificado está em unidades que o desenvolvedor optar por.</span><span class="sxs-lookup"><span data-stu-id="dd698-167">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="dd698-168">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="dd698-168">For example:</span></span>

* <span data-ttu-id="dd698-169">Se o aplicativo web foi principalmente cache de cadeias de caracteres, cada tamanho de entrada de cache pode ser o comprimento da cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="dd698-169">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="dd698-170">O aplicativo pode especificar o tamanho de todas as entradas como 1 e o limite de tamanho é a contagem de entradas.</span><span class="sxs-lookup"><span data-stu-id="dd698-170">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="dd698-171">O código a seguir cria um sem unidade tamanho fixo [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) acessível pelo [injeção de dependência](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="dd698-171">The following code creates a unitless fixed size [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="dd698-172">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) não tem unidades.</span><span class="sxs-lookup"><span data-stu-id="dd698-172">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) does not have units.</span></span> <span data-ttu-id="dd698-173">As entradas em cache devem especificar o tamanho em qualquer unidade que consideram mais apropriados se o tamanho da memória de cache tiver sido definido.</span><span class="sxs-lookup"><span data-stu-id="dd698-173">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="dd698-174">Todos os usuários de uma instância de cache devem usar a mesma unidade de sistema.</span><span class="sxs-lookup"><span data-stu-id="dd698-174">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="dd698-175">Uma entrada não será armazenada, se a soma dos tamanhos da entrada armazenada em cache excede o valor especificado pelo `SizeLimit`.</span><span class="sxs-lookup"><span data-stu-id="dd698-175">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="dd698-176">Se nenhum limite de tamanho do cache for definido, o tamanho do cache definida na entrada será ignorado.</span><span class="sxs-lookup"><span data-stu-id="dd698-176">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="dd698-177">O código a seguir registra `MyMemoryCache` com o [injeção de dependência](xref:fundamentals/dependency-injection) contêiner.</span><span class="sxs-lookup"><span data-stu-id="dd698-177">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="dd698-178">`MyMemoryCache` é criado como um cache de memória independentes para os componentes que reconhecem esse cache de tamanho limitado e saber como definir o tamanho do cache de entrada adequadamente.</span><span class="sxs-lookup"><span data-stu-id="dd698-178">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="dd698-179">O seguinte código usa `MyMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="dd698-179">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="dd698-180">O tamanho da entrada de cache pode ser definido [tamanho](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) ou o [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) método de extensão:</span><span class="sxs-lookup"><span data-stu-id="dd698-180">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a><span data-ttu-id="dd698-181">Dependências de cache</span><span class="sxs-lookup"><span data-stu-id="dd698-181">Cache dependencies</span></span>

<span data-ttu-id="dd698-182">O exemplo a seguir mostra como expirar uma entrada de cache, se uma entrada dependente expira.</span><span class="sxs-lookup"><span data-stu-id="dd698-182">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="dd698-183">Um `CancellationChangeToken` é adicionado ao item em cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-183">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="dd698-184">Quando `Cancel` é chamado de `CancellationTokenSource`, ambas as entradas de cache são removidas.</span><span class="sxs-lookup"><span data-stu-id="dd698-184">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="dd698-185">Usando um `CancellationTokenSource` permite que várias entradas de cache a ser removido como um grupo.</span><span class="sxs-lookup"><span data-stu-id="dd698-185">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="dd698-186">Com o `using` padrão no código acima, as entradas de cache criado dentro de `using` bloco herdará as configurações de expiração e gatilhos.</span><span class="sxs-lookup"><span data-stu-id="dd698-186">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="dd698-187">Observações adicionais</span><span class="sxs-lookup"><span data-stu-id="dd698-187">Additional notes</span></span>

- <span data-ttu-id="dd698-188">Ao usar um retorno de chamada para preencher novamente um item de cache:</span><span class="sxs-lookup"><span data-stu-id="dd698-188">When using a callback to repopulate a cache item:</span></span>

  - <span data-ttu-id="dd698-189">Várias solicitações podem localizar o valor da chave em cache vazio porque o retorno de chamada não foi concluído.</span><span class="sxs-lookup"><span data-stu-id="dd698-189">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  - <span data-ttu-id="dd698-190">Isso pode resultar em vários threads repopulação o item em cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-190">This can result in several threads repopulating the cached item.</span></span>

- <span data-ttu-id="dd698-191">Quando uma entrada de cache é usada para criar outro, o filho copia a entrada de pai tokens de expiração e as configurações de expiração com base no tempo.</span><span class="sxs-lookup"><span data-stu-id="dd698-191">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="dd698-192">O filho não está expirada pela remoção manual ou a atualização da entrada pai.</span><span class="sxs-lookup"><span data-stu-id="dd698-192">The child isn't expired by manual removal or updating of the parent entry.</span></span>

- <span data-ttu-id="dd698-193">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) para definir os retornos de chamada que serão acionados depois que a entrada de cache é removida do cache.</span><span class="sxs-lookup"><span data-stu-id="dd698-193">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd698-194">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dd698-194">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
