---
title: Migre de Microsoft. Extensions. Logging 2,1 para 2,2 ou 3,0
author: pakrym
description: Saiba como migrar um aplicativo non-ASP.NET Core que usa Microsoft. Extensions. Logging de 2,1 para 2,2 ou 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 1082ae494aae7a31b195b32dfedae81c60fe3eed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060372"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migre de Microsoft. Extensions. Logging 2,1 para 2,2 ou 3,0

Este artigo descreve as etapas comuns para migrar um aplicativo non-ASP.NET Core que usa `Microsoft.Extensions.Logging` de 2,1 para 2,2 ou 3,0.

## <a name="21-to-22"></a>2.1 a 2.2

Criar `ServiceCollection` e chamar manualmente `AddLogging` .

2,1 exemplo:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2,2 exemplo:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2,1 a 3,0

Em 3,0, use `LoggingFactory.Create` .

2,1 exemplo:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3,0 exemplo:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Recursos adicionais

* [Pacote NuGet Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>
