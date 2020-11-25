---
title: Host genérico .NET no ASP.NET Core
author: rick-anderson
description: Use o host genérico do .NET Core em aplicativos ASP.NET Core.  O host genérico é responsável pela inicialização do aplicativo e pelo gerenciamento de tempo de vida.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 263c7713166005dfdec8ede6bfa9b03b730dede7
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035808"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="4d64a-104">Host genérico .NET no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d64a-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4d64a-105">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="4d64a-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="4d64a-106">Este tópico fornece informações sobre como usar o host genérico .NET no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d64a-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="4d64a-107">Para obter informações sobre como usar o host genérico .NET em aplicativos de console, consulte [host genérico .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="4d64a-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="4d64a-108">Definição de host</span><span class="sxs-lookup"><span data-stu-id="4d64a-108">Host definition</span></span>

<span data-ttu-id="4d64a-109">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="4d64a-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4d64a-110">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="4d64a-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="4d64a-111">Registro em log</span><span class="sxs-lookup"><span data-stu-id="4d64a-111">Logging</span></span>
* <span data-ttu-id="4d64a-112">Configuração</span><span class="sxs-lookup"><span data-stu-id="4d64a-112">Configuration</span></span>
* <span data-ttu-id="4d64a-113">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="4d64a-113">`IHostedService` implementations</span></span>

<span data-ttu-id="4d64a-114">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="4d64a-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="4d64a-115">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="4d64a-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4d64a-116">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4d64a-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4d64a-117">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="4d64a-117">Set up a host</span></span>

<span data-ttu-id="4d64a-118">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4d64a-119">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-119">The `Main` method:</span></span>

* <span data-ttu-id="4d64a-120">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4d64a-121">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4d64a-122">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host:</span><span class="sxs-lookup"><span data-stu-id="4d64a-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="4d64a-123">O código a seguir cria uma carga de trabalho não HTTP com uma `IHostedService` implementação adicionada ao contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="4d64a-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="4d64a-124">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4d64a-125">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4d64a-126">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4d64a-127">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="4d64a-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4d64a-128">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="4d64a-128">Default builder settings</span></span>

<span data-ttu-id="4d64a-129">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="4d64a-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="4d64a-130">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="4d64a-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="4d64a-131">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="4d64a-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="4d64a-132">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4d64a-133">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="4d64a-133">Command-line arguments.</span></span>
* <span data-ttu-id="4d64a-134">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="4d64a-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="4d64a-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4d64a-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="4d64a-136">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4d64a-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4d64a-137">[Segredos do usuário](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4d64a-138">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="4d64a-138">Environment variables.</span></span>
  * <span data-ttu-id="4d64a-139">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="4d64a-139">Command-line arguments.</span></span>
* <span data-ttu-id="4d64a-140">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="4d64a-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4d64a-141">Console</span><span class="sxs-lookup"><span data-stu-id="4d64a-141">Console</span></span>
  * <span data-ttu-id="4d64a-142">Depurar</span><span class="sxs-lookup"><span data-stu-id="4d64a-142">Debug</span></span>
  * <span data-ttu-id="4d64a-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="4d64a-143">EventSource</span></span>
  * <span data-ttu-id="4d64a-144">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="4d64a-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4d64a-145">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4d64a-146">O método <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>:</span><span class="sxs-lookup"><span data-stu-id="4d64a-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="4d64a-147">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4d64a-148">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4d64a-149">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4d64a-150">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="4d64a-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4d64a-151">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4d64a-152">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="4d64a-152">Enables IIS integration.</span></span> <span data-ttu-id="4d64a-153">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4d64a-154">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="4d64a-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4d64a-155">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="4d64a-155">Framework-provided services</span></span>

<span data-ttu-id="4d64a-156">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="4d64a-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4d64a-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4d64a-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4d64a-159">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4d64a-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4d64a-160">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="4d64a-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4d64a-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="4d64a-162">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4d64a-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4d64a-163">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4d64a-164">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4d64a-165">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="4d64a-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4d64a-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-166">IHostLifetime</span></span>

<span data-ttu-id="4d64a-167">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="4d64a-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4d64a-168">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="4d64a-168">The last implementation registered is used.</span></span>

<span data-ttu-id="4d64a-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="4d64a-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4d64a-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4d64a-171">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="4d64a-172">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="4d64a-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4d64a-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4d64a-173">IHostEnvironment</span></span>

<span data-ttu-id="4d64a-174">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="4d64a-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4d64a-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4d64a-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4d64a-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4d64a-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4d64a-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="4d64a-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4d64a-178">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="4d64a-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4d64a-179">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="4d64a-179">Host configuration</span></span>

<span data-ttu-id="4d64a-180">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4d64a-181">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="4d64a-182">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4d64a-183">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4d64a-184">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4d64a-185">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="4d64a-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4d64a-186">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4d64a-187">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4d64a-188">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4d64a-189">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4d64a-190">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="4d64a-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4d64a-191">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="4d64a-191">App configuration</span></span>

<span data-ttu-id="4d64a-192">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4d64a-193">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4d64a-194">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="4d64a-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4d64a-195">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="4d64a-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4d64a-196">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4d64a-197">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4d64a-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4d64a-198">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="4d64a-198">Settings for all app types</span></span>

<span data-ttu-id="4d64a-199">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="4d64a-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4d64a-200">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="4d64a-201">Para obter mais informações, consulte a seção [configurações padrão do Construtor](#default-builder-settings) .</span><span class="sxs-lookup"><span data-stu-id="4d64a-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4d64a-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4d64a-202">ApplicationName</span></span>

<span data-ttu-id="4d64a-203">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4d64a-204">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="4d64a-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4d64a-205">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-205">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-206">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4d64a-207">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4d64a-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4d64a-208">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="4d64a-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4d64a-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4d64a-209">ContentRoot</span></span>

<span data-ttu-id="4d64a-210">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4d64a-211">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4d64a-212">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4d64a-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4d64a-213">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-213">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-214">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="4d64a-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4d64a-215">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4d64a-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4d64a-216">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4d64a-217">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="4d64a-217">For more information, see:</span></span>

* [<span data-ttu-id="4d64a-218">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="4d64a-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4d64a-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4d64a-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4d64a-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4d64a-220">EnvironmentName</span></span>

<span data-ttu-id="4d64a-221">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4d64a-222">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4d64a-223">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4d64a-224">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="4d64a-224">**Key**: `environment`</span></span>  
<span data-ttu-id="4d64a-225">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-225">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-226">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="4d64a-226">**Default**: `Production`</span></span>  
<span data-ttu-id="4d64a-227">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4d64a-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4d64a-228">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4d64a-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="4d64a-229">ShutdownTimeout</span></span>

<span data-ttu-id="4d64a-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4d64a-231">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-231">The default value is five seconds.</span></span>  <span data-ttu-id="4d64a-232">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="4d64a-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="4d64a-233">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="4d64a-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4d64a-234">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="4d64a-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4d64a-235">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4d64a-236">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4d64a-237">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="4d64a-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4d64a-238">**Chave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4d64a-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4d64a-239">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="4d64a-239">**Type**: `int`</span></span>  
<span data-ttu-id="4d64a-240">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="4d64a-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4d64a-241">**Variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4d64a-242">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4d64a-243">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="4d64a-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="4d64a-244">Desabilitar recarga de configuração de aplicativo na alteração</span><span class="sxs-lookup"><span data-stu-id="4d64a-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="4d64a-245">Por [padrão](xref:fundamentals/configuration/index#default), *appsettings.json* e *appSettings. { Ambiente}. JSON* são recarregados quando o arquivo é alterado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="4d64a-246">Para desabilitar esse comportamento de recarregamento no ASP.NET Core 5,0 ou posterior, defina a `hostBuilder:reloadConfigOnChange` chave como `false` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="4d64a-247">**Chave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4d64a-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4d64a-248">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-249">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="4d64a-249">**Default**: `true`</span></span>  
<span data-ttu-id="4d64a-250">**Argumento de linha de comando**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4d64a-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4d64a-251">**Variável de ambiente**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4d64a-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="4d64a-252">O separador de dois-pontos ( `:` ) não funciona com chaves hierárquicas de variáveis de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="4d64a-253">Para obter mais informações, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="4d64a-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="4d64a-254">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="4d64a-254">Settings for web apps</span></span>

<span data-ttu-id="4d64a-255">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="4d64a-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4d64a-256">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4d64a-257">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="4d64a-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4d64a-258">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4d64a-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4d64a-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4d64a-259">CaptureStartupErrors</span></span>

<span data-ttu-id="4d64a-260">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4d64a-261">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4d64a-262">**Chave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4d64a-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4d64a-263">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-264">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4d64a-265">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4d64a-266">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4d64a-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="4d64a-267">DetailedErrors</span></span>

<span data-ttu-id="4d64a-268">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="4d64a-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4d64a-269">**Chave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4d64a-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4d64a-270">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-271">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="4d64a-271">**Default**: `false`</span></span>  
<span data-ttu-id="4d64a-272">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4d64a-273">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4d64a-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4d64a-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="4d64a-275">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="4d64a-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4d64a-276">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4d64a-277">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="4d64a-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4d64a-278">**Chave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4d64a-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4d64a-279">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-279">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-280">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="4d64a-280">**Default**: Empty string</span></span>  
<span data-ttu-id="4d64a-281">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4d64a-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4d64a-282">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4d64a-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4d64a-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4d64a-284">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="4d64a-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4d64a-285">**Chave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4d64a-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4d64a-286">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-286">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-287">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="4d64a-287">**Default**: Empty string</span></span>  
<span data-ttu-id="4d64a-288">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4d64a-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4d64a-289">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4d64a-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4d64a-290">HTTPS_Port</span></span>

<span data-ttu-id="4d64a-291">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4d64a-291">The HTTPS redirect port.</span></span> <span data-ttu-id="4d64a-292">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="4d64a-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4d64a-293">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="4d64a-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="4d64a-294">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-294">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-295">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="4d64a-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4d64a-296">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4d64a-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4d64a-297">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4d64a-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4d64a-298">PreferHostingUrls</span></span>

<span data-ttu-id="4d64a-299">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="4d64a-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4d64a-300">**Chave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4d64a-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4d64a-301">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-302">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="4d64a-302">**Default**: `true`</span></span>  
<span data-ttu-id="4d64a-303">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4d64a-304">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4d64a-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4d64a-305">PreventHostingStartup</span></span>

<span data-ttu-id="4d64a-306">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4d64a-307">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4d64a-308">**Chave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4d64a-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4d64a-309">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-310">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="4d64a-310">**Default**: `false`</span></span>  
<span data-ttu-id="4d64a-311">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4d64a-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4d64a-312">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4d64a-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="4d64a-313">StartupAssembly</span></span>

<span data-ttu-id="4d64a-314">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4d64a-315">**Chave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4d64a-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4d64a-316">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-316">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-317">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="4d64a-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4d64a-318">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4d64a-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4d64a-319">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4d64a-320">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="4d64a-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4d64a-321">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="4d64a-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4d64a-322">URLs</span><span class="sxs-lookup"><span data-stu-id="4d64a-322">URLs</span></span>

<span data-ttu-id="4d64a-323">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="4d64a-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4d64a-324">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4d64a-325">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="4d64a-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4d64a-326">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="4d64a-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4d64a-327">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="4d64a-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="4d64a-328">**Chave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="4d64a-328">**Key**: `urls`</span></span>  
<span data-ttu-id="4d64a-329">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-329">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-330">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4d64a-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4d64a-331">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4d64a-332">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4d64a-333">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="4d64a-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4d64a-334">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-334">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4d64a-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4d64a-335">WebRoot</span></span>

<span data-ttu-id="4d64a-336">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4d64a-337">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4d64a-338">**Chave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="4d64a-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="4d64a-339">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-339">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-340">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4d64a-341">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="4d64a-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4d64a-342">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4d64a-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4d64a-343">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4d64a-344">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="4d64a-344">For more information, see:</span></span>

* [<span data-ttu-id="4d64a-345">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="4d64a-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4d64a-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4d64a-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4d64a-347">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="4d64a-347">Manage the host lifetime</span></span>

<span data-ttu-id="4d64a-348">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4d64a-349">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="4d64a-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4d64a-350">Executar</span><span class="sxs-lookup"><span data-stu-id="4d64a-350">Run</span></span>

<span data-ttu-id="4d64a-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4d64a-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-352">RunAsync</span></span>

<span data-ttu-id="4d64a-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4d64a-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-354">RunConsoleAsync</span></span>

<span data-ttu-id="4d64a-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="4d64a-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4d64a-356">Iniciar</span><span class="sxs-lookup"><span data-stu-id="4d64a-356">Start</span></span>

<span data-ttu-id="4d64a-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="4d64a-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4d64a-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-358">StartAsync</span></span>

<span data-ttu-id="4d64a-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4d64a-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="4d64a-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4d64a-361">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4d64a-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-362">StopAsync</span></span>

<span data-ttu-id="4d64a-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="4d64a-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4d64a-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4d64a-364">WaitForShutdown</span></span>

<span data-ttu-id="4d64a-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="4d64a-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4d64a-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="4d64a-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4d64a-368">Controle externo</span><span class="sxs-lookup"><span data-stu-id="4d64a-368">External control</span></span>

<span data-ttu-id="4d64a-369">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="4d64a-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4d64a-370">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="4d64a-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="4d64a-371">Este tópico fornece informações sobre como usar o host genérico .NET no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d64a-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="4d64a-372">Para obter informações sobre como usar o host genérico .NET em aplicativos de console, consulte [host genérico .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="4d64a-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="4d64a-373">Definição de host</span><span class="sxs-lookup"><span data-stu-id="4d64a-373">Host definition</span></span>

<span data-ttu-id="4d64a-374">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="4d64a-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4d64a-375">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="4d64a-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="4d64a-376">Registro em log</span><span class="sxs-lookup"><span data-stu-id="4d64a-376">Logging</span></span>
* <span data-ttu-id="4d64a-377">Configuração</span><span class="sxs-lookup"><span data-stu-id="4d64a-377">Configuration</span></span>
* <span data-ttu-id="4d64a-378">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="4d64a-378">`IHostedService` implementations</span></span>

<span data-ttu-id="4d64a-379">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="4d64a-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="4d64a-380">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="4d64a-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4d64a-381">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4d64a-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4d64a-382">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="4d64a-382">Set up a host</span></span>

<span data-ttu-id="4d64a-383">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4d64a-384">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-384">The `Main` method:</span></span>

* <span data-ttu-id="4d64a-385">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4d64a-386">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4d64a-387">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host genérico:</span><span class="sxs-lookup"><span data-stu-id="4d64a-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="4d64a-388">O código a seguir cria um host genérico usando carga de trabalho não HTTP.</span><span class="sxs-lookup"><span data-stu-id="4d64a-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="4d64a-389">A `IHostedService` implementação é adicionada ao contêiner di:</span><span class="sxs-lookup"><span data-stu-id="4d64a-389">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="4d64a-390">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4d64a-391">O código anterior é gerado pelos modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d64a-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="4d64a-392">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4d64a-393">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4d64a-394">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="4d64a-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4d64a-395">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="4d64a-395">Default builder settings</span></span>

<span data-ttu-id="4d64a-396">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:</span><span class="sxs-lookup"><span data-stu-id="4d64a-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="4d64a-397">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="4d64a-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="4d64a-398">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="4d64a-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="4d64a-399">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4d64a-400">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="4d64a-400">Command-line arguments.</span></span>
* <span data-ttu-id="4d64a-401">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="4d64a-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="4d64a-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4d64a-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="4d64a-403">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4d64a-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4d64a-404">[Segredos do usuário](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4d64a-405">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="4d64a-405">Environment variables.</span></span>
  * <span data-ttu-id="4d64a-406">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="4d64a-406">Command-line arguments.</span></span>
* <span data-ttu-id="4d64a-407">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="4d64a-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4d64a-408">Console</span><span class="sxs-lookup"><span data-stu-id="4d64a-408">Console</span></span>
  * <span data-ttu-id="4d64a-409">Depurar</span><span class="sxs-lookup"><span data-stu-id="4d64a-409">Debug</span></span>
  * <span data-ttu-id="4d64a-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="4d64a-410">EventSource</span></span>
  * <span data-ttu-id="4d64a-411">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="4d64a-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4d64a-412">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4d64a-413">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="4d64a-414">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4d64a-415">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4d64a-416">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4d64a-417">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="4d64a-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4d64a-418">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4d64a-419">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="4d64a-419">Enables IIS integration.</span></span> <span data-ttu-id="4d64a-420">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4d64a-421">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="4d64a-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4d64a-422">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="4d64a-422">Framework-provided services</span></span>

<span data-ttu-id="4d64a-423">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="4d64a-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4d64a-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4d64a-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4d64a-426">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4d64a-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4d64a-427">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="4d64a-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4d64a-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="4d64a-429">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4d64a-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4d64a-430">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4d64a-431">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4d64a-432">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="4d64a-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4d64a-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-433">IHostLifetime</span></span>

<span data-ttu-id="4d64a-434">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="4d64a-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4d64a-435">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="4d64a-435">The last implementation registered is used.</span></span>

<span data-ttu-id="4d64a-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="4d64a-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4d64a-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4d64a-438">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="4d64a-439">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="4d64a-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4d64a-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4d64a-440">IHostEnvironment</span></span>

<span data-ttu-id="4d64a-441">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="4d64a-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4d64a-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4d64a-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4d64a-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4d64a-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4d64a-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="4d64a-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4d64a-445">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="4d64a-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4d64a-446">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="4d64a-446">Host configuration</span></span>

<span data-ttu-id="4d64a-447">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4d64a-448">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="4d64a-449">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4d64a-450">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="4d64a-451">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4d64a-452">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="4d64a-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4d64a-453">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4d64a-454">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4d64a-455">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4d64a-456">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4d64a-457">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="4d64a-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4d64a-458">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="4d64a-458">App configuration</span></span>

<span data-ttu-id="4d64a-459">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4d64a-460">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4d64a-461">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="4d64a-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4d64a-462">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="4d64a-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4d64a-463">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4d64a-464">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4d64a-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4d64a-465">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="4d64a-465">Settings for all app types</span></span>

<span data-ttu-id="4d64a-466">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="4d64a-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4d64a-467">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4d64a-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4d64a-468">ApplicationName</span></span>

<span data-ttu-id="4d64a-469">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4d64a-470">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="4d64a-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4d64a-471">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-471">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-472">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4d64a-473">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4d64a-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4d64a-474">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="4d64a-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4d64a-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4d64a-475">ContentRoot</span></span>

<span data-ttu-id="4d64a-476">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4d64a-477">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4d64a-478">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4d64a-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4d64a-479">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-479">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-480">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="4d64a-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4d64a-481">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4d64a-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4d64a-482">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4d64a-483">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="4d64a-483">For more information, see:</span></span>

* [<span data-ttu-id="4d64a-484">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="4d64a-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4d64a-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4d64a-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4d64a-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4d64a-486">EnvironmentName</span></span>

<span data-ttu-id="4d64a-487">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4d64a-488">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4d64a-489">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4d64a-490">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="4d64a-490">**Key**: `environment`</span></span>  
<span data-ttu-id="4d64a-491">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-491">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-492">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="4d64a-492">**Default**: `Production`</span></span>  
<span data-ttu-id="4d64a-493">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4d64a-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4d64a-494">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4d64a-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="4d64a-495">ShutdownTimeout</span></span>

<span data-ttu-id="4d64a-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4d64a-497">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-497">The default value is five seconds.</span></span>  <span data-ttu-id="4d64a-498">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="4d64a-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="4d64a-499">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="4d64a-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4d64a-500">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="4d64a-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4d64a-501">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4d64a-502">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4d64a-503">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="4d64a-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4d64a-504">**Chave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4d64a-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4d64a-505">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="4d64a-505">**Type**: `int`</span></span>  
<span data-ttu-id="4d64a-506">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="4d64a-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4d64a-507">**Variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4d64a-508">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4d64a-509">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="4d64a-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="4d64a-510">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="4d64a-510">Settings for web apps</span></span>

<span data-ttu-id="4d64a-511">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="4d64a-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4d64a-512">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4d64a-513">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="4d64a-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4d64a-514">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4d64a-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4d64a-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4d64a-515">CaptureStartupErrors</span></span>

<span data-ttu-id="4d64a-516">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4d64a-517">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4d64a-518">**Chave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4d64a-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4d64a-519">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-520">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4d64a-521">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4d64a-522">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4d64a-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="4d64a-523">DetailedErrors</span></span>

<span data-ttu-id="4d64a-524">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="4d64a-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4d64a-525">**Chave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4d64a-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4d64a-526">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-527">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="4d64a-527">**Default**: `false`</span></span>  
<span data-ttu-id="4d64a-528">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4d64a-529">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4d64a-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4d64a-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="4d64a-531">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="4d64a-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4d64a-532">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4d64a-533">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="4d64a-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4d64a-534">**Chave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4d64a-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4d64a-535">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-535">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-536">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="4d64a-536">**Default**: Empty string</span></span>  
<span data-ttu-id="4d64a-537">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4d64a-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4d64a-538">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4d64a-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4d64a-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4d64a-540">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="4d64a-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4d64a-541">**Chave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4d64a-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4d64a-542">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-542">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-543">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="4d64a-543">**Default**: Empty string</span></span>  
<span data-ttu-id="4d64a-544">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4d64a-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4d64a-545">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4d64a-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4d64a-546">HTTPS_Port</span></span>

<span data-ttu-id="4d64a-547">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4d64a-547">The HTTPS redirect port.</span></span> <span data-ttu-id="4d64a-548">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="4d64a-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4d64a-549">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="4d64a-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="4d64a-550">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-550">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-551">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="4d64a-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4d64a-552">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4d64a-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4d64a-553">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4d64a-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4d64a-554">PreferHostingUrls</span></span>

<span data-ttu-id="4d64a-555">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="4d64a-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4d64a-556">**Chave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4d64a-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4d64a-557">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-558">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="4d64a-558">**Default**: `true`</span></span>  
<span data-ttu-id="4d64a-559">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4d64a-560">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4d64a-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4d64a-561">PreventHostingStartup</span></span>

<span data-ttu-id="4d64a-562">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4d64a-563">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4d64a-564">**Chave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4d64a-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4d64a-565">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="4d64a-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4d64a-566">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="4d64a-566">**Default**: `false`</span></span>  
<span data-ttu-id="4d64a-567">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4d64a-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4d64a-568">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4d64a-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="4d64a-569">StartupAssembly</span></span>

<span data-ttu-id="4d64a-570">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4d64a-571">**Chave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4d64a-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4d64a-572">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-572">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-573">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="4d64a-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4d64a-574">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4d64a-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4d64a-575">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4d64a-576">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="4d64a-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4d64a-577">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="4d64a-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4d64a-578">URLs</span><span class="sxs-lookup"><span data-stu-id="4d64a-578">URLs</span></span>

<span data-ttu-id="4d64a-579">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="4d64a-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4d64a-580">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4d64a-581">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="4d64a-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4d64a-582">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="4d64a-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4d64a-583">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="4d64a-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="4d64a-584">**Chave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="4d64a-584">**Key**: `urls`</span></span>  
<span data-ttu-id="4d64a-585">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-585">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-586">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4d64a-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4d64a-587">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4d64a-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4d64a-588">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4d64a-589">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="4d64a-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4d64a-590">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4d64a-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4d64a-591">WebRoot</span></span>

<span data-ttu-id="4d64a-592">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4d64a-593">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4d64a-594">**Chave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="4d64a-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="4d64a-595">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-595">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-596">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="4d64a-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4d64a-597">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="4d64a-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4d64a-598">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4d64a-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4d64a-599">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4d64a-600">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="4d64a-600">For more information, see:</span></span>

* [<span data-ttu-id="4d64a-601">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="4d64a-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4d64a-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4d64a-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4d64a-603">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="4d64a-603">Manage the host lifetime</span></span>

<span data-ttu-id="4d64a-604">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4d64a-605">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="4d64a-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4d64a-606">Executar</span><span class="sxs-lookup"><span data-stu-id="4d64a-606">Run</span></span>

<span data-ttu-id="4d64a-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4d64a-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-608">RunAsync</span></span>

<span data-ttu-id="4d64a-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4d64a-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-610">RunConsoleAsync</span></span>

<span data-ttu-id="4d64a-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="4d64a-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4d64a-612">Iniciar</span><span class="sxs-lookup"><span data-stu-id="4d64a-612">Start</span></span>

<span data-ttu-id="4d64a-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="4d64a-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4d64a-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-614">StartAsync</span></span>

<span data-ttu-id="4d64a-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4d64a-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="4d64a-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4d64a-617">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4d64a-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-618">StopAsync</span></span>

<span data-ttu-id="4d64a-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="4d64a-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4d64a-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4d64a-620">WaitForShutdown</span></span>

<span data-ttu-id="4d64a-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="4d64a-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4d64a-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="4d64a-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4d64a-624">Controle externo</span><span class="sxs-lookup"><span data-stu-id="4d64a-624">External control</span></span>

<span data-ttu-id="4d64a-625">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="4d64a-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4d64a-626">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="4d64a-627">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="4d64a-628">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="4d64a-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="4d64a-629">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="4d64a-630">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="4d64a-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="4d64a-631">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="4d64a-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="4d64a-632">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="4d64a-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4d64a-633">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="4d64a-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="4d64a-634">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4d64a-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4d64a-635">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="4d64a-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="4d64a-636">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="4d64a-637">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="4d64a-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="4d64a-638">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="4d64a-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="4d64a-639">Na configuração **Inicialização do .NET Core (console)**, localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="4d64a-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="4d64a-640">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="4d64a-641">Introdução</span><span class="sxs-lookup"><span data-stu-id="4d64a-641">Introduction</span></span>

<span data-ttu-id="4d64a-642">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="4d64a-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="4d64a-643">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="4d64a-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="4d64a-644"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="4d64a-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="4d64a-645">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="4d64a-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="4d64a-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="4d64a-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4d64a-647">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="4d64a-647">Set up a host</span></span>

<span data-ttu-id="4d64a-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="4d64a-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="4d64a-649">Opções</span><span class="sxs-lookup"><span data-stu-id="4d64a-649">Options</span></span>

<span data-ttu-id="4d64a-650">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="4d64a-651">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="4d64a-651">Shutdown timeout</span></span>

<span data-ttu-id="4d64a-652">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4d64a-653">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-653">The default value is five seconds.</span></span>

<span data-ttu-id="4d64a-654">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundo:</span><span class="sxs-lookup"><span data-stu-id="4d64a-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="4d64a-655">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="4d64a-655">Default services</span></span>

<span data-ttu-id="4d64a-656">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="4d64a-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="4d64a-657">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="4d64a-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="4d64a-658">[Configuração](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="4d64a-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="4d64a-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4d64a-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="4d64a-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4d64a-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="4d64a-661">[Opções](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="4d64a-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="4d64a-662">[Registro em log](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="4d64a-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4d64a-663">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="4d64a-663">Host configuration</span></span>

<span data-ttu-id="4d64a-664">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="4d64a-664">Host configuration is created by:</span></span>

* <span data-ttu-id="4d64a-665">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="4d64a-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="4d64a-666">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="4d64a-667">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="4d64a-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="4d64a-668">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="4d64a-668">Application key (name)</span></span>

<span data-ttu-id="4d64a-669">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="4d64a-670">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="4d64a-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="4d64a-671">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="4d64a-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4d64a-672">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-672">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-673">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="4d64a-674">**Definir usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="4d64a-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="4d64a-675">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4d64a-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="4d64a-676">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="4d64a-676">Content root</span></span>

<span data-ttu-id="4d64a-677">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="4d64a-678">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4d64a-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4d64a-679">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-679">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-680">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="4d64a-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="4d64a-681">**Definir usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="4d64a-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="4d64a-682">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4d64a-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4d64a-683">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="4d64a-684">Para obter mais informações, consulte [conceitos básicos: content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="4d64a-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="4d64a-685">Ambiente</span><span class="sxs-lookup"><span data-stu-id="4d64a-685">Environment</span></span>

<span data-ttu-id="4d64a-686">Define o [ambiente](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4d64a-687">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="4d64a-687">**Key**: `environment`</span></span>  
<span data-ttu-id="4d64a-688">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="4d64a-688">**Type**: `string`</span></span>  
<span data-ttu-id="4d64a-689">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="4d64a-689">**Default**: `Production`</span></span>  
<span data-ttu-id="4d64a-690">**Definir usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="4d64a-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="4d64a-691">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4d64a-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4d64a-692">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="4d64a-692">The environment can be set to any value.</span></span> <span data-ttu-id="4d64a-693">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4d64a-694">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="4d64a-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="4d64a-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="4d64a-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="4d64a-697">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="4d64a-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4d64a-699">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="4d64a-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4d64a-700">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="4d64a-700">No providers are included by default.</span></span> <span data-ttu-id="4d64a-701">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="4d64a-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="4d64a-702">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="4d64a-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="4d64a-703">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="4d64a-703">Environment variable configuration.</span></span>
* <span data-ttu-id="4d64a-704">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="4d64a-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="4d64a-705">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="4d64a-705">Any other required configuration providers.</span></span>

<span data-ttu-id="4d64a-706">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4d64a-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="4d64a-707">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="4d64a-708">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="4d64a-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="4d64a-709">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="4d64a-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="4d64a-710">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="4d64a-711">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4d64a-712">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="4d64a-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4d64a-713">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4d64a-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="4d64a-714">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="4d64a-715">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4d64a-716">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="4d64a-717">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="4d64a-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="4d64a-718">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4d64a-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="4d64a-719">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="4d64a-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="4d64a-720">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="4d64a-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="4d64a-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="4d64a-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4d64a-722">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="4d64a-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="4d64a-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4d64a-725">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="4d64a-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="4d64a-726">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="4d64a-727">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4d64a-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="4d64a-728">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="4d64a-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="4d64a-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4d64a-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="4d64a-730">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="4d64a-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="4d64a-731">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="4d64a-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="4d64a-732">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="4d64a-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="4d64a-733">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="4d64a-734">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="4d64a-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="4d64a-735">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="4d64a-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="4d64a-736">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="4d64a-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="4d64a-737">ConfigureServices</span></span>

<span data-ttu-id="4d64a-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4d64a-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4d64a-740">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="4d64a-741">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="4d64a-742">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4d64a-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="4d64a-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="4d64a-743">ConfigureLogging</span></span>

<span data-ttu-id="4d64a-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="4d64a-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="4d64a-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="4d64a-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-746">UseConsoleLifetime</span></span>

<span data-ttu-id="4d64a-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="4d64a-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="4d64a-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="4d64a-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="4d64a-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="4d64a-750">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="4d64a-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="4d64a-751">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="4d64a-751">Container configuration</span></span>

<span data-ttu-id="4d64a-752">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="4d64a-753">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="4d64a-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="4d64a-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="4d64a-755">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="4d64a-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="4d64a-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="4d64a-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4d64a-758">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4d64a-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="4d64a-759">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="4d64a-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="4d64a-760">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4d64a-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="4d64a-761">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="4d64a-761">Extensibility</span></span>

<span data-ttu-id="4d64a-762">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="4d64a-763">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="4d64a-764">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="4d64a-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="4d64a-765">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="4d64a-765">Manage the host</span></span>

<span data-ttu-id="4d64a-766">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="4d64a-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4d64a-767">Executar</span><span class="sxs-lookup"><span data-stu-id="4d64a-767">Run</span></span>

<span data-ttu-id="4d64a-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="4d64a-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="4d64a-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-769">RunAsync</span></span>

<span data-ttu-id="4d64a-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="4d64a-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="4d64a-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-771">RunConsoleAsync</span></span>

<span data-ttu-id="4d64a-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="4d64a-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="4d64a-773">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-773">Start and StopAsync</span></span>

<span data-ttu-id="4d64a-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="4d64a-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="4d64a-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="4d64a-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="4d64a-776">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="4d64a-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="4d64a-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="4d64a-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4d64a-779">WaitForShutdown</span></span>

<span data-ttu-id="4d64a-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>é disparado por meio do <xref:Microsoft.Extensions.Hosting.IHostLifetime> , como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escuta para <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="4d64a-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="4d64a-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="4d64a-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4d64a-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="4d64a-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="4d64a-784">Controle externo</span><span class="sxs-lookup"><span data-stu-id="4d64a-784">External control</span></span>

<span data-ttu-id="4d64a-785">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="4d64a-785">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="4d64a-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="4d64a-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4d64a-787">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="4d64a-788">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="4d64a-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="4d64a-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="4d64a-790">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="4d64a-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="4d64a-791">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4d64a-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="4d64a-792">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4d64a-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="4d64a-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4d64a-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="4d64a-794">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="4d64a-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4d64a-795">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="4d64a-795">Cancellation Token</span></span> | <span data-ttu-id="4d64a-796">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="4d64a-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="4d64a-797">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="4d64a-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="4d64a-798">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4d64a-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4d64a-799">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-799">All requests should be processed.</span></span> <span data-ttu-id="4d64a-800">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="4d64a-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="4d64a-801">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4d64a-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4d64a-802">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="4d64a-802">Requests may still be processing.</span></span> <span data-ttu-id="4d64a-803">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="4d64a-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="4d64a-804">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="4d64a-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="4d64a-805">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="4d64a-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="4d64a-806">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="4d64a-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="4d64a-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4d64a-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="4d64a-808">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="4d64a-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4d64a-809">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4d64a-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
