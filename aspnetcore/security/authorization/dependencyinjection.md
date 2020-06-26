---
title: Injeção de dependência em manipuladores de requisitos no ASP.NET Core
author: rick-anderson
description: Saiba como injetar manipuladores de requisitos de autorização em um aplicativo ASP.NET Core usando injeção de dependência.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: d12253ad1c1442c0db5cd497393daabe280fae8d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406349"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="b6e5e-103">Injeção de dependência em manipuladores de requisitos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6e5e-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="b6e5e-104">Os [manipuladores de autorização devem ser registrados](xref:security/authorization/policies#handler-registration) na coleção de serviços durante a configuração (usando a [injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="b6e5e-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="b6e5e-105">Suponha que você tenha um repositório de regras que deseja avaliar dentro de um manipulador de autorização e que o repositório tenha sido registrado na coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="b6e5e-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="b6e5e-106">A autorização irá resolver e injetar isso em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="b6e5e-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="b6e5e-107">Por exemplo, se você quisesse usar o ASP. A infraestrutura de registro em log da rede que você deseja injetar `ILoggerFactory` em seu manipulador.</span><span class="sxs-lookup"><span data-stu-id="b6e5e-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="b6e5e-108">Esse manipulador pode ser semelhante a:</span><span class="sxs-lookup"><span data-stu-id="b6e5e-108">Such a handler might look like:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="b6e5e-109">Você registraria o manipulador com `services.AddSingleton()` :</span><span class="sxs-lookup"><span data-stu-id="b6e5e-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="b6e5e-110">Uma instância do manipulador será criada quando seu aplicativo for iniciado e DI irá injetar o registrado `ILoggerFactory` em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="b6e5e-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="b6e5e-111">Os manipuladores que usam Entity Framework não devem ser registrados como singletons.</span><span class="sxs-lookup"><span data-stu-id="b6e5e-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
