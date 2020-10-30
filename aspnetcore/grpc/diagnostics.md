---
title: Registro em log e diagnóstico no gRPC no .NET
author: jamesnk
description: Saiba como coletar diagnósticos do seu aplicativo gRPC no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/diagnostics
ms.openlocfilehash: 1f25ae76e5a480e5e6f247e4ac78d06dd4e778e9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060437"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="b4406-103">Registro em log e diagnóstico no gRPC no .NET</span><span class="sxs-lookup"><span data-stu-id="b4406-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="b4406-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b4406-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b4406-105">Este artigo fornece diretrizes para coletar diagnósticos de um aplicativo gRPC para ajudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="b4406-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="b4406-106">Os tópicos abordados incluem:</span><span class="sxs-lookup"><span data-stu-id="b4406-106">Topics covered include:</span></span>

* <span data-ttu-id="b4406-107">Logs estruturados em **log** gravados no [log do .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b4406-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b4406-108"><xref:Microsoft.Extensions.Logging.ILogger> é usado pelas estruturas de aplicativo para gravar logs e por usuários para seus próprios registros em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="b4406-109">**Rastreamento** -eventos relacionados a uma operação escrita usando `DiaganosticSource` e `Activity` .</span><span class="sxs-lookup"><span data-stu-id="b4406-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="b4406-110">Os rastreamentos da fonte de diagnóstico são comumente usados para coletar telemetria de aplicativo por bibliotecas como [Application insights](/azure/azure-monitor/app/asp-net-core) e [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="b4406-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="b4406-111">**Métricas** – representação de medidas de dados em intervalos de tempo, por exemplo, solicitações por segundo.</span><span class="sxs-lookup"><span data-stu-id="b4406-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="b4406-112">As métricas são emitidas usando `EventCounter` e podem ser observadas usando [a ferramenta de linha de comando dotnet-Counters](/dotnet/core/diagnostics/dotnet-counters) ou com [Application insights](/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="b4406-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="b4406-113">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="b4406-113">Logging</span></span>

<span data-ttu-id="b4406-114">os serviços gRPC e o cliente gRPC gravam logs usando o [log do .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b4406-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b4406-115">Os logs são um bom lugar para começar quando você precisa depurar um comportamento inesperado em seus aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b4406-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="b4406-116">log de serviços gRPCs</span><span class="sxs-lookup"><span data-stu-id="b4406-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="b4406-117">Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b4406-118">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="b4406-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b4406-119">Como os serviços gRPCs são hospedados em ASP.NET Core, ele usa o sistema de registro em log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4406-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="b4406-120">Na configuração padrão, o gRPC registra muito pouca informação, mas isso pode ser configurado.</span><span class="sxs-lookup"><span data-stu-id="b4406-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="b4406-121">Consulte a documentação em [log de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4406-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="b4406-122">gRPC adiciona logs na `Grpc` categoria.</span><span class="sxs-lookup"><span data-stu-id="b4406-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="b4406-123">Para habilitar logs detalhados do gRPC, configure os `Grpc` prefixos para o `Debug` nível em seu *:::no-loc(appsettings.json):::* arquivo adicionando os seguintes itens à `LogLevel` subseção em `Logging` :</span><span class="sxs-lookup"><span data-stu-id="b4406-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *:::no-loc(appsettings.json):::* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="b4406-124">Você também pode configurar isso em *Startup.cs* com `ConfigureLogging` :</span><span class="sxs-lookup"><span data-stu-id="b4406-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="b4406-125">Se você não estiver usando a configuração baseada em JSON, defina o seguinte valor de configuração em seu sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="b4406-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="b4406-126">Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados.</span><span class="sxs-lookup"><span data-stu-id="b4406-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="b4406-127">Por exemplo, ao usar variáveis de ambiente, dois `_` caracteres são usados em vez do `:` (por exemplo, `Logging__LogLevel__Grpc` ).</span><span class="sxs-lookup"><span data-stu-id="b4406-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="b4406-128">É recomendável usar o `Debug` nível ao coletar diagnósticos mais detalhados para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="b4406-129">O `Trace` nível produz diagnósticos de nível muito baixo e raramente é necessário para diagnosticar problemas em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="b4406-130">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="b4406-130">Sample logging output</span></span>

<span data-ttu-id="b4406-131">Aqui está um exemplo de saída do console no `Debug` nível de um serviço gRPC:</span><span class="sxs-lookup"><span data-stu-id="b4406-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="b4406-132">Acessar logs do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b4406-132">Access server-side logs</span></span>

<span data-ttu-id="b4406-133">A maneira como você acessa os logs do lado do servidor depende do ambiente no qual você está executando o.</span><span class="sxs-lookup"><span data-stu-id="b4406-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="b4406-134">Como um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="b4406-134">As a console app</span></span>

<span data-ttu-id="b4406-135">Se você estiver executando o em um aplicativo de console, o [agente de log do console](xref:fundamentals/logging/index#console) deverá ser habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="b4406-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="b4406-136">os logs do gRPC serão exibidos no console do.</span><span class="sxs-lookup"><span data-stu-id="b4406-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="b4406-137">Outros ambientes</span><span class="sxs-lookup"><span data-stu-id="b4406-137">Other environments</span></span>

<span data-ttu-id="b4406-138">Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, kubernetes ou serviço do Windows), consulte <xref:fundamentals/logging/index> para obter mais informações sobre como configurar provedores de log adequados para o ambiente.</span><span class="sxs-lookup"><span data-stu-id="b4406-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="b4406-139">log de cliente do gRPC</span><span class="sxs-lookup"><span data-stu-id="b4406-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b4406-140">Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b4406-141">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="b4406-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b4406-142">Para obter logs do cliente .NET, você pode definir a `GrpcChannelOptions.LoggerFactory` propriedade quando o canal do cliente é criado.</span><span class="sxs-lookup"><span data-stu-id="b4406-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="b4406-143">Se você estiver chamando um serviço gRPC de um aplicativo ASP.NET Core, a fábrica do agente poderá ser resolvida da injeção de dependência (DI):</span><span class="sxs-lookup"><span data-stu-id="b4406-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="b4406-144">Uma maneira alternativa de habilitar o log do cliente é usar a [fábrica do cliente do gRPC](xref:grpc/clientfactory) para criar o cliente.</span><span class="sxs-lookup"><span data-stu-id="b4406-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="b4406-145">Um cliente gRPC registrado com a fábrica do cliente e resolvido a partir de DI usará automaticamente o log configurado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="b4406-146">Se seu aplicativo não estiver usando DI, você poderá criar uma nova `ILoggerFactory` instância com [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="b4406-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="b4406-147">Para acessar esse método, adicione o pacote [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) ao seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="b4406-148">escopos de log do cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="b4406-148">gRPC client log scopes</span></span>

<span data-ttu-id="b4406-149">O cliente gRPC adiciona um [escopo de log](../fundamentals/logging/index.md#log-scopes) aos logs feitos durante uma chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4406-149">The gRPC client adds a [logging scope](../fundamentals/logging/index.md#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="b4406-150">O escopo tem metadados relacionados à chamada gRPC:</span><span class="sxs-lookup"><span data-stu-id="b4406-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="b4406-151">**GrpcMethodType** -o tipo de método gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4406-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="b4406-152">Os valores possíveis são nomes de `Grpc.Core.MethodType` enum, por exemplo, unário</span><span class="sxs-lookup"><span data-stu-id="b4406-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="b4406-153">**GrpcUri** -o URI relativo do método gRPC, por exemplo,/Greet. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="b4406-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="b4406-154">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="b4406-154">Sample logging output</span></span>

<span data-ttu-id="b4406-155">Aqui está um exemplo de saída do console no `Debug` nível de um cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="b4406-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="b4406-156">Rastreamento</span><span class="sxs-lookup"><span data-stu-id="b4406-156">Tracing</span></span>

<span data-ttu-id="b4406-157">os serviços gRPC e o cliente gRPC fornecem informações sobre chamadas gRPC usando a [diagnóstico](/dotnet/api/system.diagnostics.diagnosticsource) e a [atividade](/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="b4406-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="b4406-158">O .NET gRPC usa uma atividade para representar uma chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4406-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="b4406-159">Os eventos de rastreamento são gravados na fonte de diagnóstico no início e na parada da atividade de chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4406-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="b4406-160">O rastreamento não captura informações sobre quando as mensagens são enviadas durante o tempo de vida das chamadas de streaming gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4406-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="b4406-161">rastreamento de serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="b4406-161">gRPC service tracing</span></span>

<span data-ttu-id="b4406-162">os serviços gRPCs são hospedados em ASP.NET Core que relata eventos sobre solicitações HTTP de entrada.</span><span class="sxs-lookup"><span data-stu-id="b4406-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="b4406-163">os metadados específicos do gRPC são adicionados ao diagnóstico de solicitação HTTP existente que o ASP.NET Core fornece.</span><span class="sxs-lookup"><span data-stu-id="b4406-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="b4406-164">O nome da origem do diagnóstico é `Microsoft.AspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="b4406-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="b4406-165">O nome da atividade é `Microsoft.AspNetCore.Hosting.HttpRequestIn` .</span><span class="sxs-lookup"><span data-stu-id="b4406-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="b4406-166">O nome do método gRPC invocado pela chamada gRPC é adicionado como uma marca com o nome `grpc.method` .</span><span class="sxs-lookup"><span data-stu-id="b4406-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="b4406-167">O código de status da chamada gRPC quando ela é concluída é adicionado como uma marca com o nome `grpc.status_code` .</span><span class="sxs-lookup"><span data-stu-id="b4406-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="b4406-168">rastreamento de cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="b4406-168">gRPC client tracing</span></span>

<span data-ttu-id="b4406-169">O cliente .NET gRPC usa `HttpClient` para fazer chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="b4406-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="b4406-170">Embora `HttpClient` o grave eventos de diagnóstico, o cliente .net gRPC fornece uma fonte de diagnóstico, atividade e eventos personalizados para que as informações completas sobre uma chamada gRPC possam ser coletadas.</span><span class="sxs-lookup"><span data-stu-id="b4406-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="b4406-171">O nome da origem do diagnóstico é `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="b4406-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="b4406-172">O nome da atividade é `Grpc.Net.Client.GrpcOut` .</span><span class="sxs-lookup"><span data-stu-id="b4406-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="b4406-173">O nome do método gRPC invocado pela chamada gRPC é adicionado como uma marca com o nome `grpc.method` .</span><span class="sxs-lookup"><span data-stu-id="b4406-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="b4406-174">O código de status da chamada gRPC quando ela é concluída é adicionado como uma marca com o nome `grpc.status_code` .</span><span class="sxs-lookup"><span data-stu-id="b4406-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="b4406-175">Coletando rastreamento</span><span class="sxs-lookup"><span data-stu-id="b4406-175">Collecting tracing</span></span>

<span data-ttu-id="b4406-176">A maneira mais fácil de usar `DiagnosticSource` é configurar uma biblioteca de telemetria, como [Application insights](/azure/azure-monitor/app/asp-net-core) ou [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="b4406-177">A biblioteca processará informações sobre chamadas gRPC em outras telemetrias do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4406-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="b4406-178">O rastreamento pode ser exibido em um serviço gerenciado como Application Insights ou você pode optar por executar seu próprio sistema de rastreamento distribuído.</span><span class="sxs-lookup"><span data-stu-id="b4406-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="b4406-179">O OpenTelemetry dá suporte à exportação de dados de rastreamento para [Jaeger](https://www.jaegertracing.io/) e [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="b4406-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="b4406-180">`DiagnosticSource` pode consumir eventos de rastreamento no código usando `DiagnosticListener` .</span><span class="sxs-lookup"><span data-stu-id="b4406-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="b4406-181">Para obter informações sobre como escutar uma fonte de diagnóstico com código, consulte o [Guia do usuário do diagnosticm](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="b4406-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="b4406-182">As bibliotecas de telemetria não capturam telemetria específica do gRPC `Grpc.Net.Client.GrpcOut` no momento.</span><span class="sxs-lookup"><span data-stu-id="b4406-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="b4406-183">Trabalho para melhorar as bibliotecas de telemetria capturando esse rastreamento está em andamento.</span><span class="sxs-lookup"><span data-stu-id="b4406-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="b4406-184">Métricas</span><span class="sxs-lookup"><span data-stu-id="b4406-184">Metrics</span></span>

<span data-ttu-id="b4406-185">Métricas é uma representação de medidas de dados em intervalos de tempo, por exemplo, solicitações por segundo.</span><span class="sxs-lookup"><span data-stu-id="b4406-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="b4406-186">Os dados de métricas permitem a observação do estado de um aplicativo em um alto nível.</span><span class="sxs-lookup"><span data-stu-id="b4406-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="b4406-187">As métricas do .NET gRPC são emitidas usando `EventCounter` .</span><span class="sxs-lookup"><span data-stu-id="b4406-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="b4406-188">métricas do serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="b4406-188">gRPC service metrics</span></span>

<span data-ttu-id="b4406-189">as métricas do servidor gRPC são relatadas na `Grpc.AspNetCore.Server` origem do evento.</span><span class="sxs-lookup"><span data-stu-id="b4406-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="b4406-190">Nome</span><span class="sxs-lookup"><span data-stu-id="b4406-190">Name</span></span>                      | <span data-ttu-id="b4406-191">Descrição</span><span class="sxs-lookup"><span data-stu-id="b4406-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="b4406-192">Total de Chamadas</span><span class="sxs-lookup"><span data-stu-id="b4406-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="b4406-193">Chamadas atuais</span><span class="sxs-lookup"><span data-stu-id="b4406-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="b4406-194">Total de chamadas com falha</span><span class="sxs-lookup"><span data-stu-id="b4406-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="b4406-195">Prazo total de chamadas excedido</span><span class="sxs-lookup"><span data-stu-id="b4406-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="b4406-196">Total de Mensagens Enviadas</span><span class="sxs-lookup"><span data-stu-id="b4406-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="b4406-197">Total de mensagens recebidas</span><span class="sxs-lookup"><span data-stu-id="b4406-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="b4406-198">Total de chamadas não implementadas</span><span class="sxs-lookup"><span data-stu-id="b4406-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="b4406-199">ASP.NET Core também fornece suas próprias métricas na `Microsoft.AspNetCore.Hosting` origem do evento.</span><span class="sxs-lookup"><span data-stu-id="b4406-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="b4406-200">métricas do cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="b4406-200">gRPC client metrics</span></span>

<span data-ttu-id="b4406-201">as métricas do cliente gRPC são relatadas na `Grpc.Net.Client` origem do evento.</span><span class="sxs-lookup"><span data-stu-id="b4406-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="b4406-202">Nome</span><span class="sxs-lookup"><span data-stu-id="b4406-202">Name</span></span>                      | <span data-ttu-id="b4406-203">Descrição</span><span class="sxs-lookup"><span data-stu-id="b4406-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="b4406-204">Total de Chamadas</span><span class="sxs-lookup"><span data-stu-id="b4406-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="b4406-205">Chamadas atuais</span><span class="sxs-lookup"><span data-stu-id="b4406-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="b4406-206">Total de chamadas com falha</span><span class="sxs-lookup"><span data-stu-id="b4406-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="b4406-207">Prazo total de chamadas excedido</span><span class="sxs-lookup"><span data-stu-id="b4406-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="b4406-208">Total de Mensagens Enviadas</span><span class="sxs-lookup"><span data-stu-id="b4406-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="b4406-209">Total de mensagens recebidas</span><span class="sxs-lookup"><span data-stu-id="b4406-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="b4406-210">Observar métricas</span><span class="sxs-lookup"><span data-stu-id="b4406-210">Observe metrics</span></span>

<span data-ttu-id="b4406-211">[dotnet-os contadores](/dotnet/core/diagnostics/dotnet-counters) são uma ferramenta de monitoramento de desempenho para monitoramento de integridade ad hoc e investigação de desempenho de primeiro nível.</span><span class="sxs-lookup"><span data-stu-id="b4406-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="b4406-212">Monitore um aplicativo .NET com `Grpc.AspNetCore.Server` `Grpc.Net.Client` o ou como o nome do provedor.</span><span class="sxs-lookup"><span data-stu-id="b4406-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="b4406-213">Outra maneira de observar as métricas do gRPC é capturar dados do contador usando [o pacote Microsoft. ApplicationInsights. EventCounterCollector](/azure/azure-monitor/app/eventcounters)da Application insights.</span><span class="sxs-lookup"><span data-stu-id="b4406-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="b4406-214">Depois da instalação, o Application Insights coleta contadores comuns do .NET em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b4406-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="b4406-215">os contadores de gRPC não são coletados por padrão, mas o app insights pode ser [personalizado para incluir contadores adicionais](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="b4406-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="b4406-216">Especifique os contadores de gRPC para o Application insights coletar em *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="b4406-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs* :</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="b4406-217">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b4406-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>