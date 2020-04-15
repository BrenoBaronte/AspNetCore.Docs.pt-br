---
title: Host Genérico .NET
author: rick-anderson
description: Saiba mais sobre o Host Genérico do .NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: b528a33fa52bfe56faaf9f3ff8c7e43db0d4e184
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384030"
---
# <a name="net-generic-host"></a><span data-ttu-id="0e3a7-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="0e3a7-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="0e3a7-104">Os modelos ASP.NET Core criam um<xref:Microsoft.Extensions.Hosting.HostBuilder>Host Genérico .NET Core ().</span><span class="sxs-lookup"><span data-stu-id="0e3a7-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="0e3a7-105">Definição do host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-105">Host definition</span></span>

<span data-ttu-id="0e3a7-106">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="0e3a7-107">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="0e3a7-108">Registro em log</span><span class="sxs-lookup"><span data-stu-id="0e3a7-108">Logging</span></span>
* <span data-ttu-id="0e3a7-109">Configuração</span><span class="sxs-lookup"><span data-stu-id="0e3a7-109">Configuration</span></span>
* <span data-ttu-id="0e3a7-110">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-110">`IHostedService` implementations</span></span>

<span data-ttu-id="0e3a7-111">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="0e3a7-112">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="0e3a7-113">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0e3a7-114">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-114">Set up a host</span></span>

<span data-ttu-id="0e3a7-115">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="0e3a7-116">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-116">The `Main` method:</span></span>

* <span data-ttu-id="0e3a7-117">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="0e3a7-118">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="0e3a7-119">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="0e3a7-120">O código a seguir cria uma `IHostedService` carga de trabalho não-HTTP com uma implementação adicionada ao contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-120">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="0e3a7-121">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="0e3a7-122">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="0e3a7-123">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="0e3a7-124">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="0e3a7-125">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="0e3a7-125">Default builder settings</span></span>

<span data-ttu-id="0e3a7-126">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="0e3a7-127">Define a raiz de [conteúdo](xref:fundamentals/index#content-root) <xref:System.IO.Directory.GetCurrentDirectory*>para o caminho retornado por .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="0e3a7-128">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="0e3a7-129">Variáveis de ambiente `DOTNET_`prefixadas com .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="0e3a7-130">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-130">Command-line arguments.</span></span>
* <span data-ttu-id="0e3a7-131">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="0e3a7-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="0e3a7-133">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="0e3a7-134">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="0e3a7-135">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-135">Environment variables.</span></span>
  * <span data-ttu-id="0e3a7-136">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-136">Command-line arguments.</span></span>
* <span data-ttu-id="0e3a7-137">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="0e3a7-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="0e3a7-138">Console</span><span class="sxs-lookup"><span data-stu-id="0e3a7-138">Console</span></span>
  * <span data-ttu-id="0e3a7-139">Depurar</span><span class="sxs-lookup"><span data-stu-id="0e3a7-139">Debug</span></span>
  * <span data-ttu-id="0e3a7-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="0e3a7-140">EventSource</span></span>
  * <span data-ttu-id="0e3a7-141">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="0e3a7-142">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="0e3a7-143">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="0e3a7-144">Carrega a configuração do host `ASPNETCORE_`a partir de variáveis de ambiente prefixadas com .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="0e3a7-145">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="0e3a7-146">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="0e3a7-147">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="0e3a7-148">Adiciona [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true`se for igual .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="0e3a7-149">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-149">Enables IIS integration.</span></span> <span data-ttu-id="0e3a7-150">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="0e3a7-151">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0e3a7-152">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="0e3a7-152">Framework-provided services</span></span>

<span data-ttu-id="0e3a7-153">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="0e3a7-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="0e3a7-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="0e3a7-156">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="0e3a7-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="0e3a7-157">Para obter mais informações sobre serviços fornecidos por estruturas, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="0e3a7-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="0e3a7-159">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="0e3a7-160">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="0e3a7-161">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="0e3a7-162">O exemplo a `IHostedService` seguir é `IHostApplicationLifetime` uma implementação que registra eventos:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="0e3a7-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-163">IHostLifetime</span></span>

<span data-ttu-id="0e3a7-164">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="0e3a7-165">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-165">The last implementation registered is used.</span></span>

<span data-ttu-id="0e3a7-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="0e3a7-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="0e3a7-168">Ouve <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="0e3a7-169">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="0e3a7-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="0e3a7-170">IHostEnvironment</span></span>

<span data-ttu-id="0e3a7-171">Injete o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="0e3a7-172">Applicationname</span><span class="sxs-lookup"><span data-stu-id="0e3a7-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="0e3a7-173">Nome do ambiente</span><span class="sxs-lookup"><span data-stu-id="0e3a7-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="0e3a7-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="0e3a7-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="0e3a7-175">Os aplicativos `IWebHostEnvironment` da Web implementam a interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0e3a7-176">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-176">Host configuration</span></span>

<span data-ttu-id="0e3a7-177">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="0e3a7-178">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="0e3a7-179">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="0e3a7-180">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0e3a7-181">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0e3a7-182">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0e3a7-183">O provedor de `DOTNET_` variável de ambiente com argumentos de prefixo e linha de comando são incluídos por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0e3a7-184">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="0e3a7-185">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0e3a7-186">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0e3a7-187">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="0e3a7-188">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-188">App configuration</span></span>

<span data-ttu-id="0e3a7-189">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0e3a7-190">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0e3a7-191">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="0e3a7-192">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="0e3a7-193">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="0e3a7-194">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="0e3a7-195">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-195">Settings for all app types</span></span>

<span data-ttu-id="0e3a7-196">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="0e3a7-197">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="0e3a7-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0e3a7-198">ApplicationName</span></span>

<span data-ttu-id="0e3a7-199">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="0e3a7-200">**Chave:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0e3a7-201">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-201">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-202">**Padrão**: O nome do conjunto que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="0e3a7-203">**Variável de ambiente:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="0e3a7-204">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="0e3a7-205">Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-205">ContentRoot</span></span>

<span data-ttu-id="0e3a7-206">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="0e3a7-207">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="0e3a7-208">**Chave:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0e3a7-209">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-209">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-210">**Padrão**: A pasta onde reside o conjunto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="0e3a7-211">**Variável de ambiente:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="0e3a7-212">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="0e3a7-213">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-213">For more information, see:</span></span>

* [<span data-ttu-id="0e3a7-214">Fundamentos: Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="0e3a7-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0e3a7-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="0e3a7-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0e3a7-216">EnvironmentName</span></span>

<span data-ttu-id="0e3a7-217">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="0e3a7-218">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0e3a7-219">Valores não são sensíveis a casos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="0e3a7-220">**Chave:**`environment`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-220">**Key**: `environment`</span></span>  
<span data-ttu-id="0e3a7-221">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-221">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-222">**Padrão:**`Production`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-222">**Default**: `Production`</span></span>  
<span data-ttu-id="0e3a7-223">**Variável de ambiente:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="0e3a7-224">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="0e3a7-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="0e3a7-225">ShutdownTimeout</span></span>

<span data-ttu-id="0e3a7-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0e3a7-227">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-227">The default value is five seconds.</span></span>  <span data-ttu-id="0e3a7-228">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="0e3a7-229">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="0e3a7-230">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="0e3a7-231">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="0e3a7-232">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="0e3a7-233">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="0e3a7-234">**Chave:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="0e3a7-235">**Tipo:**`int`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-235">**Type**: `int`</span></span>  
<span data-ttu-id="0e3a7-236">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="0e3a7-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="0e3a7-237">**Variável de ambiente:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="0e3a7-238">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="0e3a7-239">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="0e3a7-240">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="0e3a7-240">Settings for web apps</span></span>

<span data-ttu-id="0e3a7-241">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="0e3a7-242">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="0e3a7-243">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="0e3a7-244">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="0e3a7-245">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="0e3a7-245">CaptureStartupErrors</span></span>

<span data-ttu-id="0e3a7-246">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="0e3a7-247">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="0e3a7-248">**Chave:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-248">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="0e3a7-249">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-249">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-250">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="0e3a7-251">**Variável de ambiente:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="0e3a7-252">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="0e3a7-253">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="0e3a7-253">DetailedErrors</span></span>

<span data-ttu-id="0e3a7-254">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="0e3a7-255">**Chave:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-255">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="0e3a7-256">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-256">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-257">**Padrão:**`false`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-257">**Default**: `false`</span></span>  
<span data-ttu-id="0e3a7-258">**Variável de ambiente:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="0e3a7-259">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="0e3a7-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="0e3a7-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="0e3a7-261">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="0e3a7-262">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="0e3a7-263">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="0e3a7-264">**Chave:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-264">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="0e3a7-265">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-265">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-266">**Padrão**: Cadeia de seqüência vazia</span><span class="sxs-lookup"><span data-stu-id="0e3a7-266">**Default**: Empty string</span></span>  
<span data-ttu-id="0e3a7-267">**Variável de ambiente:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="0e3a7-268">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="0e3a7-269">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="0e3a7-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="0e3a7-270">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="0e3a7-271">**Chave:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-271">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="0e3a7-272">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-272">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-273">**Padrão**: Cadeia de seqüência vazia</span><span class="sxs-lookup"><span data-stu-id="0e3a7-273">**Default**: Empty string</span></span>  
<span data-ttu-id="0e3a7-274">**Variável de ambiente:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="0e3a7-275">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="0e3a7-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="0e3a7-276">HTTPS_Port</span></span>

<span data-ttu-id="0e3a7-277">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-277">The HTTPS redirect port.</span></span> <span data-ttu-id="0e3a7-278">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0e3a7-279">**Chave:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-279">**Key**: `https_port`</span></span>  
<span data-ttu-id="0e3a7-280">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-280">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-281">**Padrão**: Um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="0e3a7-282">**Variável de ambiente:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="0e3a7-283">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="0e3a7-284">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="0e3a7-284">PreferHostingUrls</span></span>

<span data-ttu-id="0e3a7-285">Indica se o host deve ouvir as URLs configuradas com as `IWebHostBuilder` `IServer` URLs configuradas com a implementação.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="0e3a7-286">**Chave:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-286">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="0e3a7-287">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-287">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-288">**Padrão:**`true`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-288">**Default**: `true`</span></span>  
<span data-ttu-id="0e3a7-289">**Variável de ambiente:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="0e3a7-290">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="0e3a7-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="0e3a7-291">PreventHostingStartup</span></span>

<span data-ttu-id="0e3a7-292">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="0e3a7-293">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="0e3a7-294">**Chave:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-294">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="0e3a7-295">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-295">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-296">**Padrão:**`false`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-296">**Default**: `false`</span></span>  
<span data-ttu-id="0e3a7-297">**Variável de ambiente:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="0e3a7-298">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="0e3a7-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="0e3a7-299">StartupAssembly</span></span>

<span data-ttu-id="0e3a7-300">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="0e3a7-301">**Chave:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-301">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="0e3a7-302">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-302">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-303">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="0e3a7-304">**Variável de ambiente:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="0e3a7-305">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="0e3a7-306">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="0e3a7-307">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="0e3a7-308">URLs</span><span class="sxs-lookup"><span data-stu-id="0e3a7-308">URLs</span></span>

<span data-ttu-id="0e3a7-309">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="0e3a7-310">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="0e3a7-311">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="0e3a7-312">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="0e3a7-313">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="0e3a7-314">**Chave:**`urls`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-314">**Key**: `urls`</span></span>  
<span data-ttu-id="0e3a7-315">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-315">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-316">**Padrão** `http://localhost:5000` : e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="0e3a7-317">**Variável de ambiente:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="0e3a7-318">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="0e3a7-319">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="0e3a7-320">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="0e3a7-321">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0e3a7-321">WebRoot</span></span>

<span data-ttu-id="0e3a7-322">A propriedade [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo aos ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-322">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="0e3a7-323">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-323">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="0e3a7-324">**Chave:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-324">**Key**: `webroot`</span></span>  
<span data-ttu-id="0e3a7-325">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-325">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-326">**Padrão**: O `wwwroot`padrão é .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="0e3a7-327">O caminho para *{content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-327">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="0e3a7-328">**Variável de ambiente:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="0e3a7-329">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-329">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="0e3a7-330">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-330">For more information, see:</span></span>

* [<span data-ttu-id="0e3a7-331">Fundamentos: Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="0e3a7-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="0e3a7-332">Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-332">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="0e3a7-333">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-333">Manage the host lifetime</span></span>

<span data-ttu-id="0e3a7-334">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="0e3a7-335">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0e3a7-336">Executar</span><span class="sxs-lookup"><span data-stu-id="0e3a7-336">Run</span></span>

<span data-ttu-id="0e3a7-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="0e3a7-338">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-338">RunAsync</span></span>

<span data-ttu-id="0e3a7-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="0e3a7-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-340">RunConsoleAsync</span></span>

<span data-ttu-id="0e3a7-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permite o suporte ao console, constrói e inicia o host e aguarda o desligamento <kbd>do Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="0e3a7-342">Iniciar</span><span class="sxs-lookup"><span data-stu-id="0e3a7-342">Start</span></span>

<span data-ttu-id="0e3a7-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="0e3a7-344">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-344">StartAsync</span></span>

<span data-ttu-id="0e3a7-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="0e3a7-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0e3a7-347">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="0e3a7-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-348">StopAsync</span></span>

<span data-ttu-id="0e3a7-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="0e3a7-350">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="0e3a7-350">WaitForShutdown</span></span>

<span data-ttu-id="0e3a7-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o segmento de chamada até que o desligamento seja acionado pelo IHostLifetime, como via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="0e3a7-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="0e3a7-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="0e3a7-354">Controle externo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-354">External control</span></span>

<span data-ttu-id="0e3a7-355">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0e3a7-356">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="0e3a7-357">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="0e3a7-358">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="0e3a7-359">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="0e3a7-360">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="0e3a7-361">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="0e3a7-362">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="0e3a7-363">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="0e3a7-364">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0e3a7-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0e3a7-365">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="0e3a7-366">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="0e3a7-367">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="0e3a7-368">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="0e3a7-369">Na configuração **Inicialização do .NET Core (console)**, localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="0e3a7-370">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="0e3a7-371">Introdução</span><span class="sxs-lookup"><span data-stu-id="0e3a7-371">Introduction</span></span>

<span data-ttu-id="0e3a7-372">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="0e3a7-373">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="0e3a7-374"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="0e3a7-375">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="0e3a7-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0e3a7-377">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-377">Set up a host</span></span>

<span data-ttu-id="0e3a7-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="0e3a7-379">Opções</span><span class="sxs-lookup"><span data-stu-id="0e3a7-379">Options</span></span>

<span data-ttu-id="0e3a7-380">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="0e3a7-381">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="0e3a7-381">Shutdown timeout</span></span>

<span data-ttu-id="0e3a7-382">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0e3a7-383">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-383">The default value is five seconds.</span></span>

<span data-ttu-id="0e3a7-384">A seguinte configuração de opção aumenta `Program.Main` o tempo de desligamento padrão de cinco segundos para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-384">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="0e3a7-385">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="0e3a7-385">Default services</span></span>

<span data-ttu-id="0e3a7-386">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="0e3a7-387">[Meio](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>Ambiente ( )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="0e3a7-388">[Configuração](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="0e3a7-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="0e3a7-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="0e3a7-391">[Opções](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="0e3a7-392">[Logging](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0e3a7-393">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-393">Host configuration</span></span>

<span data-ttu-id="0e3a7-394">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-394">Host configuration is created by:</span></span>

* <span data-ttu-id="0e3a7-395">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="0e3a7-396">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="0e3a7-397">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="0e3a7-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="0e3a7-398">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-398">Application key (name)</span></span>

<span data-ttu-id="0e3a7-399">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="0e3a7-400">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="0e3a7-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="0e3a7-401">**Chave:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-401">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0e3a7-402">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-402">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-403">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="0e3a7-404">**Conjunto usando:**`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="0e3a7-405">**Variável**de `<PREFIX_>APPLICATIONNAME` `<PREFIX_>` ambiente : (é [opcional e definido pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0e3a7-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="0e3a7-406">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-406">Content root</span></span>

<span data-ttu-id="0e3a7-407">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="0e3a7-408">**Chave:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-408">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0e3a7-409">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-409">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-410">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="0e3a7-411">**Conjunto usando:**`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="0e3a7-412">**Variável**de `<PREFIX_>CONTENTROOT` `<PREFIX_>` ambiente : (é [opcional e definido pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0e3a7-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="0e3a7-413">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="0e3a7-414">Para obter mais informações, consulte [Fundamentos: Raiz de conteúdo](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="0e3a7-415">Ambiente</span><span class="sxs-lookup"><span data-stu-id="0e3a7-415">Environment</span></span>

<span data-ttu-id="0e3a7-416">Define o [ambiente](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0e3a7-417">**Chave:**`environment`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-417">**Key**: `environment`</span></span>  
<span data-ttu-id="0e3a7-418">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-418">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-419">**Padrão:**`Production`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-419">**Default**: `Production`</span></span>  
<span data-ttu-id="0e3a7-420">**Conjunto usando:**`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="0e3a7-421">**Variável**de `<PREFIX_>ENVIRONMENT` `<PREFIX_>` ambiente : (é [opcional e definido pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0e3a7-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="0e3a7-422">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-422">The environment can be set to any value.</span></span> <span data-ttu-id="0e3a7-423">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0e3a7-424">Valores não são sensíveis a casos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-424">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="0e3a7-425">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="0e3a7-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="0e3a7-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="0e3a7-427">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="0e3a7-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="0e3a7-429">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0e3a7-430">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-430">No providers are included by default.</span></span> <span data-ttu-id="0e3a7-431">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="0e3a7-432">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="0e3a7-433">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-433">Environment variable configuration.</span></span>
* <span data-ttu-id="0e3a7-434">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="0e3a7-435">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-435">Any other required configuration providers.</span></span>

<span data-ttu-id="0e3a7-436">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="0e3a7-437">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="0e3a7-438">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="0e3a7-439">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="0e3a7-440">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="0e3a7-441">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0e3a7-442">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0e3a7-443">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="0e3a7-444">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="0e3a7-445">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0e3a7-446">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="0e3a7-447">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="0e3a7-448">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="0e3a7-449">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="0e3a7-450">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="0e3a7-451">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="0e3a7-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="0e3a7-452">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="0e3a7-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="0e3a7-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="0e3a7-455">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="0e3a7-456">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="0e3a7-457">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="0e3a7-458">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="0e3a7-459">*appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="0e3a7-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="0e3a7-460">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="0e3a7-461">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="0e3a7-462">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="0e3a7-463">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="0e3a7-464">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="0e3a7-465">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="0e3a7-466">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="0e3a7-467">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="0e3a7-467">ConfigureServices</span></span>

<span data-ttu-id="0e3a7-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0e3a7-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="0e3a7-470">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0e3a7-471">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="0e3a7-472">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-472">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="0e3a7-473">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="0e3a7-473">ConfigureLogging</span></span>

<span data-ttu-id="0e3a7-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="0e3a7-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="0e3a7-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-476">UseConsoleLifetime</span></span>

<span data-ttu-id="0e3a7-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>ouve <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chamadas <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="0e3a7-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="0e3a7-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="0e3a7-480">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="0e3a7-481">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="0e3a7-481">Container configuration</span></span>

<span data-ttu-id="0e3a7-482">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="0e3a7-483">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="0e3a7-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="0e3a7-485">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="0e3a7-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="0e3a7-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="0e3a7-488">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="0e3a7-489">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="0e3a7-490">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="0e3a7-491">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="0e3a7-491">Extensibility</span></span>

<span data-ttu-id="0e3a7-492">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="0e3a7-493">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="0e3a7-494">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="0e3a7-495">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-495">Manage the host</span></span>

<span data-ttu-id="0e3a7-496">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0e3a7-497">Executar</span><span class="sxs-lookup"><span data-stu-id="0e3a7-497">Run</span></span>

<span data-ttu-id="0e3a7-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="0e3a7-499">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-499">RunAsync</span></span>

<span data-ttu-id="0e3a7-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="0e3a7-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-501">RunConsoleAsync</span></span>

<span data-ttu-id="0e3a7-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permite o suporte ao console, constrói e inicia o host e aguarda o desligamento <kbd>do Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="0e3a7-503">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-503">Start and StopAsync</span></span>

<span data-ttu-id="0e3a7-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="0e3a7-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="0e3a7-506">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="0e3a7-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="0e3a7-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="0e3a7-509">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="0e3a7-509">WaitForShutdown</span></span>

<span data-ttu-id="0e3a7-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>é acionado através <xref:Microsoft.Extensions.Hosting.IHostLifetime>do `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , tais como (escuta <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="0e3a7-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="0e3a7-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="0e3a7-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="0e3a7-514">Controle externo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-514">External control</span></span>

<span data-ttu-id="0e3a7-515">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-515">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0e3a7-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0e3a7-517">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="0e3a7-518">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="0e3a7-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="0e3a7-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="0e3a7-520">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="0e3a7-521">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="0e3a7-522">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="0e3a7-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="0e3a7-524">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="0e3a7-525">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="0e3a7-525">Cancellation Token</span></span> | <span data-ttu-id="0e3a7-526">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="0e3a7-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="0e3a7-527">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="0e3a7-528">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="0e3a7-529">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-529">All requests should be processed.</span></span> <span data-ttu-id="0e3a7-530">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="0e3a7-531">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="0e3a7-532">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-532">Requests may still be processing.</span></span> <span data-ttu-id="0e3a7-533">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="0e3a7-534">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="0e3a7-535">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-535">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="0e3a7-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="0e3a7-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="0e3a7-538">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0e3a7-539">Os modelos ASP.NET Core criam um<xref:Microsoft.Extensions.Hosting.HostBuilder>Host Genérico .NET Core ().</span><span class="sxs-lookup"><span data-stu-id="0e3a7-539">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="0e3a7-540">Definição do host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-540">Host definition</span></span>

<span data-ttu-id="0e3a7-541">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-541">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="0e3a7-542">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-542">Dependency injection (DI)</span></span>
* <span data-ttu-id="0e3a7-543">Registro em log</span><span class="sxs-lookup"><span data-stu-id="0e3a7-543">Logging</span></span>
* <span data-ttu-id="0e3a7-544">Configuração</span><span class="sxs-lookup"><span data-stu-id="0e3a7-544">Configuration</span></span>
* <span data-ttu-id="0e3a7-545">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-545">`IHostedService` implementations</span></span>

<span data-ttu-id="0e3a7-546">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-546">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="0e3a7-547">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-547">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="0e3a7-548">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-548">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0e3a7-549">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-549">Set up a host</span></span>

<span data-ttu-id="0e3a7-550">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-550">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="0e3a7-551">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-551">The `Main` method:</span></span>

* <span data-ttu-id="0e3a7-552">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-552">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="0e3a7-553">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-553">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="0e3a7-554">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-554">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="0e3a7-555">O código a seguir cria uma `IHostedService` carga de trabalho não-HTTP com uma implementação adicionada ao contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-555">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="0e3a7-556">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-556">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="0e3a7-557">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-557">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="0e3a7-558">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-558">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="0e3a7-559">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-559">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="0e3a7-560">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="0e3a7-560">Default builder settings</span></span>

<span data-ttu-id="0e3a7-561">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-561">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="0e3a7-562">Define a raiz de [conteúdo](xref:fundamentals/index#content-root) <xref:System.IO.Directory.GetCurrentDirectory*>para o caminho retornado por .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-562">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="0e3a7-563">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-563">Loads host configuration from:</span></span>
  * <span data-ttu-id="0e3a7-564">Variáveis de ambiente `DOTNET_`prefixadas com .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-564">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="0e3a7-565">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-565">Command-line arguments.</span></span>
* <span data-ttu-id="0e3a7-566">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-566">Loads app configuration from:</span></span>
  * <span data-ttu-id="0e3a7-567">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-567">*appsettings.json*.</span></span>
  * <span data-ttu-id="0e3a7-568">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-568">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="0e3a7-569">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-569">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="0e3a7-570">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-570">Environment variables.</span></span>
  * <span data-ttu-id="0e3a7-571">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-571">Command-line arguments.</span></span>
* <span data-ttu-id="0e3a7-572">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="0e3a7-572">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="0e3a7-573">Console</span><span class="sxs-lookup"><span data-stu-id="0e3a7-573">Console</span></span>
  * <span data-ttu-id="0e3a7-574">Depurar</span><span class="sxs-lookup"><span data-stu-id="0e3a7-574">Debug</span></span>
  * <span data-ttu-id="0e3a7-575">EventSource</span><span class="sxs-lookup"><span data-stu-id="0e3a7-575">EventSource</span></span>
  * <span data-ttu-id="0e3a7-576">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="0e3a7-576">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="0e3a7-577">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-577">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="0e3a7-578">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-578">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="0e3a7-579">Carrega a configuração do host `ASPNETCORE_`a partir de variáveis de ambiente prefixadas com .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-579">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="0e3a7-580">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-580">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="0e3a7-581">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-581">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="0e3a7-582">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-582">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="0e3a7-583">Adiciona [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true`se for igual .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-583">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="0e3a7-584">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-584">Enables IIS integration.</span></span> <span data-ttu-id="0e3a7-585">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-585">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="0e3a7-586">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-586">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0e3a7-587">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="0e3a7-587">Framework-provided services</span></span>

<span data-ttu-id="0e3a7-588">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-588">The following services are registered automatically:</span></span>

* [<span data-ttu-id="0e3a7-589">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-589">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="0e3a7-590">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-590">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="0e3a7-591">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="0e3a7-591">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="0e3a7-592">Para obter mais informações sobre serviços fornecidos por estruturas, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-592">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="0e3a7-593">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-593">IHostApplicationLifetime</span></span>

<span data-ttu-id="0e3a7-594">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-594">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="0e3a7-595">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-595">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="0e3a7-596">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-596">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="0e3a7-597">O exemplo a `IHostedService` seguir é `IHostApplicationLifetime` uma implementação que registra eventos:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-597">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="0e3a7-598">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="0e3a7-598">IHostLifetime</span></span>

<span data-ttu-id="0e3a7-599">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-599">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="0e3a7-600">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-600">The last implementation registered is used.</span></span>

<span data-ttu-id="0e3a7-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="0e3a7-602">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-602">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="0e3a7-603">Ouve <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-603">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="0e3a7-604">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-604">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="0e3a7-605">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="0e3a7-605">IHostEnvironment</span></span>

<span data-ttu-id="0e3a7-606">Injete o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-606">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="0e3a7-607">Applicationname</span><span class="sxs-lookup"><span data-stu-id="0e3a7-607">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="0e3a7-608">Nome do ambiente</span><span class="sxs-lookup"><span data-stu-id="0e3a7-608">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="0e3a7-609">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="0e3a7-609">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="0e3a7-610">Os aplicativos `IWebHostEnvironment` da Web implementam a interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-610">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0e3a7-611">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-611">Host configuration</span></span>

<span data-ttu-id="0e3a7-612">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-612">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="0e3a7-613">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-613">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="0e3a7-614">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-614">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="0e3a7-615">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-615">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0e3a7-616">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-616">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0e3a7-617">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-617">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0e3a7-618">O provedor de `DOTNET_` variável de ambiente com argumentos de prefixo e linha de comando são incluídos por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-618">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0e3a7-619">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-619">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="0e3a7-620">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-620">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0e3a7-621">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-621">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0e3a7-622">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-622">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="0e3a7-623">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-623">App configuration</span></span>

<span data-ttu-id="0e3a7-624">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-624">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0e3a7-625">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-625">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0e3a7-626">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-626">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="0e3a7-627">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-627">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="0e3a7-628">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-628">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="0e3a7-629">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-629">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="0e3a7-630">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-630">Settings for all app types</span></span>

<span data-ttu-id="0e3a7-631">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-631">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="0e3a7-632">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-632">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="0e3a7-633">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0e3a7-633">ApplicationName</span></span>

<span data-ttu-id="0e3a7-634">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-634">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="0e3a7-635">**Chave:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-635">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0e3a7-636">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-636">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-637">**Padrão**: O nome do conjunto que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-637">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="0e3a7-638">**Variável de ambiente:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-638">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="0e3a7-639">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-639">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="0e3a7-640">Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-640">ContentRoot</span></span>

<span data-ttu-id="0e3a7-641">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-641">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="0e3a7-642">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-642">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="0e3a7-643">**Chave:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-643">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0e3a7-644">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-644">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-645">**Padrão**: A pasta onde reside o conjunto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-645">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="0e3a7-646">**Variável de ambiente:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-646">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="0e3a7-647">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-647">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="0e3a7-648">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-648">For more information, see:</span></span>

* [<span data-ttu-id="0e3a7-649">Fundamentos: Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-649">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="0e3a7-650">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0e3a7-650">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="0e3a7-651">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0e3a7-651">EnvironmentName</span></span>

<span data-ttu-id="0e3a7-652">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-652">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="0e3a7-653">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-653">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0e3a7-654">Valores não são sensíveis a casos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-654">Values aren't case-sensitive.</span></span>

<span data-ttu-id="0e3a7-655">**Chave:**`environment`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-655">**Key**: `environment`</span></span>  
<span data-ttu-id="0e3a7-656">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-656">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-657">**Padrão:**`Production`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-657">**Default**: `Production`</span></span>  
<span data-ttu-id="0e3a7-658">**Variável de ambiente:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-658">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="0e3a7-659">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-659">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="0e3a7-660">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="0e3a7-660">ShutdownTimeout</span></span>

<span data-ttu-id="0e3a7-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0e3a7-662">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-662">The default value is five seconds.</span></span>  <span data-ttu-id="0e3a7-663">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-663">During the timeout period, the host:</span></span>

* <span data-ttu-id="0e3a7-664">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-664">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="0e3a7-665">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-665">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="0e3a7-666">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-666">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="0e3a7-667">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-667">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="0e3a7-668">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-668">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="0e3a7-669">**Chave:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-669">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="0e3a7-670">**Tipo:**`int`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-670">**Type**: `int`</span></span>  
<span data-ttu-id="0e3a7-671">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="0e3a7-671">**Default**: 5 seconds</span></span>  
<span data-ttu-id="0e3a7-672">**Variável de ambiente:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-672">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="0e3a7-673">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-673">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="0e3a7-674">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-674">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="0e3a7-675">Desativar a recarga da configuração do aplicativo na alteração</span><span class="sxs-lookup"><span data-stu-id="0e3a7-675">Disable app configuration reload on change</span></span>

<span data-ttu-id="0e3a7-676">Por [padrão,](xref:fundamentals/configuration/index#default) *appsettings.json* e *appsettings.{ O ambiente}.json* é recarregado quando o arquivo é alterado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-676">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="0e3a7-677">Para desativar esse comportamento de recarga no ASP.NET O Núcleo `hostBuilder:reloadConfigOnChange` 5.0 Visualização 3 ou posterior, defina a chave para `false`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-677">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="0e3a7-678">**Chave:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-678">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="0e3a7-679">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-679">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-680">**Padrão:**`true`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-680">**Default**: `true`</span></span>  
<span data-ttu-id="0e3a7-681">**Argumento de linha de comando:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-681">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="0e3a7-682">**Variável de ambiente:**`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-682">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="0e3a7-683">O separador de cólon`:`não funciona com teclas hierárquicas variáveis de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-683">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="0e3a7-684">Para obter mais informações, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-684">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="0e3a7-685">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="0e3a7-685">Settings for web apps</span></span>

<span data-ttu-id="0e3a7-686">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-686">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="0e3a7-687">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-687">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="0e3a7-688">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-688">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="0e3a7-689">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-689">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="0e3a7-690">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="0e3a7-690">CaptureStartupErrors</span></span>

<span data-ttu-id="0e3a7-691">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-691">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="0e3a7-692">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-692">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="0e3a7-693">**Chave:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-693">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="0e3a7-694">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-694">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-695">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-695">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="0e3a7-696">**Variável de ambiente:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-696">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="0e3a7-697">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-697">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="0e3a7-698">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="0e3a7-698">DetailedErrors</span></span>

<span data-ttu-id="0e3a7-699">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-699">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="0e3a7-700">**Chave:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-700">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="0e3a7-701">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-701">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-702">**Padrão:**`false`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-702">**Default**: `false`</span></span>  
<span data-ttu-id="0e3a7-703">**Variável de ambiente:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-703">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="0e3a7-704">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-704">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="0e3a7-705">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="0e3a7-705">HostingStartupAssemblies</span></span>

<span data-ttu-id="0e3a7-706">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-706">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="0e3a7-707">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-707">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="0e3a7-708">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-708">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="0e3a7-709">**Chave:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-709">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="0e3a7-710">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-710">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-711">**Padrão**: Cadeia de seqüência vazia</span><span class="sxs-lookup"><span data-stu-id="0e3a7-711">**Default**: Empty string</span></span>  
<span data-ttu-id="0e3a7-712">**Variável de ambiente:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-712">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="0e3a7-713">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-713">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="0e3a7-714">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="0e3a7-714">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="0e3a7-715">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-715">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="0e3a7-716">**Chave:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-716">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="0e3a7-717">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-717">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-718">**Padrão**: Cadeia de seqüência vazia</span><span class="sxs-lookup"><span data-stu-id="0e3a7-718">**Default**: Empty string</span></span>  
<span data-ttu-id="0e3a7-719">**Variável de ambiente:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-719">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="0e3a7-720">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-720">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="0e3a7-721">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="0e3a7-721">HTTPS_Port</span></span>

<span data-ttu-id="0e3a7-722">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-722">The HTTPS redirect port.</span></span> <span data-ttu-id="0e3a7-723">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-723">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0e3a7-724">**Chave:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-724">**Key**: `https_port`</span></span>  
<span data-ttu-id="0e3a7-725">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-725">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-726">**Padrão**: Um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-726">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="0e3a7-727">**Variável de ambiente:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-727">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="0e3a7-728">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-728">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="0e3a7-729">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="0e3a7-729">PreferHostingUrls</span></span>

<span data-ttu-id="0e3a7-730">Indica se o host deve ouvir as URLs configuradas com as `IWebHostBuilder` `IServer` URLs configuradas com a implementação.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-730">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="0e3a7-731">**Chave:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-731">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="0e3a7-732">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-732">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-733">**Padrão:**`true`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-733">**Default**: `true`</span></span>  
<span data-ttu-id="0e3a7-734">**Variável de ambiente:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-734">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="0e3a7-735">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-735">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="0e3a7-736">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="0e3a7-736">PreventHostingStartup</span></span>

<span data-ttu-id="0e3a7-737">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-737">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="0e3a7-738">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-738">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="0e3a7-739">**Chave:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-739">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="0e3a7-740">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="0e3a7-740">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0e3a7-741">**Padrão:**`false`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-741">**Default**: `false`</span></span>  
<span data-ttu-id="0e3a7-742">**Variável de ambiente:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-742">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="0e3a7-743">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-743">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="0e3a7-744">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="0e3a7-744">StartupAssembly</span></span>

<span data-ttu-id="0e3a7-745">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-745">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="0e3a7-746">**Chave:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-746">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="0e3a7-747">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-747">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-748">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-748">**Default**: The app's assembly</span></span>  
<span data-ttu-id="0e3a7-749">**Variável de ambiente:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-749">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="0e3a7-750">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-750">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="0e3a7-751">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-751">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="0e3a7-752">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-752">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="0e3a7-753">URLs</span><span class="sxs-lookup"><span data-stu-id="0e3a7-753">URLs</span></span>

<span data-ttu-id="0e3a7-754">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-754">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="0e3a7-755">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-755">For example, `http://localhost:123`.</span></span> <span data-ttu-id="0e3a7-756">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="0e3a7-756">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="0e3a7-757">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-757">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="0e3a7-758">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-758">Supported formats vary among servers.</span></span>

<span data-ttu-id="0e3a7-759">**Chave:**`urls`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-759">**Key**: `urls`</span></span>  
<span data-ttu-id="0e3a7-760">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-760">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-761">**Padrão** `http://localhost:5000` : e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-761">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="0e3a7-762">**Variável de ambiente:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-762">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="0e3a7-763">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-763">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="0e3a7-764">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-764">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="0e3a7-765">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-765">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="0e3a7-766">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0e3a7-766">WebRoot</span></span>

<span data-ttu-id="0e3a7-767">A propriedade [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo aos ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-767">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="0e3a7-768">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-768">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="0e3a7-769">**Chave:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-769">**Key**: `webroot`</span></span>  
<span data-ttu-id="0e3a7-770">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-770">**Type**: `string`</span></span>  
<span data-ttu-id="0e3a7-771">**Padrão**: O `wwwroot`padrão é .</span><span class="sxs-lookup"><span data-stu-id="0e3a7-771">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="0e3a7-772">O caminho para *{content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-772">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="0e3a7-773">**Variável de ambiente:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="0e3a7-773">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="0e3a7-774">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-774">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="0e3a7-775">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-775">For more information, see:</span></span>

* [<span data-ttu-id="0e3a7-776">Fundamentos: Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="0e3a7-776">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="0e3a7-777">Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-777">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="0e3a7-778">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="0e3a7-778">Manage the host lifetime</span></span>

<span data-ttu-id="0e3a7-779">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-779">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="0e3a7-780">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-780">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0e3a7-781">Executar</span><span class="sxs-lookup"><span data-stu-id="0e3a7-781">Run</span></span>

<span data-ttu-id="0e3a7-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="0e3a7-783">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-783">RunAsync</span></span>

<span data-ttu-id="0e3a7-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="0e3a7-785">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-785">RunConsoleAsync</span></span>

<span data-ttu-id="0e3a7-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permite o suporte ao console, constrói e inicia o host e aguarda o desligamento <kbd>do Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="0e3a7-787">Iniciar</span><span class="sxs-lookup"><span data-stu-id="0e3a7-787">Start</span></span>

<span data-ttu-id="0e3a7-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="0e3a7-789">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-789">StartAsync</span></span>

<span data-ttu-id="0e3a7-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="0e3a7-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0e3a7-792">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-792">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="0e3a7-793">StopAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-793">StopAsync</span></span>

<span data-ttu-id="0e3a7-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="0e3a7-795">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="0e3a7-795">WaitForShutdown</span></span>

<span data-ttu-id="0e3a7-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o segmento de chamada até que o desligamento seja acionado pelo IHostLifetime, como via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="0e3a7-797">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0e3a7-797">WaitForShutdownAsync</span></span>

<span data-ttu-id="0e3a7-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0e3a7-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="0e3a7-799">Controle externo</span><span class="sxs-lookup"><span data-stu-id="0e3a7-799">External control</span></span>

<span data-ttu-id="0e3a7-800">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="0e3a7-800">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0e3a7-801">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0e3a7-801">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
