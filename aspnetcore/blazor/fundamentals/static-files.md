---
title: ASP.NET Core Blazor arquivos estáticos
author: guardrex
description: Saiba como configurar e gerenciar arquivos estáticos para Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/static-files
ms.openlocfilehash: 709250251113014027fe86c6a373e58a9c2a5009
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100110002"
---
# <a name="aspnet-core-blazor-static-files"></a><span data-ttu-id="5ce5e-103">ASP.NET Core Blazor arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="5ce5e-103">ASP.NET Core Blazor static files</span></span>

<span data-ttu-id="5ce5e-104">*Este artigo aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="5ce5e-104">*This article applies to Blazor Server.*</span></span>

<span data-ttu-id="5ce5e-105">Para criar mapeamentos de arquivo adicionais com um <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou configurar outros <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , use **uma** das abordagens a seguir.</span><span class="sxs-lookup"><span data-stu-id="5ce5e-105">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="5ce5e-106">Nos exemplos a seguir, o `{EXTENSION}` espaço reservado é a extensão de arquivo e o `{CONTENT TYPE}` espaço reservado é o tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="5ce5e-106">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="5ce5e-107">Configure opções por [injeção de dependência (di)](xref:blazor/fundamentals/dependency-injection) em `Startup.ConfigureServices` ( `Startup.cs` ) usando <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="5ce5e-107">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="5ce5e-108">Como essa abordagem configura o mesmo provedor de arquivos usado para servir `blazor.server.js` , certifique-se de que sua configuração personalizada não interfira no fornecimento `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="5ce5e-108">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="5ce5e-109">Por exemplo, não remova o mapeamento de arquivos JavaScript Configurando o provedor com `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="5ce5e-109">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="5ce5e-110">Use duas chamadas para <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> no `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="5ce5e-110">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="5ce5e-111">Configure o provedor de arquivo personalizado na primeira chamada com <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="5ce5e-111">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="5ce5e-112">O segundo middleware serve `blazor.server.js` , que usa a configuração de arquivos estáticos padrão fornecida pela Blazor estrutura.</span><span class="sxs-lookup"><span data-stu-id="5ce5e-112">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="5ce5e-113">Você pode evitar interferir no serviço `_framework/blazor.server.js` usando <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> o para executar um middleware de arquivo estático personalizado:</span><span class="sxs-lookup"><span data-stu-id="5ce5e-113">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```
