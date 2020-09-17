---
title: Redis backplane para ASP.NET Core SignalR escalar horizontalmente
author: bradygaster
description: Saiba como configurar um backplane Redis para habilitar a expansão para um SignalR aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/redis-backplane
ms.openlocfilehash: a57176409c5f9bcc620db7e070f6616951eb9a54
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722768"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a><span data-ttu-id="f2ddd-103">Configurar um Redis backplane para ASP.NET Core SignalR escalar horizontalmente</span><span class="sxs-lookup"><span data-stu-id="f2ddd-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="f2ddd-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse), [Brady GASTER](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="f2ddd-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="f2ddd-105">Este artigo explica SignalR aspectos específicos da configuração de um servidor [Redis](https://redis.io/) a ser usado para escalar horizontalmente um SignalR aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="f2ddd-106">Configurar um backplane Redis</span><span class="sxs-lookup"><span data-stu-id="f2ddd-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="f2ddd-107">Implantar um servidor Redis.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="f2ddd-108">Para uso em produção, um backplane Redis é recomendado somente quando executado na mesma data center que o SignalR aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="f2ddd-109">Caso contrário, a latência de rede degrada o desempenho.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="f2ddd-110">Se seu SignalR aplicativo estiver em execução na nuvem do Azure, recomendamos o serviço do Azure SignalR em vez de um backplane Redis.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="f2ddd-111">Você pode usar o serviço de cache Redis do Azure para ambientes de desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="f2ddd-112">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="f2ddd-113">Documentação de Redis</span><span class="sxs-lookup"><span data-stu-id="f2ddd-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="f2ddd-114">Documentação do Cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="f2ddd-114">Azure Redis Cache documentation</span></span>](/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="f2ddd-115">No SignalR aplicativo, instale o `Microsoft.AspNetCore.SignalR.Redis` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="f2ddd-116">No `Startup.ConfigureServices` método, chame `AddRedis` após `AddSignalR` :</span><span class="sxs-lookup"><span data-stu-id="f2ddd-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="f2ddd-117">Configure as opções conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="f2ddd-118">A maioria das opções pode ser definida na cadeia de conexão ou no objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="f2ddd-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="f2ddd-119">Opções especificadas em `ConfigurationOptions` substituir aquelas definidas na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="f2ddd-120">O exemplo a seguir mostra como definir opções no `ConfigurationOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="f2ddd-121">Este exemplo adiciona um prefixo de canal para que vários aplicativos possam compartilhar a mesma instância de Redis, conforme explicado na etapa a seguir.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="f2ddd-122">No código anterior, `options.Configuration` é inicializado com tudo o que foi especificado na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="f2ddd-123">No SignalR aplicativo, instale um dos seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="f2ddd-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` -Depende de StackExchange. Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="f2ddd-125">Este é o pacote recomendado para o ASP.NET Core 2,2 e posterior.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="f2ddd-126">`Microsoft.AspNetCore.SignalR.Redis` -Depende de StackExchange. Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="f2ddd-127">Este pacote não está incluído no ASP.NET Core 3,0 e posterior.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="f2ddd-128">No `Startup.ConfigureServices` método, chame <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="f2ddd-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="f2ddd-129">Ao usar o `Microsoft.AspNetCore.SignalR.Redis` , chame <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="f2ddd-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="f2ddd-130">Configure as opções conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="f2ddd-131">A maioria das opções pode ser definida na cadeia de conexão ou no objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="f2ddd-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="f2ddd-132">Opções especificadas em `ConfigurationOptions` substituir aquelas definidas na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="f2ddd-133">O exemplo a seguir mostra como definir opções no `ConfigurationOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="f2ddd-134">Este exemplo adiciona um prefixo de canal para que vários aplicativos possam compartilhar a mesma instância de Redis, conforme explicado na etapa a seguir.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="f2ddd-135">Ao usar o `Microsoft.AspNetCore.SignalR.Redis` , chame <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="f2ddd-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="f2ddd-136">No código anterior, `options.Configuration` é inicializado com tudo o que foi especificado na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="f2ddd-137">Para obter informações sobre as opções de Redis, consulte a [documentação do StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="f2ddd-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="f2ddd-138">No SignalR aplicativo, instale o seguinte pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="f2ddd-139">No `Startup.ConfigureServices` método, chame <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="f2ddd-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="f2ddd-140">Configure as opções conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="f2ddd-141">A maioria das opções pode ser definida na cadeia de conexão ou no objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="f2ddd-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="f2ddd-142">Opções especificadas em `ConfigurationOptions` substituir aquelas definidas na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="f2ddd-143">O exemplo a seguir mostra como definir opções no `ConfigurationOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="f2ddd-144">Este exemplo adiciona um prefixo de canal para que vários aplicativos possam compartilhar a mesma instância de Redis, conforme explicado na etapa a seguir.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="f2ddd-145">No código anterior, `options.Configuration` é inicializado com tudo o que foi especificado na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="f2ddd-146">Para obter informações sobre as opções de Redis, consulte a [documentação do StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="f2ddd-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="f2ddd-147">Se você estiver usando um servidor Redis para vários SignalR aplicativos, use um prefixo de canal diferente para cada SignalR aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="f2ddd-148">Definir um prefixo de canal isola um SignalR aplicativo de outros que usam prefixos de canal diferentes.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="f2ddd-149">Se você não atribuir prefixos diferentes, uma mensagem enviada de um aplicativo para todos os seus próprios clientes acessará todos os clientes de todos os aplicativos que usam o servidor Redis como um backplane.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="f2ddd-150">Configure o software de balanceamento de carga do farm de servidores para sessões adesivas.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="f2ddd-151">Aqui estão alguns exemplos de documentação sobre como fazer isso:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="f2ddd-152">IIS</span><span class="sxs-lookup"><span data-stu-id="f2ddd-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="f2ddd-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="f2ddd-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="f2ddd-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="f2ddd-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="f2ddd-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="f2ddd-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="f2ddd-156">Erros do servidor Redis</span><span class="sxs-lookup"><span data-stu-id="f2ddd-156">Redis server errors</span></span>

<span data-ttu-id="f2ddd-157">Quando um servidor Redis fica inativo, o SignalR gera exceções que indicam que as mensagens não serão entregues.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="f2ddd-158">Algumas mensagens de exceção típicas:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-158">Some typical exception messages:</span></span>

* <span data-ttu-id="f2ddd-159">*Falha ao gravar mensagem*</span><span class="sxs-lookup"><span data-stu-id="f2ddd-159">*Failed writing message*</span></span>
* <span data-ttu-id="f2ddd-160">*Falha ao invocar o método de Hub ' MethodName '*</span><span class="sxs-lookup"><span data-stu-id="f2ddd-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="f2ddd-161">*Falha na conexão com o Redis*</span><span class="sxs-lookup"><span data-stu-id="f2ddd-161">*Connection to Redis failed*</span></span>

<span data-ttu-id="f2ddd-162">SignalR não armazena em buffer as mensagens para enviá-las quando o servidor volta a ficar em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-162">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="f2ddd-163">Todas as mensagens enviadas enquanto o servidor Redis está inoperante são perdidas.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-163">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="f2ddd-164">SignalR reconecta-se automaticamente quando o servidor Redis está disponível novamente.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-164">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="f2ddd-165">Comportamento personalizado para falhas de conexão</span><span class="sxs-lookup"><span data-stu-id="f2ddd-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="f2ddd-166">Aqui está um exemplo que mostra como lidar com eventos de falha de conexão do Redis.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="f2ddd-167">Clustering Redis</span><span class="sxs-lookup"><span data-stu-id="f2ddd-167">Redis Clustering</span></span>

<span data-ttu-id="f2ddd-168">O [clustering Redis](https://redis.io/topics/cluster-spec) é um método para obter alta disponibilidade usando vários servidores Redis.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="f2ddd-169">O clustering não tem suporte oficialmente, mas pode funcionar.</span><span class="sxs-lookup"><span data-stu-id="f2ddd-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f2ddd-170">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f2ddd-170">Next steps</span></span>

<span data-ttu-id="f2ddd-171">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="f2ddd-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="f2ddd-172">Documentação de Redis</span><span class="sxs-lookup"><span data-stu-id="f2ddd-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="f2ddd-173">Documentação do StackExchange Redis</span><span class="sxs-lookup"><span data-stu-id="f2ddd-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="f2ddd-174">Documentação do Cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="f2ddd-174">Azure Redis Cache documentation</span></span>](/azure/redis-cache/)