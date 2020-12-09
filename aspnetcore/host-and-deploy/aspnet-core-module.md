---
title: Módulo do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o módulo ASP.NET Core para hospedar ASP.NET Core aplicativos com o IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: d0e6c0c31890c58aaca936fc6f1e92cb9a1ab456
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901230"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="df5ee-103">Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df5ee-103">ASP.NET Core Module</span></span>

<span data-ttu-id="df5ee-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)e [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="df5ee-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="df5ee-105">O módulo ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS, permitindo que ASP.NET Core aplicativos funcionem com o IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="df5ee-106">Execute ASP.NET Core aplicativos com o IIS:</span><span class="sxs-lookup"><span data-stu-id="df5ee-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="df5ee-107">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS ( `w3wp.exe` ), chamado de [modelo de hospedagem em processo](xref:host-and-deploy/iis/in-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="df5ee-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="df5ee-108">Encaminhamento de solicitações da Web para um aplicativo de ASP.NET Core de back-end que executa o servidor Kestrel, chamado de [modelo de hospedagem fora do processo](xref:host-and-deploy/iis/out-of-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="df5ee-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="df5ee-109">Há compensações entre cada um dos modelos de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="df5ee-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="df5ee-110">Por padrão, o modelo de hospedagem em processo é usado devido a um melhor desempenho e diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="df5ee-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="df5ee-111">Instalar o módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df5ee-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="df5ee-112">Baixe o instalador usando o seguinte link:</span><span class="sxs-lookup"><span data-stu-id="df5ee-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="df5ee-113">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="df5ee-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="df5ee-114">Para obter mais detalhes sobre como instalar o módulo ASP.NET Core ou instalar versões diferentes do módulo, consulte [instalar o pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="df5ee-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="df5ee-115">Para uma experiência de tutorial sobre como publicar um aplicativo de ASP.NET Core em um servidor IIS, confira <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="df5ee-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="df5ee-116">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="df5ee-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="df5ee-117">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="df5ee-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="df5ee-118">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="df5ee-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="df5ee-119">Versões do Windows com suporte:</span><span class="sxs-lookup"><span data-stu-id="df5ee-119">Supported Windows versions:</span></span>

* <span data-ttu-id="df5ee-120">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="df5ee-120">Windows 7 or later</span></span>
* <span data-ttu-id="df5ee-121">Windows Server 2012 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="df5ee-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="df5ee-122">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="df5ee-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="df5ee-123">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="df5ee-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="df5ee-124">O módulo não funciona com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="df5ee-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="df5ee-125">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="df5ee-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="df5ee-126">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="df5ee-126">In-process hosting model</span></span>

<span data-ttu-id="df5ee-127">ASP.NET Core aplicativos assumem como padrão o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="df5ee-128">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="df5ee-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="df5ee-129">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="df5ee-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="df5ee-130">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="df5ee-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="df5ee-131">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="df5ee-132">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df5ee-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="df5ee-133">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="df5ee-134">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="df5ee-135">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="df5ee-136">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-136">Use one app pool per app.</span></span>

* <span data-ttu-id="df5ee-137">Ao usar [implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou colocar manualmente um [ `app_offline.htm` arquivo na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo poderá não ser desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="df5ee-138">Por exemplo, uma conexão WebSocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="df5ee-139">A arquitetura (número de bit) do aplicativo e o runtime instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="df5ee-140">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="df5ee-140">Client disconnects are detected.</span></span> <span data-ttu-id="df5ee-141">O [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token de cancelamento é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="df5ee-142">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez do diretório do aplicativo (por exemplo, `C:\Windows\System32\inetsrv` para `w3wp.exe` ).</span><span class="sxs-lookup"><span data-stu-id="df5ee-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="df5ee-143">Para obter um exemplo de código que define o diretório atual do aplicativo, consulte a [ `CurrentDirectoryHelpers` classe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="df5ee-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="df5ee-144">Chame o método `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="df5ee-145">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="df5ee-146">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="df5ee-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="df5ee-147">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="df5ee-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="df5ee-148">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="df5ee-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="df5ee-149">Não há suporte para [implantações de pacote da Web (arquivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) .</span><span class="sxs-lookup"><span data-stu-id="df5ee-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="df5ee-150">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="df5ee-150">Out-of-process hosting model</span></span>

<span data-ttu-id="df5ee-151">Para configurar um aplicativo para hospedagem fora do processo, defina o valor da `<AspNetCoreHostingModel>` propriedade como `OutOfProcess` no arquivo de projeto ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="df5ee-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="df5ee-152">A hospedagem em processo é definida com `InProcess` , que é o valor padrão.</span><span class="sxs-lookup"><span data-stu-id="df5ee-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="df5ee-153">O valor de `<AspNetCoreHostingModel>` não diferencia maiúsculas de minúsculas, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="df5ee-154">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="df5ee-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="df5ee-155">Para o fora do processo, o [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="df5ee-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="df5ee-156">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df5ee-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="df5ee-157">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="df5ee-158">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="df5ee-158">Hosting model changes</span></span>

<span data-ttu-id="df5ee-159">Se a `hostingModel` configuração for alterada no `web.config` arquivo (explicado na seção [configuração com `web.config` ](#configuration-with-webconfig) ), o módulo reciclará o processo de trabalho para o IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="df5ee-160">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="df5ee-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="df5ee-161">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="df5ee-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="df5ee-162">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="df5ee-162">Process name</span></span>

<span data-ttu-id="df5ee-163">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="df5ee-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="df5ee-164">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="df5ee-165">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="df5ee-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="df5ee-166">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="df5ee-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="df5ee-167">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="df5ee-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="df5ee-168">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="df5ee-169">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="df5ee-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="df5ee-170">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df5ee-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="df5ee-171">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="df5ee-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="df5ee-172">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="df5ee-172">Configuration with web.config</span></span>

<span data-ttu-id="df5ee-173">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="df5ee-174">O arquivo a seguir `web.config` é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o módulo ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="df5ee-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="df5ee-175">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="df5ee-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="df5ee-176">A <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriedade é definida como `false` para indicar que as configurações especificadas no [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemento não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="df5ee-177">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="df5ee-178">O caminho salva os logs de stdout na `LogFiles` pasta, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="df5ee-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="df5ee-179">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="df5ee-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="df5ee-180">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="df5ee-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="df5ee-181">Atributo</span><span class="sxs-lookup"><span data-stu-id="df5ee-181">Attribute</span></span> | <span data-ttu-id="df5ee-182">Descrição</span><span class="sxs-lookup"><span data-stu-id="df5ee-182">Description</span></span> | <span data-ttu-id="df5ee-183">Padrão</span><span class="sxs-lookup"><span data-stu-id="df5ee-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="df5ee-184">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-184">Optional string attribute.</span></span></p><p><span data-ttu-id="df5ee-185">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="df5ee-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="df5ee-186">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-187">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="df5ee-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="df5ee-188">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-189">Se for true, o token será encaminhado para o processo filho escutando `%ASPNETCORE_PORT%` como um cabeçalho `'MS-ASPNETCORE-WINAUTHTOKEN'` por solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="df5ee-190">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="df5ee-191">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-191">Optional string attribute.</span></span></p><p><span data-ttu-id="df5ee-192">Especifica o modelo de hospedagem como em processo ( `InProcess` / `inprocess` ) ou fora do processo ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="df5ee-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="df5ee-193">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-194">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="df5ee-195">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="df5ee-196">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="df5ee-197">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="df5ee-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="df5ee-198">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="df5ee-198">Default: `1`</span></span><br><span data-ttu-id="df5ee-199">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="df5ee-199">Min: `1`</span></span><br><span data-ttu-id="df5ee-200">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="df5ee-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="df5ee-201">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="df5ee-201">Required string attribute.</span></span></p><p><span data-ttu-id="df5ee-202">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="df5ee-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="df5ee-203">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="df5ee-203">Relative paths are supported.</span></span> <span data-ttu-id="df5ee-204">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="df5ee-205">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-206">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="df5ee-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="df5ee-207">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="df5ee-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="df5ee-208">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="df5ee-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="df5ee-209">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="df5ee-209">Default: `10`</span></span><br><span data-ttu-id="df5ee-210">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-210">Min: `0`</span></span><br><span data-ttu-id="df5ee-211">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="df5ee-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="df5ee-212">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="df5ee-213">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="df5ee-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="df5ee-214">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="df5ee-215">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="df5ee-216">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="df5ee-217">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="df5ee-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="df5ee-218">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="df5ee-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="df5ee-219">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-219">Default: `00:02:00`</span></span><br><span data-ttu-id="df5ee-220">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-220">Min: `00:00:00`</span></span><br><span data-ttu-id="df5ee-221">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="df5ee-222">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-223">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="df5ee-224">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="df5ee-224">Default: `10`</span></span><br><span data-ttu-id="df5ee-225">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-225">Min: `0`</span></span><br><span data-ttu-id="df5ee-226">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="df5ee-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="df5ee-227">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-228">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="df5ee-229">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="df5ee-230">Ao hospedar *em processo*: o processo **não** é reiniciado e **não usa a** configuração **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="df5ee-230">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="df5ee-231">Ao hospedar *fora do processo*: o módulo tenta reinicializar o processo quando recebe uma nova solicitação e continua tentando reiniciar o processo em solicitações de entrada subsequentes, a menos que o aplicativo não seja iniciado **rapidFailsPerMinute** número de vezes no último minuto de reversão.</span><span class="sxs-lookup"><span data-stu-id="df5ee-231">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="df5ee-232">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="df5ee-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="df5ee-233">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="df5ee-233">Default: `120`</span></span><br><span data-ttu-id="df5ee-234">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-234">Min: `0`</span></span><br><span data-ttu-id="df5ee-235">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="df5ee-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="df5ee-236">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-237">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="df5ee-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="df5ee-238">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-238">Optional string attribute.</span></span></p><p><span data-ttu-id="df5ee-239">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="df5ee-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="df5ee-240">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="df5ee-241">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="df5ee-242">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="df5ee-243">O uso de delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e uma extensão de arquivo ( `.log` ) são adicionados ao último segmento do caminho **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="df5ee-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="df5ee-244">Se `.\logs\stdout` for fornecido como um valor, um log de stdout de exemplo será salvo como `stdout_20180205194132_1934.log` na `logs` pasta quando salvo em 2/5/2018 às 19:41:32 com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="df5ee-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="df5ee-245">Definir variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="df5ee-245">Set environment variables</span></span>

<span data-ttu-id="df5ee-246">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="df5ee-247">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="df5ee-248">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="df5ee-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="df5ee-249">O exemplo a seguir define duas variáveis de ambiente no `web.config` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="df5ee-250">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="df5ee-251">Um desenvolvedor pode definir temporariamente esse valor no `web.config` arquivo para forçar a [página de exceção do desenvolvedor](xref:fundamentals/error-handling) a ser carregada durante a depuração de uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="df5ee-252">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="df5ee-253">Uma alternativa para definir o ambiente diretamente no `web.config` é incluir a `<EnvironmentName>` propriedade no perfil de [publicação ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="df5ee-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="df5ee-254">Essa abordagem define o ambiente em `web.config` que o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="df5ee-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="df5ee-255">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="df5ee-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="df5ee-256">Se um arquivo com o nome `app_offline.htm` for detectado no diretório raiz de um aplicativo, o módulo ASP.NET Core tentará desligar o aplicativo normalmente e interromperá o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="df5ee-257">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="df5ee-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="df5ee-258">Enquanto o `app_offline.htm` arquivo estiver presente, o módulo ASP.NET Core responde às solicitações enviando de volta o conteúdo do `app_offline.htm` arquivo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="df5ee-259">Quando o `app_offline.htm` arquivo é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="df5ee-260">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="df5ee-261">Por exemplo, uma conexão WebSocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="df5ee-262">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="df5ee-262">Start-up error page</span></span>

<span data-ttu-id="df5ee-263">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="df5ee-264">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="df5ee-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="df5ee-265">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="df5ee-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="df5ee-266">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="df5ee-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="df5ee-267">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="df5ee-268">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="df5ee-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="df5ee-269">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="df5ee-269">Log creation and redirection</span></span>

<span data-ttu-id="df5ee-270">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="df5ee-271">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="df5ee-272">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="df5ee-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="df5ee-273">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="df5ee-274">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="df5ee-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="df5ee-275">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="df5ee-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="df5ee-276">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="df5ee-277">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="df5ee-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="df5ee-278">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="df5ee-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="df5ee-279">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="df5ee-280">O nome do arquivo de log é composto acrescentando o carimbo de data/hora, a ID do processo e a extensão do arquivo ( `.log` ) ao último segmento do `stdoutLogFile` caminho (normalmente `stdout` ) delimitado por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="df5ee-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="df5ee-281">Se o `stdoutLogFile` caminho terminar com `stdout` , um log de um aplicativo com um PID de 1934 criado em 2/5/2018 às 19:42:32 terá o nome do arquivo `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="df5ee-282">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="df5ee-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="df5ee-283">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="df5ee-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="df5ee-284">O seguinte elemento de exemplo `aspNetCore` configura o log de stdout no caminho relativo `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="df5ee-285">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="df5ee-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="df5ee-286">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o `stdoutLogFile` valor como `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="df5ee-287">A `%home` variável de ambiente é predefinida para aplicativos hospedados pelo serviço de Azure app.</span><span class="sxs-lookup"><span data-stu-id="df5ee-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="df5ee-288">Para criar regras de filtro de log, consulte as seções de [configuração](xref:fundamentals/logging/index#log-filtering) e [filtragem de log](xref:fundamentals/logging/index#log-filtering) da documentação de log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df5ee-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="df5ee-289">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="df5ee-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="df5ee-290">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="df5ee-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="df5ee-291">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="df5ee-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="df5ee-292">Adicione o `<handlerSettings>` elemento ao `<aspNetCore>` elemento em `web.config` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="df5ee-293">A definição de `debugLevel` como `TRACE` expõe uma fidelidade maior de informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="df5ee-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="df5ee-294">Todas as pastas no caminho ( `logs` no exemplo anterior) são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="df5ee-295">O pool de aplicativos deve ter acesso de gravação ao local onde os logs são gravados (use `IIS AppPool\{APP POOL NAME}` , em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos, para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="df5ee-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="df5ee-296">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="df5ee-297">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="df5ee-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="df5ee-298">ERRO</span><span class="sxs-lookup"><span data-stu-id="df5ee-298">ERROR</span></span>
* <span data-ttu-id="df5ee-299">WARNING</span><span class="sxs-lookup"><span data-stu-id="df5ee-299">WARNING</span></span>
* <span data-ttu-id="df5ee-300">INFO</span><span class="sxs-lookup"><span data-stu-id="df5ee-300">INFO</span></span>
* <span data-ttu-id="df5ee-301">RASTREAMENTO</span><span class="sxs-lookup"><span data-stu-id="df5ee-301">TRACE</span></span>

<span data-ttu-id="df5ee-302">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="df5ee-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="df5ee-303">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="df5ee-303">CONSOLE</span></span>
* <span data-ttu-id="df5ee-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="df5ee-304">EVENTLOG</span></span>
* <span data-ttu-id="df5ee-305">FILE</span><span class="sxs-lookup"><span data-stu-id="df5ee-305">FILE</span></span>

<span data-ttu-id="df5ee-306">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="df5ee-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="df5ee-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="df5ee-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="df5ee-308">(Padrão: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="df5ee-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="df5ee-309">`ASPNETCORE_MODULE_DEBUG`: Configuração de nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="df5ee-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="df5ee-310">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="df5ee-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="df5ee-311">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-311">The size of the log isn't limited.</span></span> <span data-ttu-id="df5ee-312">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="df5ee-313">Consulte [configuração com web.config](#configuration-with-webconfig) para obter um exemplo do `aspNetCore` elemento no `web.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="df5ee-314">Modificar o tamanho da pilha</span><span class="sxs-lookup"><span data-stu-id="df5ee-314">Modify the stack size</span></span>

<span data-ttu-id="df5ee-315">*Aplica-se somente ao usar o modelo de hospedagem em processo.*</span><span class="sxs-lookup"><span data-stu-id="df5ee-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="df5ee-316">Configure o tamanho da pilha gerenciada usando a `stackSize` configuração em bytes em `web.config` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="df5ee-317">O tamanho padrão é 1.048.576 bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="df5ee-317">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="df5ee-318">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="df5ee-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="df5ee-319">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="df5ee-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="df5ee-320">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="df5ee-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="df5ee-321">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="df5ee-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="df5ee-322">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="df5ee-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="df5ee-323">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="df5ee-324">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="df5ee-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="df5ee-325">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="df5ee-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="df5ee-326">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="df5ee-327">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="df5ee-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="df5ee-328">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="df5ee-329">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="df5ee-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="df5ee-330">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="df5ee-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="df5ee-331">Como a conta do sistema local não tem permissão Modificar para o caminho de compartilhamento usado pela configuração compartilhada do IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações do módulo no `applicationHost.config` arquivo no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="df5ee-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="df5ee-332">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="df5ee-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="df5ee-333">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="df5ee-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="df5ee-334">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="df5ee-335">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="df5ee-335">Run the installer.</span></span>
1. <span data-ttu-id="df5ee-336">Exporte o `applicationHost.config` arquivo atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="df5ee-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="df5ee-337">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="df5ee-338">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="df5ee-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="df5ee-339">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="df5ee-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="df5ee-340">No sistema de hospedagem, navegue até `%windir%\System32\inetsrv` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="df5ee-341">Localize o `aspnetcore.dll` arquivo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="df5ee-342">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="df5ee-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="df5ee-343">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="df5ee-344">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="df5ee-345">O arquivo é denominado `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="df5ee-346">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="df5ee-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="df5ee-347">Módulo</span><span class="sxs-lookup"><span data-stu-id="df5ee-347">Module</span></span>

<span data-ttu-id="df5ee-348">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="df5ee-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="df5ee-349">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="df5ee-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="df5ee-350">Esquema</span><span class="sxs-lookup"><span data-stu-id="df5ee-350">Schema</span></span>

<span data-ttu-id="df5ee-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="df5ee-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="df5ee-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="df5ee-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="df5ee-353">Configuração</span><span class="sxs-lookup"><span data-stu-id="df5ee-353">Configuration</span></span>

<span data-ttu-id="df5ee-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="df5ee-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="df5ee-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="df5ee-355">**IIS Express**</span></span>

* <span data-ttu-id="df5ee-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="df5ee-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="df5ee-357">*iisexpress.exe* CLI `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="df5ee-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="df5ee-358">Os arquivos podem ser encontrados pesquisando `aspnetcore` no `applicationHost.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="df5ee-359">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="df5ee-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="df5ee-360">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="df5ee-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="df5ee-361">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="df5ee-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="df5ee-362">Versões do Windows com suporte:</span><span class="sxs-lookup"><span data-stu-id="df5ee-362">Supported Windows versions:</span></span>

* <span data-ttu-id="df5ee-363">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="df5ee-363">Windows 7 or later</span></span>
* <span data-ttu-id="df5ee-364">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="df5ee-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="df5ee-365">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="df5ee-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="df5ee-366">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="df5ee-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="df5ee-367">O módulo não funciona com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="df5ee-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="df5ee-368">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="df5ee-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="df5ee-369">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="df5ee-369">In-process hosting model</span></span>

<span data-ttu-id="df5ee-370">Para configurar um aplicativo para hospedagem em processo, adicione a propriedade `<AspNetCoreHostingModel>` ao arquivo de projeto do aplicativo com um valor de `InProcess` (a hospedagem de fora do processo é definida com `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="df5ee-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="df5ee-371">Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="df5ee-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="df5ee-372">O valor de `<AspNetCoreHostingModel>` não diferencia maiúsculas de minúsculas, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="df5ee-373">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="df5ee-374">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="df5ee-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="df5ee-375">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="df5ee-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="df5ee-376">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="df5ee-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="df5ee-377">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="df5ee-378">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df5ee-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="df5ee-379">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="df5ee-380">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="df5ee-381">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="df5ee-382">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-382">Use one app pool per app.</span></span>

* <span data-ttu-id="df5ee-383">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="df5ee-384">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="df5ee-385">A arquitetura (número de bit) do aplicativo e o runtime instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="df5ee-386">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="df5ee-386">Client disconnects are detected.</span></span> <span data-ttu-id="df5ee-387">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="df5ee-388">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="df5ee-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="df5ee-389">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="df5ee-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="df5ee-390">Chame o método `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="df5ee-391">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="df5ee-392">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="df5ee-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="df5ee-393">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="df5ee-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="df5ee-394">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="df5ee-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="df5ee-395">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="df5ee-395">Out-of-process hosting model</span></span>

<span data-ttu-id="df5ee-396">Para configurar um aplicativo para hospedagem fora do processo, use qualquer uma das abordagens a seguir no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="df5ee-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="df5ee-397">não especifique a propriedade `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="df5ee-398">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="df5ee-399">Defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` (a hospedagem no processo é definida com `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="df5ee-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="df5ee-400">O valor não diferencia maiúsculas de minúsculas, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="df5ee-401">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="df5ee-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="df5ee-402">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="df5ee-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="df5ee-403">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df5ee-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="df5ee-404">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="df5ee-405">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="df5ee-405">Hosting model changes</span></span>

<span data-ttu-id="df5ee-406">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="df5ee-407">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="df5ee-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="df5ee-408">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="df5ee-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="df5ee-409">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="df5ee-409">Process name</span></span>

<span data-ttu-id="df5ee-410">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="df5ee-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="df5ee-411">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="df5ee-412">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="df5ee-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="df5ee-413">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="df5ee-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="df5ee-414">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="df5ee-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="df5ee-415">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="df5ee-416">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="df5ee-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="df5ee-417">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df5ee-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="df5ee-418">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="df5ee-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="df5ee-419">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="df5ee-419">Configuration with web.config</span></span>

<span data-ttu-id="df5ee-420">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="df5ee-421">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="df5ee-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="df5ee-422">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="df5ee-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="df5ee-423">A <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriedade é definida como `false` para indicar que as configurações especificadas no [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemento não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="df5ee-424">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="df5ee-425">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="df5ee-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="df5ee-426">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="df5ee-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="df5ee-427">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="df5ee-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="df5ee-428">Atributo</span><span class="sxs-lookup"><span data-stu-id="df5ee-428">Attribute</span></span> | <span data-ttu-id="df5ee-429">Descrição</span><span class="sxs-lookup"><span data-stu-id="df5ee-429">Description</span></span> | <span data-ttu-id="df5ee-430">Padrão</span><span class="sxs-lookup"><span data-stu-id="df5ee-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="df5ee-431">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-431">Optional string attribute.</span></span></p><p><span data-ttu-id="df5ee-432">Argumentos para o executável especificado em `processPath` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="df5ee-433">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-434">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="df5ee-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="df5ee-435">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-436">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="df5ee-437">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="df5ee-438">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-438">Optional string attribute.</span></span></p><p><span data-ttu-id="df5ee-439">Especifica o modelo de hospedagem como em processo ( `InProcess` / `inprocess` ) ou fora do processo ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="df5ee-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="df5ee-440">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-441">Especifica o número de instâncias do processo especificado na `processPath` configuração que pode ser girada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="df5ee-442">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="df5ee-443">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="df5ee-444">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="df5ee-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="df5ee-445">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="df5ee-445">Default: `1`</span></span><br><span data-ttu-id="df5ee-446">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="df5ee-446">Min: `1`</span></span><br><span data-ttu-id="df5ee-447">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="df5ee-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="df5ee-448">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="df5ee-448">Required string attribute.</span></span></p><p><span data-ttu-id="df5ee-449">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="df5ee-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="df5ee-450">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="df5ee-450">Relative paths are supported.</span></span> <span data-ttu-id="df5ee-451">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="df5ee-452">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-453">Especifica o número de vezes que o processo especificado em `processPath` tem permissão para falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="df5ee-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="df5ee-454">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="df5ee-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="df5ee-455">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="df5ee-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="df5ee-456">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="df5ee-456">Default: `10`</span></span><br><span data-ttu-id="df5ee-457">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-457">Min: `0`</span></span><br><span data-ttu-id="df5ee-458">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="df5ee-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="df5ee-459">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="df5ee-460">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="df5ee-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="df5ee-461">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="df5ee-462">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="df5ee-463">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="df5ee-464">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="df5ee-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="df5ee-465">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="df5ee-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="df5ee-466">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-466">Default: `00:02:00`</span></span><br><span data-ttu-id="df5ee-467">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-467">Min: `00:00:00`</span></span><br><span data-ttu-id="df5ee-468">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="df5ee-469">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-470">Duração em segundos que o módulo aguarda até que o executável seja desligado normalmente quando o `app_offline.htm` arquivo é detectado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="df5ee-471">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="df5ee-471">Default: `10`</span></span><br><span data-ttu-id="df5ee-472">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-472">Min: `0`</span></span><br><span data-ttu-id="df5ee-473">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="df5ee-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="df5ee-474">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-475">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="df5ee-476">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="df5ee-477">Ao hospedar *em processo*: o processo **não** é reiniciado e **não usa a** `rapidFailsPerMinute` configuração.</span><span class="sxs-lookup"><span data-stu-id="df5ee-477">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="df5ee-478">Ao hospedar *fora do processo*: o módulo tenta reinicializar o processo quando recebe uma nova solicitação e continua tentando reiniciar o processo em solicitações de entrada subsequentes, a menos que o aplicativo não consiga iniciar o `rapidFailsPerMinute` número de vezes no último minuto de reversão.</span><span class="sxs-lookup"><span data-stu-id="df5ee-478">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="df5ee-479">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="df5ee-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="df5ee-480">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="df5ee-480">Default: `120`</span></span><br><span data-ttu-id="df5ee-481">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-481">Min: `0`</span></span><br><span data-ttu-id="df5ee-482">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="df5ee-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="df5ee-483">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-484">Se true, **stdout** e **stderr** para o processo especificado em `processPath` serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="df5ee-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="df5ee-485">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-485">Optional string attribute.</span></span></p><p><span data-ttu-id="df5ee-486">Especifica o caminho de arquivo relativo ou absoluto para o qual `stdout` e `stderr` do processo especificado em `processPath` são registrados.</span><span class="sxs-lookup"><span data-stu-id="df5ee-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="df5ee-487">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="df5ee-488">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="df5ee-489">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="df5ee-490">O uso de delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e uma extensão de arquivo ( `.log` ) são adicionados ao último segmento do `stdoutLogFile` caminho.</span><span class="sxs-lookup"><span data-stu-id="df5ee-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="df5ee-491">Se `.\logs\stdout` for fornecido como um valor, um log de stdout de exemplo será salvo como `stdout_20180205194132_1934.log` na `logs` pasta quando salvo em 2/5/2018 às 19:41:32 com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="df5ee-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="df5ee-492">Configurando variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="df5ee-492">Setting environment variables</span></span>

<span data-ttu-id="df5ee-493">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="df5ee-494">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="df5ee-495">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="df5ee-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="df5ee-496">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="df5ee-496">The following example sets two environment variables.</span></span> <span data-ttu-id="df5ee-497">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="df5ee-498">Um desenvolvedor pode definir temporariamente esse valor no `web.config` arquivo para forçar a [página de exceção do desenvolvedor](xref:fundamentals/error-handling) a ser carregada durante a depuração de uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="df5ee-499">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="df5ee-500">Uma alternativa para definir o ambiente diretamente no `web.config` é incluir a `<EnvironmentName>` propriedade no perfil de [publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="df5ee-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="df5ee-501">Essa abordagem define o ambiente em `web.config` que o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="df5ee-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="df5ee-502">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="df5ee-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="df5ee-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="df5ee-503">app_offline.htm</span></span>

<span data-ttu-id="df5ee-504">Se um arquivo com o nome `app_offline.htm` for detectado no diretório raiz de um aplicativo, o módulo ASP.NET Core tentará desligar o aplicativo normalmente e interromperá o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="df5ee-505">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="df5ee-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="df5ee-506">Enquanto o `app_offline.htm` arquivo estiver presente, o módulo ASP.NET Core responde às solicitações enviando de volta o conteúdo do `app_offline.htm` arquivo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="df5ee-507">Quando o `app_offline.htm` arquivo é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="df5ee-508">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="df5ee-509">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="df5ee-510">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="df5ee-510">Start-up error page</span></span>

<span data-ttu-id="df5ee-511">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="df5ee-512">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="df5ee-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="df5ee-513">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="df5ee-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="df5ee-514">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="df5ee-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="df5ee-515">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="df5ee-516">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="df5ee-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="df5ee-517">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="df5ee-517">Log creation and redirection</span></span>

<span data-ttu-id="df5ee-518">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="df5ee-519">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="df5ee-520">O pool de aplicativos deve ter acesso de gravação ao local onde os logs são gravados (use `IIS AppPool\{APP POOL NAME}` para fornecer permissão de gravação, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos).</span><span class="sxs-lookup"><span data-stu-id="df5ee-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="df5ee-521">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="df5ee-522">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="df5ee-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="df5ee-523">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="df5ee-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="df5ee-524">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="df5ee-525">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="df5ee-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="df5ee-526">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="df5ee-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="df5ee-527">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="df5ee-528">O nome do arquivo de log é composto acrescentando o carimbo de data/hora, a ID do processo e a extensão do arquivo ( `.log` ) ao último segmento do `stdoutLogFile` caminho (normalmente `stdout` ) delimitado por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="df5ee-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="df5ee-529">Se o `stdoutLogFile` caminho terminar com `stdout` , um log de um aplicativo com um PID de 1934 criado em 2/5/2018 às 19:42:32 terá o nome do arquivo `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="df5ee-530">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="df5ee-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="df5ee-531">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="df5ee-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="df5ee-532">O seguinte elemento de exemplo `aspNetCore` configura o log de stdout no caminho relativo `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="df5ee-533">Confirme se a identidade do usuário do pool de aplicativos tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="df5ee-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="df5ee-534">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o `stdoutLogFile` valor como `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="df5ee-535">A `%home` variável de ambiente é predefinida para aplicativos hospedados pelo serviço de Azure app.</span><span class="sxs-lookup"><span data-stu-id="df5ee-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="df5ee-536">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="df5ee-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="df5ee-537">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="df5ee-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="df5ee-538">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="df5ee-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="df5ee-539">Adicione o `<handlerSettings>` elemento ao `<aspNetCore>` elemento em `web.config` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="df5ee-540">A definição de `debugLevel` como `TRACE` expõe uma fidelidade maior de informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="df5ee-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="df5ee-541">As pastas no caminho fornecido ao `<handlerSetting>` valor ( `logs` no exemplo anterior) não são criadas automaticamente pelo módulo e devem existir na implantação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="df5ee-542">O pool de aplicativos deve ter acesso de gravação ao local onde os logs são gravados (use `IIS AppPool\{APP POOL NAME}` para fornecer permissão de gravação, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos).</span><span class="sxs-lookup"><span data-stu-id="df5ee-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="df5ee-543">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="df5ee-544">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="df5ee-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="df5ee-545">ERRO</span><span class="sxs-lookup"><span data-stu-id="df5ee-545">ERROR</span></span>
* <span data-ttu-id="df5ee-546">WARNING</span><span class="sxs-lookup"><span data-stu-id="df5ee-546">WARNING</span></span>
* <span data-ttu-id="df5ee-547">INFO</span><span class="sxs-lookup"><span data-stu-id="df5ee-547">INFO</span></span>
* <span data-ttu-id="df5ee-548">RASTREAMENTO</span><span class="sxs-lookup"><span data-stu-id="df5ee-548">TRACE</span></span>

<span data-ttu-id="df5ee-549">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="df5ee-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="df5ee-550">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="df5ee-550">CONSOLE</span></span>
* <span data-ttu-id="df5ee-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="df5ee-551">EVENTLOG</span></span>
* <span data-ttu-id="df5ee-552">FILE</span><span class="sxs-lookup"><span data-stu-id="df5ee-552">FILE</span></span>

<span data-ttu-id="df5ee-553">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="df5ee-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="df5ee-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="df5ee-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="df5ee-555">(Padrão: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="df5ee-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="df5ee-556">`ASPNETCORE_MODULE_DEBUG`: Configuração de nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="df5ee-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="df5ee-557">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="df5ee-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="df5ee-558">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-558">The size of the log isn't limited.</span></span> <span data-ttu-id="df5ee-559">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="df5ee-560">Consulte [configuração com web.config](#configuration-with-webconfig) para obter um exemplo do `aspNetCore` elemento no `web.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="df5ee-561">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="df5ee-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="df5ee-562">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="df5ee-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="df5ee-563">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="df5ee-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="df5ee-564">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="df5ee-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="df5ee-565">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="df5ee-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="df5ee-566">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="df5ee-567">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="df5ee-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="df5ee-568">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="df5ee-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="df5ee-569">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="df5ee-570">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="df5ee-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="df5ee-571">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="df5ee-572">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="df5ee-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="df5ee-573">O instalador do módulo ASP.NET Core é executado com os privilégios da `TrustedInstaller` conta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="df5ee-574">Como a conta do sistema local não tem permissão Modificar para o caminho de compartilhamento usado pela configuração compartilhada do IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações do módulo no `applicationHost.config` arquivo no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="df5ee-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="df5ee-575">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="df5ee-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="df5ee-576">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="df5ee-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="df5ee-577">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="df5ee-578">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="df5ee-578">Run the installer.</span></span>
1. <span data-ttu-id="df5ee-579">Exporte o `applicationHost.config` arquivo atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="df5ee-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="df5ee-580">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="df5ee-581">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="df5ee-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="df5ee-582">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="df5ee-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="df5ee-583">No sistema de hospedagem, navegue até `%windir%\System32\inetsrv` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="df5ee-584">Localize o `aspnetcore.dll` arquivo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="df5ee-585">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="df5ee-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="df5ee-586">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="df5ee-587">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em `C:\\Users\\%UserName%\\AppData\\Local\\Temp` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="df5ee-588">O arquivo é nomeado `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log` , onde o espaço reservado `{TIMESTAMP}` é o carimbo de data/hora.</span><span class="sxs-lookup"><span data-stu-id="df5ee-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="df5ee-589">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="df5ee-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="df5ee-590">Módulo</span><span class="sxs-lookup"><span data-stu-id="df5ee-590">Module</span></span>

<span data-ttu-id="df5ee-591">**IIS (x86/AMD64)**:</span><span class="sxs-lookup"><span data-stu-id="df5ee-591">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="df5ee-592">**IIS Express (x86/AMD64)**:</span><span class="sxs-lookup"><span data-stu-id="df5ee-592">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="df5ee-593">Esquema</span><span class="sxs-lookup"><span data-stu-id="df5ee-593">Schema</span></span>

<span data-ttu-id="df5ee-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="df5ee-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="df5ee-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="df5ee-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="df5ee-596">Configuração</span><span class="sxs-lookup"><span data-stu-id="df5ee-596">Configuration</span></span>

<span data-ttu-id="df5ee-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="df5ee-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="df5ee-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="df5ee-598">**IIS Express**</span></span>

* <span data-ttu-id="df5ee-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="df5ee-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="df5ee-600">*iisexpress.exe* CLI `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="df5ee-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="df5ee-601">Os arquivos podem ser encontrados pesquisando `aspnetcore` no `applicationHost.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="df5ee-602">O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="df5ee-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="df5ee-603">Versões do Windows com suporte:</span><span class="sxs-lookup"><span data-stu-id="df5ee-603">Supported Windows versions:</span></span>

* <span data-ttu-id="df5ee-604">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="df5ee-604">Windows 7 or later</span></span>
* <span data-ttu-id="df5ee-605">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="df5ee-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="df5ee-606">O módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="df5ee-606">The module only works with Kestrel.</span></span> <span data-ttu-id="df5ee-607">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="df5ee-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="df5ee-608">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="df5ee-609">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha.</span><span class="sxs-lookup"><span data-stu-id="df5ee-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="df5ee-610">Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="df5ee-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="df5ee-611">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="df5ee-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo do ASP.NET Core](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="df5ee-613">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="df5ee-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="df5ee-614">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="df5ee-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="df5ee-615">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="df5ee-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="df5ee-616">O módulo Especifica a porta por meio de uma variável de ambiente na inicialização, e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor a ser escutado `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="df5ee-617">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="df5ee-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="df5ee-618">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="df5ee-619">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df5ee-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="df5ee-620">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="df5ee-621">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="df5ee-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="df5ee-622">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="df5ee-623">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="df5ee-624">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="df5ee-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="df5ee-625">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="df5ee-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="df5ee-626">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="df5ee-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="df5ee-627">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="df5ee-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="df5ee-628">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="df5ee-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="df5ee-629">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df5ee-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="df5ee-630">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="df5ee-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="df5ee-631">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="df5ee-631">Configuration with web.config</span></span>

<span data-ttu-id="df5ee-632">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="df5ee-633">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="df5ee-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="df5ee-634">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="df5ee-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="df5ee-635">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="df5ee-636">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="df5ee-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="df5ee-637">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="df5ee-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="df5ee-638">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="df5ee-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="df5ee-639">Atributo</span><span class="sxs-lookup"><span data-stu-id="df5ee-639">Attribute</span></span> | <span data-ttu-id="df5ee-640">Descrição</span><span class="sxs-lookup"><span data-stu-id="df5ee-640">Description</span></span> | <span data-ttu-id="df5ee-641">Padrão</span><span class="sxs-lookup"><span data-stu-id="df5ee-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="df5ee-642">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-642">Optional string attribute.</span></span></p><p><span data-ttu-id="df5ee-643">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="df5ee-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="df5ee-644">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-645">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="df5ee-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="df5ee-646">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-647">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="df5ee-648">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="df5ee-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="df5ee-649">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-650">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="df5ee-651">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="df5ee-652">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="df5ee-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="df5ee-653">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="df5ee-653">Default: `1`</span></span><br><span data-ttu-id="df5ee-654">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="df5ee-654">Min: `1`</span></span><br><span data-ttu-id="df5ee-655">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="df5ee-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="df5ee-656">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="df5ee-656">Required string attribute.</span></span></p><p><span data-ttu-id="df5ee-657">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="df5ee-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="df5ee-658">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="df5ee-658">Relative paths are supported.</span></span> <span data-ttu-id="df5ee-659">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="df5ee-660">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-661">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="df5ee-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="df5ee-662">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="df5ee-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="df5ee-663">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="df5ee-663">Default: `10`</span></span><br><span data-ttu-id="df5ee-664">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-664">Min: `0`</span></span><br><span data-ttu-id="df5ee-665">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="df5ee-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="df5ee-666">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="df5ee-667">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="df5ee-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="df5ee-668">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="df5ee-669">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-669">Default: `00:02:00`</span></span><br><span data-ttu-id="df5ee-670">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-670">Min: `00:00:00`</span></span><br><span data-ttu-id="df5ee-671">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="df5ee-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="df5ee-672">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-673">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="df5ee-674">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="df5ee-674">Default: `10`</span></span><br><span data-ttu-id="df5ee-675">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-675">Min: `0`</span></span><br><span data-ttu-id="df5ee-676">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="df5ee-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="df5ee-677">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="df5ee-678">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="df5ee-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="df5ee-679">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="df5ee-680">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="df5ee-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="df5ee-681">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="df5ee-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="df5ee-682">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="df5ee-682">Default: `120`</span></span><br><span data-ttu-id="df5ee-683">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="df5ee-683">Min: `0`</span></span><br><span data-ttu-id="df5ee-684">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="df5ee-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="df5ee-685">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="df5ee-686">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="df5ee-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="df5ee-687">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="df5ee-687">Optional string attribute.</span></span></p><p><span data-ttu-id="df5ee-688">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="df5ee-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="df5ee-689">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="df5ee-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="df5ee-690">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="df5ee-691">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="df5ee-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="df5ee-692">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="df5ee-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="df5ee-693">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="df5ee-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="df5ee-694">Configurando variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="df5ee-694">Setting environment variables</span></span>

<span data-ttu-id="df5ee-695">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="df5ee-696">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="df5ee-697">As variáveis de ambiente definidas nesta seção são conflitantes com as variáveis de ambiente do sistema definidas com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="df5ee-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="df5ee-698">Quando a variável de ambiente é definida no arquivo *web.config* e no nível do sistema do Windows, o valor do arquivo *web.config* fica anexado ao valor da variável de ambiente do sistema (por exemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), o que impede a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="df5ee-699">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="df5ee-699">The following example sets two environment variables.</span></span> <span data-ttu-id="df5ee-700">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="df5ee-701">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="df5ee-702">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="df5ee-703">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="df5ee-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="df5ee-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="df5ee-704">app_offline.htm</span></span>

<span data-ttu-id="df5ee-705">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="df5ee-706">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="df5ee-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="df5ee-707">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="df5ee-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="df5ee-708">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="df5ee-709">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="df5ee-709">Start-up error page</span></span>

<span data-ttu-id="df5ee-710">Se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="df5ee-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="df5ee-711">Para omitir esta página e reverter para a página de código de status 502 padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="df5ee-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="df5ee-712">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="df5ee-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="df5ee-713">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="df5ee-713">Log creation and redirection</span></span>

<span data-ttu-id="df5ee-714">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="df5ee-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="df5ee-715">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="df5ee-716">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="df5ee-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="df5ee-717">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="df5ee-718">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="df5ee-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="df5ee-719">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="df5ee-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="df5ee-720">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="df5ee-721">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="df5ee-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="df5ee-722">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="df5ee-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="df5ee-723">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="df5ee-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="df5ee-724">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo (*.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="df5ee-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="df5ee-725">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="df5ee-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="df5ee-726">O seguinte elemento de exemplo `aspNetCore` configura o log de stdout no caminho relativo `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="df5ee-727">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="df5ee-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="df5ee-728">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o `stdoutLogFile` valor como `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="df5ee-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="df5ee-729">A `%home` variável de ambiente é predefinida para aplicativos hospedados pelo serviço de Azure app.</span><span class="sxs-lookup"><span data-stu-id="df5ee-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="df5ee-730">Para criar regras de filtro de log, consulte as seções de [configuração](xref:fundamentals/logging/index#log-filtering) e [filtragem de log](xref:fundamentals/logging/index#log-filtering) da documentação de log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df5ee-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="df5ee-731">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="df5ee-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="df5ee-732">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="df5ee-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="df5ee-733">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="df5ee-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="df5ee-734">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="df5ee-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="df5ee-735">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="df5ee-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="df5ee-736">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="df5ee-737">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="df5ee-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="df5ee-738">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="df5ee-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="df5ee-739">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="df5ee-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="df5ee-740">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="df5ee-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="df5ee-741">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="df5ee-742">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="df5ee-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="df5ee-743">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="df5ee-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="df5ee-744">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="df5ee-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="df5ee-745">Ao usar uma configuração compartilhada de IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="df5ee-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="df5ee-746">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="df5ee-747">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="df5ee-747">Run the installer.</span></span>
1. <span data-ttu-id="df5ee-748">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="df5ee-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="df5ee-749">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="df5ee-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="df5ee-750">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="df5ee-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="df5ee-751">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="df5ee-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="df5ee-752">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="df5ee-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="df5ee-753">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="df5ee-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="df5ee-754">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="df5ee-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="df5ee-755">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="df5ee-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="df5ee-756">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C: \\ usuários \\ % username% \\ AppData \\ local \\ Temp*. O arquivo é nomeado *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="df5ee-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="df5ee-757">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="df5ee-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="df5ee-758">Módulo</span><span class="sxs-lookup"><span data-stu-id="df5ee-758">Module</span></span>

<span data-ttu-id="df5ee-759">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="df5ee-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="df5ee-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="df5ee-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="df5ee-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="df5ee-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="df5ee-762">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="df5ee-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="df5ee-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="df5ee-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="df5ee-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="df5ee-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="df5ee-765">Esquema</span><span class="sxs-lookup"><span data-stu-id="df5ee-765">Schema</span></span>

<span data-ttu-id="df5ee-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="df5ee-766">**IIS**</span></span>

* <span data-ttu-id="df5ee-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="df5ee-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="df5ee-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="df5ee-768">**IIS Express**</span></span>

* <span data-ttu-id="df5ee-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="df5ee-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="df5ee-770">Configuração</span><span class="sxs-lookup"><span data-stu-id="df5ee-770">Configuration</span></span>

<span data-ttu-id="df5ee-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="df5ee-771">**IIS**</span></span>

* <span data-ttu-id="df5ee-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="df5ee-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="df5ee-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="df5ee-773">**IIS Express**</span></span>

* <span data-ttu-id="df5ee-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="df5ee-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="df5ee-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="df5ee-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="df5ee-776">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="df5ee-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="df5ee-777">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="df5ee-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="df5ee-778">[Fonte de referência do módulo ASP.NET Core [Branch padrão (Mestre)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use a lista suspensa **Branch** para selecionar uma versão específica (por exemplo, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="df5ee-778">[ASP.NET Core Module reference source [default branch (master)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
