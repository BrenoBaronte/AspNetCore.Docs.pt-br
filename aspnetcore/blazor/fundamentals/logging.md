---
title: Log de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre como fazer logon em Blazor aplicativos, incluindo a configuração de nível de log e como gravar mensagens de log de Razor componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 78117fa6e9c7d5aed3fb31bbd3afee55b3b5b875
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506702"
---
# <a name="aspnet-core-no-locblazor-logging"></a>Log de ASP.NET Core Blazor

::: zone pivot="webassembly"

Configure o log personalizado em Blazor WebAssembly aplicativos com a <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> propriedade.

Adicione o namespace para <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> para `Program.cs` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

No `Program.Main` `Program.cs` , defina o nível de log mínimo com <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> e adicione o provedor de log personalizado:

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

A `Logging` propriedade é do tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , portanto, todos os métodos de extensão disponíveis no <xref:Microsoft.Extensions.Logging.ILoggingBuilder> também estão disponíveis no `Logging` .

A configuração de log pode ser carregada de arquivos de configurações do aplicativo. Para obter mais informações, consulte <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="no-locsignalr-net-client-logging"></a>SignalR Log de cliente .NET

Insira um <xref:Microsoft.Extensions.Logging.ILoggerProvider> para adicionar um `WebAssemblyConsoleLogger` aos provedores de log passados para <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Ao contrário de um tradicional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` é um wrapper em relação às APIs de log específicas do navegador (por exemplo, `console.log` ). O uso de `WebAssemblyConsoleLogger` torna o registro em log possível no mono dentro de um contexto do navegador.

Adicione o namespace para <xref:Microsoft.Extensions.Logging?displayProperty=fullName> e insira um <xref:Microsoft.Extensions.Logging.ILoggerProvider> no componente:

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

No [ `OnInitializedAsync` método](xref:blazor/components/lifecycle#component-initialization-methods)do componente, use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

Para obter diretrizes gerais de log de ASP.NET Core relacionadas ao Blazor Server , consulte <xref:fundamentals/logging/index> .

::: zone-end

## <a name="log-in-no-locrazor-components"></a>Componentes de logon Razor

Os agentes respeitam a configuração de inicialização do aplicativo.

A `using` diretiva do <xref:Microsoft.Extensions.Logging> é necessária para dar suporte a conclusões do [IntelliSense](/visualstudio/ide/using-intellisense) para APIs, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .

O exemplo a seguir demonstra o registro em log com um <xref:Microsoft.Extensions.Logging.ILogger> nos componentes do.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

O exemplo a seguir demonstra o registro em log com um <xref:Microsoft.Extensions.Logging.ILoggerFactory> nos componentes do.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/logging/index>
