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
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>Configurar opções para o servidor Web ASP.NET Core Kestrel

O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.

Para fornecer configuração adicional após chamar `ConfigureWebHostDefaults`, use `ConfigureKestrel`:

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

Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Nos exemplos mostrados posteriormente neste artigo, as opções de Kestrel são configuradas no código C#. As opções de Kestrel também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index). Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider) pode carregar a configuração Kestrel de um *appsettings.json* ou *appSettings. { Arquivo de ambiente}. JSON* :

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> e a [configuração do ponto de extremidade](xref:fundamentals/servers/kestrel/endpoints) são configuráveis de provedores de configuração. A configuração restante do Kestrel deve ser configurada no código C#.

Use **uma** das seguintes abordagens:

* Configurar Kestrel em `Startup.ConfigureServices` :

  1. Injetar uma instância do `IConfiguration` na `Startup` classe. O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.
  2. No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:

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

* Configure o Kestrel ao compilar o host:

  No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:

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

Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).

## <a name="general-limits"></a>Limites gerais

### <a name="keep-alive-timeout"></a>Tempo limite de keep-alive

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5). O padrão é de dois minutos.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Número máximo de conexões de cliente

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets). Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

O número máximo de conexões é ilimitado (nulo) por padrão.

### <a name="maximum-request-body-size"></a>Tamanho máximo do corpo da solicitação

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.

A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Substitua a configuração em uma solicitação específica no middleware:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação. Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.

Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo de solicitação do Kestrel é desabilitado. O IIS já define o limite.

### <a name="minimum-request-body-data-rate"></a>Taxa de dados mínima do corpo da solicitação

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo. Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel permite que o cliente aumente sua taxa de envio até o mínimo. A taxa não é verificada durante esse período. O período de carência ajuda a evitar a remoção de conexões que estão enviando dados inicialmente a uma taxa lenta devido ao início lento do TCP.

A taxa mínima padrão é de 240 bytes/segundo com um período de carência de 5 segundos.

Uma taxa mínima também se aplica à resposta. O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.

Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Substitua os limites de taxa mínimo por solicitação no middleware:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

O <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referido no exemplo anterior não está presente no `HttpContext.Features` para solicitações HTTP/2. A modificação de limites de taxa em uma base por solicitação geralmente não tem suporte para HTTP/2 devido ao suporte do protocolo para multiplexação de solicitação. No entanto, o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ainda está presente em `HttpContext.Features` para solicitações HTTP/2, pois o limite de taxa de leitura ainda pode ser *desabilitado totalmente* em cada solicitação, definindo `IHttpMinRequestBodyDataRateFeature.MinDataRate` para `null` mesmo em uma solicitação HTTP/2. Tentar ler `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou tentar defini-lo como um valor diferente de `null` resultará na geração de um `NotSupportedException` devido a uma solicitação HTTP/2.

Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.

### <a name="request-headers-timeout"></a>Tempo limite dos cabeçalhos de solicitação

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação. O padrão é 30 segundos.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>Limites de HTTP/2

### <a name="maximum-streams-per-connection"></a>Fluxos máximos por conexão

O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2. Os fluxos em excesso são recusados.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

O valor padrão é 100.

### <a name="header-table-size"></a>Tamanho da tabela de cabeçalho

O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2. O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK. O valor é fornecido em octetos e deve ser maior do que zero (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

O valor padrão é 4096.

### <a name="maximum-frame-size"></a>Tamanho máximo do quadro

`Http2.MaxFrameSize` indica o tamanho máximo permitido de um conteúdo de quadro de conexão HTTP/2 recebido ou enviado pelo servidor. O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

O valor padrão é 2^14 (16.384).

### <a name="maximum-request-header-size"></a>Tamanho máximo do cabeçalho de solicitação

`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação. Esse limite se aplica tanto ao nome quanto ao valor em suas representações compactadas e descompactadas. O valor deve ser maior que zero (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

O valor padrão é 8.192.

### <a name="initial-connection-window-size"></a>Tamanho inicial da janela de conexão

`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão. As solicitações também são limitadas por `Http2.InitialStreamWindowSize`. O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

O valor padrão é 128 KB (131.072).

### <a name="initial-stream-window-size"></a>Tamanho inicial da janela de fluxo

`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo). As solicitações também são limitadas por `Http2.InitialConnectionWindowSize`. O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

O valor padrão é 96 KB (98.304).

### <a name="http2-keep-alive-ping-configuration"></a>Configuração de ping de Keep Alive do HTTP/2

O Kestrel pode ser configurado para enviar pings HTTP/2 para clientes conectados. Os pings HTTP/2 atendem a várias finalidades:

* Mantenha as conexões ociosas ativas. Alguns clientes e servidores proxy fecham as conexões que estão ociosas. Os pings HTTP/2 são considerados como atividade em uma conexão e impedem que a conexão seja fechada como ociosa.
* Feche as conexões não íntegras. As conexões nas quais o cliente não responde ao ping Keep Alive no tempo configurado são fechadas pelo servidor.

Há duas opções de configuração relacionadas a pings de Keep Alive HTTP/2:

* `Http2.KeepAlivePingInterval` é um `TimeSpan` que configura o intervalo de ping. O servidor enviará um ping de Keep Alive para o cliente se ele não receber nenhum quadro para esse período de tempo. Os pings de Keep Alive são desabilitados quando essa opção é definida como `TimeSpan.MaxValue` . O valor padrão é `TimeSpan.MaxValue`.
* `Http2.KeepAlivePingTimeout` é um `TimeSpan` que configura o tempo limite de ping. Se o servidor não receber nenhum quadro, como um ping de resposta, durante esse tempo limite, a conexão será fechada. O tempo limite de Keep Alive é desabilitado quando essa opção é definida como `TimeSpan.MaxValue` . O valor padrão é 20 segundos.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>Outras opções

### <a name="synchronous-io"></a>E/S Síncrona

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a e/s síncrona é permitida para a solicitação e a resposta. O valor padrão é `false`.

> [!WARNING]
> Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta. Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.

O exemplo a seguir habilita e/s síncrona:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Para obter informações sobre outras opções e limites do Kestrel, confira:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
