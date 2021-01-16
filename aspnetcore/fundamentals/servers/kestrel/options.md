---
title: Configurar opções para o servidor Web ASP.NET Core Kestrel
author: rick-anderson
description: Saiba mais sobre como configurar opções para o Kestrel, o servidor Web de plataforma cruzada para ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253914"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="50a68-103">Configurar opções para o servidor Web ASP.NET Core Kestrel</span><span class="sxs-lookup"><span data-stu-id="50a68-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="50a68-104">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="50a68-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="50a68-105">Para fornecer configuração adicional após chamar `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="50a68-105">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="50a68-106">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="50a68-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="50a68-107">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="50a68-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="50a68-108">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="50a68-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="50a68-109">Nos exemplos mostrados posteriormente neste artigo, as opções de Kestrel são configuradas no código C#.</span><span class="sxs-lookup"><span data-stu-id="50a68-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="50a68-110">As opções de Kestrel também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="50a68-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="50a68-111">Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider) pode carregar a configuração Kestrel de um *appsettings.json* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="50a68-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="50a68-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> e a [configuração do ponto de extremidade](xref:fundamentals/servers/kestrel/endpoints) são configuráveis de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="50a68-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="50a68-113">A configuração restante do Kestrel deve ser configurada no código C#.</span><span class="sxs-lookup"><span data-stu-id="50a68-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="50a68-114">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="50a68-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="50a68-115">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50a68-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="50a68-116">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="50a68-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="50a68-117">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="50a68-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="50a68-118">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="50a68-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="50a68-119">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="50a68-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="50a68-120">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="50a68-120">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="50a68-121">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="50a68-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="50a68-122">Limites gerais</span><span class="sxs-lookup"><span data-stu-id="50a68-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="50a68-123">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="50a68-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="50a68-124">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="50a68-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="50a68-125">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="50a68-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="50a68-126">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="50a68-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="50a68-127">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="50a68-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="50a68-128">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="50a68-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="50a68-129">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="50a68-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="50a68-130">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="50a68-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="50a68-131">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="50a68-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="50a68-132">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="50a68-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="50a68-133">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="50a68-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="50a68-134">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="50a68-134">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="50a68-135">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="50a68-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="50a68-136">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="50a68-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="50a68-137">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="50a68-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="50a68-138">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo de solicitação do Kestrel é desabilitado.</span><span class="sxs-lookup"><span data-stu-id="50a68-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="50a68-139">O IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="50a68-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="50a68-140">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="50a68-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="50a68-141">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="50a68-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="50a68-142">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel permite que o cliente aumente sua taxa de envio até o mínimo.</span><span class="sxs-lookup"><span data-stu-id="50a68-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="50a68-143">A taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="50a68-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="50a68-144">O período de carência ajuda a evitar a remoção de conexões que estão enviando dados inicialmente a uma taxa lenta devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="50a68-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="50a68-145">A taxa mínima padrão é de 240 bytes/segundo com um período de carência de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="50a68-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="50a68-146">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="50a68-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="50a68-147">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="50a68-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="50a68-148">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="50a68-148">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="50a68-149">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="50a68-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="50a68-150">O <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referido no exemplo anterior não está presente no `HttpContext.Features` para solicitações HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="50a68-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in `HttpContext.Features` for HTTP/2 requests.</span></span> <span data-ttu-id="50a68-151">A modificação de limites de taxa em uma base por solicitação geralmente não tem suporte para HTTP/2 devido ao suporte do protocolo para multiplexação de solicitação.</span><span class="sxs-lookup"><span data-stu-id="50a68-151">Modifying rate limits on a per-request basis is generally not supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="50a68-152">No entanto, o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ainda está presente em `HttpContext.Features` para solicitações HTTP/2, pois o limite de taxa de leitura ainda pode ser *desabilitado totalmente* em cada solicitação, definindo `IHttpMinRequestBodyDataRateFeature.MinDataRate` para `null` mesmo em uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="50a68-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="50a68-153">Tentar ler `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou tentar defini-lo como um valor diferente de `null` resultará na geração de um `NotSupportedException` devido a uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="50a68-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="50a68-154">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="50a68-154">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="50a68-155">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="50a68-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="50a68-156">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="50a68-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="50a68-157">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="50a68-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="50a68-158">Limites de HTTP/2</span><span class="sxs-lookup"><span data-stu-id="50a68-158">HTTP/2 limits</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="50a68-159">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="50a68-159">Maximum streams per connection</span></span>

<span data-ttu-id="50a68-160">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="50a68-160">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="50a68-161">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="50a68-161">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="50a68-162">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="50a68-162">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="50a68-163">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="50a68-163">Header table size</span></span>

<span data-ttu-id="50a68-164">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="50a68-164">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="50a68-165">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="50a68-165">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="50a68-166">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="50a68-166">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="50a68-167">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="50a68-167">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="50a68-168">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="50a68-168">Maximum frame size</span></span>

<span data-ttu-id="50a68-169">`Http2.MaxFrameSize` indica o tamanho máximo permitido de um conteúdo de quadro de conexão HTTP/2 recebido ou enviado pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="50a68-169">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="50a68-170">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="50a68-170">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="50a68-171">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="50a68-171">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="50a68-172">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="50a68-172">Maximum request header size</span></span>

<span data-ttu-id="50a68-173">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="50a68-173">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="50a68-174">Esse limite se aplica tanto ao nome quanto ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="50a68-174">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="50a68-175">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="50a68-175">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="50a68-176">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="50a68-176">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="50a68-177">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="50a68-177">Initial connection window size</span></span>

<span data-ttu-id="50a68-178">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="50a68-178">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="50a68-179">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="50a68-179">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="50a68-180">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="50a68-180">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="50a68-181">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="50a68-181">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="50a68-182">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="50a68-182">Initial stream window size</span></span>

<span data-ttu-id="50a68-183">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="50a68-183">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="50a68-184">As solicitações também são limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="50a68-184">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="50a68-185">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="50a68-185">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="50a68-186">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="50a68-186">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="50a68-187">Configuração de ping de Keep Alive do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="50a68-187">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="50a68-188">O Kestrel pode ser configurado para enviar pings HTTP/2 para clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="50a68-188">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="50a68-189">Os pings HTTP/2 atendem a várias finalidades:</span><span class="sxs-lookup"><span data-stu-id="50a68-189">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="50a68-190">Mantenha as conexões ociosas ativas.</span><span class="sxs-lookup"><span data-stu-id="50a68-190">Keep idle connections alive.</span></span> <span data-ttu-id="50a68-191">Alguns clientes e servidores proxy fecham as conexões que estão ociosas.</span><span class="sxs-lookup"><span data-stu-id="50a68-191">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="50a68-192">Os pings HTTP/2 são considerados como atividade em uma conexão e impedem que a conexão seja fechada como ociosa.</span><span class="sxs-lookup"><span data-stu-id="50a68-192">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="50a68-193">Feche as conexões não íntegras.</span><span class="sxs-lookup"><span data-stu-id="50a68-193">Close unhealthy connections.</span></span> <span data-ttu-id="50a68-194">As conexões nas quais o cliente não responde ao ping Keep Alive no tempo configurado são fechadas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="50a68-194">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="50a68-195">Há duas opções de configuração relacionadas a pings de Keep Alive HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="50a68-195">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="50a68-196">`Http2.KeepAlivePingInterval` é um `TimeSpan` que configura o intervalo de ping.</span><span class="sxs-lookup"><span data-stu-id="50a68-196">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping interval.</span></span> <span data-ttu-id="50a68-197">O servidor enviará um ping de Keep Alive para o cliente se ele não receber nenhum quadro para esse período de tempo.</span><span class="sxs-lookup"><span data-stu-id="50a68-197">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="50a68-198">Os pings de Keep Alive são desabilitados quando essa opção é definida como `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="50a68-198">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="50a68-199">O valor padrão é `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="50a68-199">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="50a68-200">`Http2.KeepAlivePingTimeout` é um `TimeSpan` que configura o tempo limite de ping.</span><span class="sxs-lookup"><span data-stu-id="50a68-200">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="50a68-201">Se o servidor não receber nenhum quadro, como um ping de resposta, durante esse tempo limite, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="50a68-201">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="50a68-202">O tempo limite de Keep Alive é desabilitado quando essa opção é definida como `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="50a68-202">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="50a68-203">O valor padrão é 20 segundos.</span><span class="sxs-lookup"><span data-stu-id="50a68-203">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="50a68-204">Outras opções</span><span class="sxs-lookup"><span data-stu-id="50a68-204">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="50a68-205">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="50a68-205">Synchronous I/O</span></span>

<span data-ttu-id="50a68-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="50a68-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="50a68-207">O valor padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="50a68-207">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="50a68-208">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="50a68-208">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="50a68-209">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="50a68-209">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="50a68-210">O exemplo a seguir habilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="50a68-210">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="50a68-211">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="50a68-211">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
