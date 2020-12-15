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
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="aa2dc-103">Log de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="aa2dc-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="aa2dc-104">Configure o log personalizado em Blazor WebAssembly aplicativos com a <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> propriedade.</span><span class="sxs-lookup"><span data-stu-id="aa2dc-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="aa2dc-105">Adicione o namespace para <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="aa2dc-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="aa2dc-106">No `Program.Main` `Program.cs` , defina o nível de log mínimo com <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> e adicione o provedor de log personalizado:</span><span class="sxs-lookup"><span data-stu-id="aa2dc-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="aa2dc-107">A `Logging` propriedade é do tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , portanto, todos os métodos de extensão disponíveis no <xref:Microsoft.Extensions.Logging.ILoggingBuilder> também estão disponíveis no `Logging` .</span><span class="sxs-lookup"><span data-stu-id="aa2dc-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="aa2dc-108">A configuração de log pode ser carregada de arquivos de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aa2dc-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="aa2dc-109">Para obter mais informações, consulte <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="aa2dc-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="aa2dc-110">SignalR Log de cliente .NET</span><span class="sxs-lookup"><span data-stu-id="aa2dc-110">SignalR .NET client logging</span></span>

<span data-ttu-id="aa2dc-111">Insira um <xref:Microsoft.Extensions.Logging.ILoggerProvider> para adicionar um `WebAssemblyConsoleLogger` aos provedores de log passados para <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aa2dc-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="aa2dc-112">Ao contrário de um tradicional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` é um wrapper em relação às APIs de log específicas do navegador (por exemplo, `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="aa2dc-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="aa2dc-113">O uso de `WebAssemblyConsoleLogger` torna o registro em log possível no mono dentro de um contexto do navegador.</span><span class="sxs-lookup"><span data-stu-id="aa2dc-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

<span data-ttu-id="aa2dc-114">Adicione o namespace para <xref:Microsoft.Extensions.Logging?displayProperty=fullName> e insira um <xref:Microsoft.Extensions.Logging.ILoggerProvider> no componente:</span><span class="sxs-lookup"><span data-stu-id="aa2dc-114">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="aa2dc-115">No [ `OnInitializedAsync` método](xref:blazor/components/lifecycle#component-initialization-methods)do componente, use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="aa2dc-115">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="aa2dc-116">Para obter diretrizes gerais de log de ASP.NET Core relacionadas ao Blazor Server , consulte <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="aa2dc-116">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="aa2dc-117">Componentes de logon Razor</span><span class="sxs-lookup"><span data-stu-id="aa2dc-117">Log in Razor components</span></span>

<span data-ttu-id="aa2dc-118">Os agentes respeitam a configuração de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aa2dc-118">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="aa2dc-119">A `using` diretiva do <xref:Microsoft.Extensions.Logging> é necessária para dar suporte a conclusões do [IntelliSense](/visualstudio/ide/using-intellisense) para APIs, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="aa2dc-119">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="aa2dc-120">O exemplo a seguir demonstra o registro em log com um <xref:Microsoft.Extensions.Logging.ILogger> nos componentes do.</span><span class="sxs-lookup"><span data-stu-id="aa2dc-120">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="aa2dc-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="aa2dc-121">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="aa2dc-122">O exemplo a seguir demonstra o registro em log com um <xref:Microsoft.Extensions.Logging.ILoggerFactory> nos componentes do.</span><span class="sxs-lookup"><span data-stu-id="aa2dc-122">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="aa2dc-123">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="aa2dc-123">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="aa2dc-124">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="aa2dc-124">Additional resources</span></span>

* <xref:fundamentals/logging/index>
