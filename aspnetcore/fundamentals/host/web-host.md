---
title: Host da Web do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o host da Web no ASP.NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 98be96bf60441cf09a315dbd1c60e109a7a08afe
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253105"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="a2162-103">Host da Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2162-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="a2162-104">ASP.NET Core aplicativos configuram e iniciam um *host*.</span><span class="sxs-lookup"><span data-stu-id="a2162-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="a2162-105">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a2162-106">No mínimo, o host configura um servidor e um pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="a2162-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="a2162-107">O host também pode configurar registro em log, a injeção de dependência e a configuração.</span><span class="sxs-lookup"><span data-stu-id="a2162-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a2162-108">Este artigo aborda o host da Web, que permanece disponível somente para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="a2162-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="a2162-109">O [Host genérico](xref:fundamentals/host/generic-host) é recomendado para todos os tipos de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a2162-110">Este artigo aborda o host da Web, que serve para hospedar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="a2162-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="a2162-111">Para outros tipos de aplicativos, use o [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a2162-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="a2162-112">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="a2162-112">Set up a host</span></span>

<span data-ttu-id="a2162-113">Crie um host usando uma instância do [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="a2162-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="a2162-114">Normalmente, isso é feito no ponto de entrada do aplicativo, o método `Main`.</span><span class="sxs-lookup"><span data-stu-id="a2162-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="a2162-115">Nos modelos de projeto, `Main` está localizado em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a2162-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="a2162-116">Um aplicativo típico chama [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para começar a configurar um host:</span><span class="sxs-lookup"><span data-stu-id="a2162-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="a2162-117">O código que chama `CreateDefaultBuilder` está em um método chamado `CreateWebHostBuilder`, que o separa do código em `Main`, que chama `Run` no objeto de construtor.</span><span class="sxs-lookup"><span data-stu-id="a2162-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="a2162-118">Essa separação será necessária se você usar [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="a2162-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="a2162-119">As ferramentas esperam encontrar um método `CreateWebHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="a2162-120">Uma alternativa é implementar `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="a2162-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="a2162-121">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="a2162-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="a2162-122">`CreateDefaultBuilder` realiza as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="a2162-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="a2162-123">Configura o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="a2162-124">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel/options>.</span><span class="sxs-lookup"><span data-stu-id="a2162-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* <span data-ttu-id="a2162-125">Configura o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-125">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="a2162-126">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="a2162-126">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
::: moniker-end
* <span data-ttu-id="a2162-127">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por [Directory. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="a2162-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="a2162-128">Carrega a [configuração do host](#host-configuration-values) de:</span><span class="sxs-lookup"><span data-stu-id="a2162-128">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="a2162-129">Variáveis de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="a2162-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="a2162-130">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="a2162-130">Command-line arguments.</span></span>
* <span data-ttu-id="a2162-131">Carrega a configuração do aplicativo na seguinte ordem de:</span><span class="sxs-lookup"><span data-stu-id="a2162-131">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="a2162-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a2162-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="a2162-133">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="a2162-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="a2162-134">[Segredos do usuário](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="a2162-134">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="a2162-135">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="a2162-135">Environment variables.</span></span>
  * <span data-ttu-id="a2162-136">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="a2162-136">Command-line arguments.</span></span>
* <span data-ttu-id="a2162-137">Configura o [registro em log](xref:fundamentals/logging/index) para a saída do console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="a2162-137">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="a2162-138">O registro em log inclui regras de [filtragem de log](xref:fundamentals/logging/index#log-filtering) especificadas em uma seção de configuração de log de um *appsettings.json* ou *appSettings. { Arquivo de ambiente}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a2162-138">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="a2162-139">Ao executar por trás do IIS com o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o `CreateDefaultBuilder` habilita a [integração do IIS](xref:host-and-deploy/iis/index), que configura o endereço base e a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-139">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="a2162-140">A Integração do IIS também configura o aplicativo para [capturar erros de inicialização](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="a2162-140">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="a2162-141">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="a2162-141">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="a2162-142">Definirá [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` se o ambiente do aplicativo for de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a2162-142">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="a2162-143">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="a2162-143">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="a2162-144">A configuração definida por `CreateDefaultBuilder` pode ser substituída e aumentada por [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) e outros métodos, bem como os métodos de extensão de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="a2162-144">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="a2162-145">Veja a seguir alguns exemplos:</span><span class="sxs-lookup"><span data-stu-id="a2162-145">A few examples follow:</span></span>

* <span data-ttu-id="a2162-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) é usado para especificar `IConfiguration` adicionais para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="a2162-147">A seguinte chamada de `ConfigureAppConfiguration` adiciona um delegado para incluir a configuração do aplicativo no arquivo *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="a2162-147">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="a2162-148">`ConfigureAppConfiguration` pode ser chamado várias vezes.</span><span class="sxs-lookup"><span data-stu-id="a2162-148">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="a2162-149">Observe que essa configuração não se aplica ao host (por exemplo, URLs de servidor ou de ambiente).</span><span class="sxs-lookup"><span data-stu-id="a2162-149">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="a2162-150">Consulte a seção [Valores de configuração de Host](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="a2162-150">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="a2162-151">A seguinte chamada de `ConfigureLogging` adiciona um delegado para configurar o nível de log mínimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) como [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="a2162-151">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="a2162-152">Essa configuração substitui as configurações em *appsettings.Development.jsem* ( `LogLevel.Debug` ) e *appsettings.Production.jsno* ( `LogLevel.Error` ) configurado por `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a2162-152">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a2162-153">`ConfigureLogging` pode ser chamado várias vezes.</span><span class="sxs-lookup"><span data-stu-id="a2162-153">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="a2162-154">A seguinte chamada para `ConfigureKestrel` substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 milhões de bytes, estabelecido quando o Kestrel foi configurado pelo `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a2162-154">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="a2162-155">A seguinte chamada a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30.000.000 bytes estabelecido quando Kestrel foi configurado por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a2162-155">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="a2162-156">A [raiz do conteúdo](xref:fundamentals/index#content-root) determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="a2162-156">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="a2162-157">Quando o aplicativo é iniciado na pasta raiz do projeto, essa pasta é usada como a raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="a2162-157">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="a2162-158">Esse é o padrão usado no [Visual Studio](https://visualstudio.microsoft.com) e nos [novos modelos dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="a2162-158">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="a2162-159">Para obter mais informações sobre a configuração de aplicativo, veja <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a2162-159">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="a2162-160">Como uma alternativa ao uso do método `CreateDefaultBuilder` estático, criar um host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) é uma abordagem compatível com o ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="a2162-160">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="a2162-161">Ao configurar um host, os métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) podem ser fornecidos.</span><span class="sxs-lookup"><span data-stu-id="a2162-161">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="a2162-162">Se uma classe `Startup` for especificada, ela deverá definir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2162-162">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="a2162-163">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a2162-163">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="a2162-164">Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="a2162-164">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="a2162-165">Diversas chamadas para `Configure` ou `UseStartup` no `WebHostBuilder` substituem configurações anteriores.</span><span class="sxs-lookup"><span data-stu-id="a2162-165">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="a2162-166">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="a2162-166">Host configuration values</span></span>

<span data-ttu-id="a2162-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) conta com as seguintes abordagens para definir os valores de configuração do host:</span><span class="sxs-lookup"><span data-stu-id="a2162-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="a2162-168">Configuração do construtor do host, que inclui variáveis de ambiente com o formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="a2162-168">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="a2162-169">Por exemplo, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a2162-169">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="a2162-170">Extensões como [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) e [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (consulte a seção [Configuração de substituição](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="a2162-170">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="a2162-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) e a chave associada.</span><span class="sxs-lookup"><span data-stu-id="a2162-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="a2162-172">Ao definir um valor com `UseSetting`, o valor é definido como uma cadeia de caracteres, independentemente do tipo.</span><span class="sxs-lookup"><span data-stu-id="a2162-172">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="a2162-173">O host usa a opção que define um valor por último.</span><span class="sxs-lookup"><span data-stu-id="a2162-173">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="a2162-174">Para obter mais informações, veja [Substituir configuração](#override-configuration) na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="a2162-174">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="a2162-175">Chave do Aplicativo (Nome)</span><span class="sxs-lookup"><span data-stu-id="a2162-175">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a2162-176">A `IWebHostEnvironment.ApplicationName` propriedade é definida automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) é chamado durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="a2162-176">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="a2162-177">O valor é definido para o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-177">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="a2162-178">Para definir o valor explicitamente, use o [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="a2162-178">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a2162-179">A propriedade [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) é definida automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) é chamado durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="a2162-179">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="a2162-180">O valor é definido para o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-180">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="a2162-181">Para definir o valor explicitamente, use o [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="a2162-181">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="a2162-182">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="a2162-182">**Key**: applicationName</span></span>  
<span data-ttu-id="a2162-183">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="a2162-183">**Type**: *string*</span></span>  
<span data-ttu-id="a2162-184">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-184">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="a2162-185">**Definir usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a2162-185">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a2162-186">**Variável de ambiente**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="a2162-186">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="a2162-187">Capturar erros de inicialização</span><span class="sxs-lookup"><span data-stu-id="a2162-187">Capture Startup Errors</span></span>

<span data-ttu-id="a2162-188">Esta configuração controla a captura de erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="a2162-188">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="a2162-189">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="a2162-189">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="a2162-190">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="a2162-190">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a2162-191">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="a2162-191">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="a2162-192">**Definir usando**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="a2162-192">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="a2162-193">**Variável de ambiente**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="a2162-193">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="a2162-194">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="a2162-194">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="a2162-195">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="a2162-195">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="a2162-196">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="a2162-196">Content root</span></span>

<span data-ttu-id="a2162-197">Essa configuração determina onde ASP.NET Core começa a Pesquisar arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="a2162-197">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="a2162-198">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="a2162-198">**Key**: contentRoot</span></span>  
<span data-ttu-id="a2162-199">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="a2162-199">**Type**: *string*</span></span>  
<span data-ttu-id="a2162-200">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="a2162-200">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="a2162-201">**Definir usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="a2162-201">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="a2162-202">**Variável de ambiente**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="a2162-202">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="a2162-203">A raiz de conteúdo também é usada como o caminho base para a [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a2162-203">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="a2162-204">Se o caminho raiz do conteúdo não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="a2162-204">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="a2162-205">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="a2162-205">For more information, see:</span></span>

* [<span data-ttu-id="a2162-206">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="a2162-206">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="a2162-207">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="a2162-207">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="a2162-208">Erros detalhados</span><span class="sxs-lookup"><span data-stu-id="a2162-208">Detailed Errors</span></span>

<span data-ttu-id="a2162-209">Determina se erros detalhados devem ser capturados.</span><span class="sxs-lookup"><span data-stu-id="a2162-209">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="a2162-210">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="a2162-210">**Key**: detailedErrors</span></span>  
<span data-ttu-id="a2162-211">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="a2162-211">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a2162-212">**Padrão**: false</span><span class="sxs-lookup"><span data-stu-id="a2162-212">**Default**: false</span></span>  
<span data-ttu-id="a2162-213">**Definir usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a2162-213">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a2162-214">**Variável de ambiente**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="a2162-214">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="a2162-215">Quando habilitado (ou quando o <a href="#environment">Ambiente</a> é definido como `Development`), o aplicativo captura exceções detalhadas.</span><span class="sxs-lookup"><span data-stu-id="a2162-215">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="a2162-216">Ambiente</span><span class="sxs-lookup"><span data-stu-id="a2162-216">Environment</span></span>

<span data-ttu-id="a2162-217">Define o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-217">Sets the app's environment.</span></span>

<span data-ttu-id="a2162-218">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="a2162-218">**Key**: environment</span></span>  
<span data-ttu-id="a2162-219">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="a2162-219">**Type**: *string*</span></span>  
<span data-ttu-id="a2162-220">**Padrão**: Production</span><span class="sxs-lookup"><span data-stu-id="a2162-220">**Default**: Production</span></span>  
<span data-ttu-id="a2162-221">**Definir usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="a2162-221">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="a2162-222">**Variável de ambiente**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="a2162-222">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="a2162-223">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="a2162-223">The environment can be set to any value.</span></span> <span data-ttu-id="a2162-224">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="a2162-224">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="a2162-225">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="a2162-225">Values aren't case sensitive.</span></span> <span data-ttu-id="a2162-226">Por padrão, o *Ambiente* é lido da variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a2162-226">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="a2162-227">Ao usar o [Visual Studio](https://visualstudio.microsoft.com), variáveis de ambiente podem ser definidas no arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a2162-227">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="a2162-228">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a2162-228">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="a2162-229">Hospedando assemblies de inicialização</span><span class="sxs-lookup"><span data-stu-id="a2162-229">Hosting Startup Assemblies</span></span>

<span data-ttu-id="a2162-230">Define os assemblies de inicialização de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-230">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="a2162-231">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="a2162-231">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="a2162-232">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="a2162-232">**Type**: *string*</span></span>  
<span data-ttu-id="a2162-233">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="a2162-233">**Default**: Empty string</span></span>  
<span data-ttu-id="a2162-234">**Definir usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a2162-234">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a2162-235">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a2162-235">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="a2162-236">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a2162-236">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="a2162-237">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-237">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="a2162-238">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="a2162-238">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="a2162-239">Porta HTTPS</span><span class="sxs-lookup"><span data-stu-id="a2162-239">HTTPS Port</span></span>

<span data-ttu-id="a2162-240">Defina a porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a2162-240">Set the HTTPS redirect port.</span></span> <span data-ttu-id="a2162-241">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="a2162-241">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="a2162-242">**Chave**: https_port **Tipo**: *string*
**Padrão**: Um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="a2162-242">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="a2162-243">**Definir usando**: `UseSetting` 
 **variável de ambiente**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="a2162-243">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="a2162-244">Hospedando assemblies de exclusão de inicialização</span><span class="sxs-lookup"><span data-stu-id="a2162-244">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="a2162-245">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a2162-245">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="a2162-246">**Chave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="a2162-246">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="a2162-247">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="a2162-247">**Type**: *string*</span></span>  
<span data-ttu-id="a2162-248">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="a2162-248">**Default**: Empty string</span></span>  
<span data-ttu-id="a2162-249">**Definir usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a2162-249">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a2162-250">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a2162-250">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="a2162-251">Preferir URLs de hospedagem</span><span class="sxs-lookup"><span data-stu-id="a2162-251">Prefer Hosting URLs</span></span>

<span data-ttu-id="a2162-252">Indica se o host deve escutar as URLs configuradas com o `WebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.</span><span class="sxs-lookup"><span data-stu-id="a2162-252">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="a2162-253">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="a2162-253">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="a2162-254">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="a2162-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a2162-255">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="a2162-255">**Default**: true</span></span>  
<span data-ttu-id="a2162-256">**Definir usando**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="a2162-256">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="a2162-257">**Variável de ambiente**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="a2162-257">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="a2162-258">Impedir inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="a2162-258">Prevent Hosting Startup</span></span>

<span data-ttu-id="a2162-259">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-259">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="a2162-260">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a2162-260">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="a2162-261">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="a2162-261">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="a2162-262">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="a2162-262">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a2162-263">**Padrão**: false</span><span class="sxs-lookup"><span data-stu-id="a2162-263">**Default**: false</span></span>  
<span data-ttu-id="a2162-264">**Definir usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a2162-264">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a2162-265">**Variável de ambiente**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="a2162-265">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="a2162-266">URLs de servidor</span><span class="sxs-lookup"><span data-stu-id="a2162-266">Server URLs</span></span>

<span data-ttu-id="a2162-267">Indica os endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="a2162-267">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="a2162-268">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="a2162-268">**Key**: urls</span></span>  
<span data-ttu-id="a2162-269">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="a2162-269">**Type**: *string*</span></span>  
<span data-ttu-id="a2162-270">**Padrão**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="a2162-270">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="a2162-271">**Definir usando**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="a2162-271">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="a2162-272">**Variável de ambiente**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="a2162-272">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="a2162-273">Defina como uma lista separada por ponto e vírgula (;) de prefixos de URL aos quais o servidor deve responder.</span><span class="sxs-lookup"><span data-stu-id="a2162-273">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="a2162-274">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="a2162-274">For example, `http://localhost:123`.</span></span> <span data-ttu-id="a2162-275">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="a2162-275">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="a2162-276">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="a2162-276">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="a2162-277">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="a2162-277">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="a2162-278">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="a2162-278">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="a2162-279">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel/endpoints>.</span><span class="sxs-lookup"><span data-stu-id="a2162-279">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="a2162-280">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="a2162-280">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="a2162-281">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a2162-281">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>
::: moniker-end

### <a name="shutdown-timeout"></a><span data-ttu-id="a2162-282">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="a2162-282">Shutdown Timeout</span></span>

<span data-ttu-id="a2162-283">Especifica o tempo de espera para o desligamento do host da Web.</span><span class="sxs-lookup"><span data-stu-id="a2162-283">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="a2162-284">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="a2162-284">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="a2162-285">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="a2162-285">**Type**: *int*</span></span>  
<span data-ttu-id="a2162-286">**Padrão**: 5</span><span class="sxs-lookup"><span data-stu-id="a2162-286">**Default**: 5</span></span>  
<span data-ttu-id="a2162-287">**Definir usando**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="a2162-287">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="a2162-288">**Variável de ambiente**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="a2162-288">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="a2162-289">Embora a chave aceite um *int* com `UseSetting` (por exemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), o método de extensão [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) usa um [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="a2162-289">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="a2162-290">Durante o período de tempo limite, a hospedagem:</span><span class="sxs-lookup"><span data-stu-id="a2162-290">During the timeout period, hosting:</span></span>

* <span data-ttu-id="a2162-291">Dispara [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="a2162-291">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="a2162-292">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="a2162-292">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="a2162-293">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="a2162-293">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="a2162-294">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="a2162-294">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="a2162-295">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a2162-295">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="a2162-296">Assembly de inicialização</span><span class="sxs-lookup"><span data-stu-id="a2162-296">Startup Assembly</span></span>

<span data-ttu-id="a2162-297">Determina o assembly para pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="a2162-297">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="a2162-298">**Chave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="a2162-298">**Key**: startupAssembly</span></span>  
<span data-ttu-id="a2162-299">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="a2162-299">**Type**: *string*</span></span>  
<span data-ttu-id="a2162-300">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a2162-300">**Default**: The app's assembly</span></span>  
<span data-ttu-id="a2162-301">**Definir usando**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="a2162-301">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="a2162-302">**Variável de ambiente**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="a2162-302">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="a2162-303">O assembly por nome (`string`) ou por tipo (`TStartup`) pode ser referenciado.</span><span class="sxs-lookup"><span data-stu-id="a2162-303">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="a2162-304">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="a2162-304">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="a2162-305">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="a2162-305">Web root</span></span>

<span data-ttu-id="a2162-306">Define o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-306">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="a2162-307">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="a2162-307">**Key**: webroot</span></span>  
<span data-ttu-id="a2162-308">**Tipo**: *cadeia de caracteres*</span><span class="sxs-lookup"><span data-stu-id="a2162-308">**Type**: *string*</span></span>  
<span data-ttu-id="a2162-309">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="a2162-309">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="a2162-310">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="a2162-310">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="a2162-311">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="a2162-311">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="a2162-312">**Definir usando**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="a2162-312">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="a2162-313">**Variável de ambiente**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="a2162-313">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="a2162-314">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="a2162-314">For more information, see:</span></span>

* [<span data-ttu-id="a2162-315">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="a2162-315">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="a2162-316">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="a2162-316">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="a2162-317">Substituir configuração</span><span class="sxs-lookup"><span data-stu-id="a2162-317">Override configuration</span></span>

<span data-ttu-id="a2162-318">Use [Configuração](xref:fundamentals/configuration/index) para configurar o host da Web.</span><span class="sxs-lookup"><span data-stu-id="a2162-318">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="a2162-319">No exemplo a seguir, a configuração do host é especificada, opcionalmente, em um arquivo *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a2162-319">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="a2162-320">Qualquer configuração carregada do arquivo *hostsettings.json* pode ser substituída por argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="a2162-320">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="a2162-321">A configuração de build (no `config`) é usada para configurar o host com [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="a2162-321">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="a2162-322">A configuração `IWebHostBuilder` é adicionada à configuração do aplicativo, mas o contrário não é verdade&mdash;`ConfigureAppConfiguration` não afeta a configuração `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a2162-322">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="a2162-323">Substituição da configuração fornecida por `UseUrls` pela configuração de *hostsettings.json* primeiro, e pela configuração de argumento da linha de comando depois:</span><span class="sxs-lookup"><span data-stu-id="a2162-323">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="a2162-324">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a2162-324">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="a2162-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) copia apenas as chaves do fornecido `IConfiguration` para a configuração do host Builder.</span><span class="sxs-lookup"><span data-stu-id="a2162-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="a2162-326">Portanto, definir `reloadOnChange: true` para arquivos de configuração JSON, INI e XML não tem nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="a2162-326">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="a2162-327">Para especificar o host executado em uma URL específica, o valor desejado pode ser passado em um prompt de comando ao executar [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="a2162-327">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="a2162-328">O argumento de linha de comando substitui o valor `urls` do arquivo *hostsettings.json* e o servidor escuta na porta 8080:</span><span class="sxs-lookup"><span data-stu-id="a2162-328">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="a2162-329">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="a2162-329">Manage the host</span></span>

<span data-ttu-id="a2162-330">**Executar**</span><span class="sxs-lookup"><span data-stu-id="a2162-330">**Run**</span></span>

<span data-ttu-id="a2162-331">O método `Run` inicia o aplicativo Web e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="a2162-331">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="a2162-332">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="a2162-332">**Start**</span></span>

<span data-ttu-id="a2162-333">Execute o host sem bloqueio, chamando seu método `Start`:</span><span class="sxs-lookup"><span data-stu-id="a2162-333">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="a2162-334">Se uma lista de URLs for passada para o método `Start`, ele escutará nas URLs especificadas:</span><span class="sxs-lookup"><span data-stu-id="a2162-334">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="a2162-335">O aplicativo pode inicializar e iniciar um novo host usando os padrões pré-configurados de `CreateDefaultBuilder`, usando um método estático conveniente.</span><span class="sxs-lookup"><span data-stu-id="a2162-335">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="a2162-336">Esses métodos iniciam o servidor sem uma saída do console e com [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) e aguardam uma quebra (Ctrl-C/SIGINT ou SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="a2162-336">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="a2162-337">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="a2162-337">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="a2162-338">Inicie com um `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="a2162-338">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a2162-339">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="a2162-339">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="a2162-340">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="a2162-340">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a2162-341">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="a2162-341">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a2162-342">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="a2162-342">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="a2162-343">Inicie com uma URL e `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="a2162-343">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a2162-344">Produz o mesmo resultado que **Start(RequestDelegate app)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="a2162-344">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="a2162-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="a2162-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="a2162-346">Use uma instância de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar o middleware de roteamento:</span><span class="sxs-lookup"><span data-stu-id="a2162-346">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a2162-347">Use as seguintes solicitações de navegador com o exemplo:</span><span class="sxs-lookup"><span data-stu-id="a2162-347">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="a2162-348">Solicitação</span><span class="sxs-lookup"><span data-stu-id="a2162-348">Request</span></span>                                    | <span data-ttu-id="a2162-349">Resposta</span><span class="sxs-lookup"><span data-stu-id="a2162-349">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="a2162-350">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="a2162-350">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="a2162-351">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="a2162-351">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="a2162-352">Gera uma exceção com a cadeia de caracteres "ooops!"</span><span class="sxs-lookup"><span data-stu-id="a2162-352">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="a2162-353">Gera uma exceção com a cadeia de caracteres "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="a2162-353">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="a2162-354">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="a2162-354">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="a2162-355">Olá, Mundo!</span><span class="sxs-lookup"><span data-stu-id="a2162-355">Hello World!</span></span>                             |

<span data-ttu-id="a2162-356">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="a2162-356">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a2162-357">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="a2162-357">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a2162-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="a2162-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="a2162-359">Use uma URL e uma instância de `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a2162-359">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a2162-360">Produz o mesmo resultado que **Start(Action\<IRouteBuilder> routeBuilder)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="a2162-360">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="a2162-361">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="a2162-361">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="a2162-362">Forneça um delegado para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a2162-362">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a2162-363">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="a2162-363">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="a2162-364">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="a2162-364">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a2162-365">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="a2162-365">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a2162-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="a2162-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="a2162-367">Forneça um delegado e uma URL para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a2162-367">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a2162-368">Produz o mesmo resultado que **StartWith(Action\<IApplicationBuilder> app)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="a2162-368">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="a2162-369">Interface IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="a2162-369">IWebHostEnvironment interface</span></span>

<span data-ttu-id="a2162-370">A `IWebHostEnvironment` interface fornece informações sobre o ambiente de hospedagem na Web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-370">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="a2162-371">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IWebHostEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="a2162-371">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="a2162-372">Uma [abordagem baseada em convenção](xref:fundamentals/environments#environment-based-startup-class-and-methods) pode ser usada para configurar o aplicativo na inicialização com base no ambiente.</span><span class="sxs-lookup"><span data-stu-id="a2162-372">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="a2162-373">Como alternativa, injete o `IWebHostEnvironment` no construtor `Startup` para uso em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a2162-373">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="a2162-374">Além do método de extensão `IsDevelopment`, `IWebHostEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="a2162-374">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="a2162-375">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a2162-375">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a2162-376">O serviço `IWebHostEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:</span><span class="sxs-lookup"><span data-stu-id="a2162-376">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="a2162-377">`IWebHostEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="a2162-377">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="a2162-378">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="a2162-378">IHostingEnvironment interface</span></span>

<span data-ttu-id="a2162-379">A [interface IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fornece informações sobre o ambiente de hospedagem na Web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-379">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="a2162-380">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IHostingEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="a2162-380">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="a2162-381">Uma [abordagem baseada em convenção](xref:fundamentals/environments#environment-based-startup-class-and-methods) pode ser usada para configurar o aplicativo na inicialização com base no ambiente.</span><span class="sxs-lookup"><span data-stu-id="a2162-381">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="a2162-382">Como alternativa, injete o `IHostingEnvironment` no construtor `Startup` para uso em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a2162-382">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="a2162-383">Além do método de extensão `IsDevelopment`, `IHostingEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="a2162-383">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="a2162-384">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a2162-384">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a2162-385">O serviço `IHostingEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:</span><span class="sxs-lookup"><span data-stu-id="a2162-385">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="a2162-386">`IHostingEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="a2162-386">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="a2162-387">Interface IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a2162-387">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="a2162-388">`IHostApplicationLifetime` permite atividades de pós-inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="a2162-388">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="a2162-389">Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="a2162-389">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="a2162-390">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="a2162-390">Cancellation Token</span></span>    | <span data-ttu-id="a2162-391">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="a2162-391">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="a2162-392">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="a2162-392">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="a2162-393">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="a2162-393">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="a2162-394">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="a2162-394">All requests should be processed.</span></span> <span data-ttu-id="a2162-395">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="a2162-395">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="a2162-396">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="a2162-396">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="a2162-397">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="a2162-397">Requests may still be processing.</span></span> <span data-ttu-id="a2162-398">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="a2162-398">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="a2162-399">`StopApplication` solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-399">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="a2162-400">A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="a2162-400">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="a2162-401">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a2162-401">IApplicationLifetime interface</span></span>

<span data-ttu-id="a2162-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite atividades pós-inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="a2162-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="a2162-403">Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="a2162-403">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="a2162-404">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="a2162-404">Cancellation Token</span></span>    | <span data-ttu-id="a2162-405">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="a2162-405">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="a2162-406">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="a2162-406">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="a2162-407">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="a2162-407">The host has fully started.</span></span> |
| [<span data-ttu-id="a2162-408">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="a2162-408">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="a2162-409">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="a2162-409">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="a2162-410">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="a2162-410">All requests should be processed.</span></span> <span data-ttu-id="a2162-411">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="a2162-411">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="a2162-412">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="a2162-412">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="a2162-413">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="a2162-413">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="a2162-414">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="a2162-414">Requests may still be processing.</span></span> <span data-ttu-id="a2162-415">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="a2162-415">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="a2162-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2162-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="a2162-417">A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="a2162-417">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="a2162-418">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="a2162-418">Scope validation</span></span>

<span data-ttu-id="a2162-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) define [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` quando o ambiente do aplicativo é de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a2162-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="a2162-420">Quando `ValidateScopes` está definido como `true`, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="a2162-420">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="a2162-421">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="a2162-421">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="a2162-422">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="a2162-422">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="a2162-423">O provedor de serviços raiz é criado quando [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) é chamado.</span><span class="sxs-lookup"><span data-stu-id="a2162-423">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="a2162-424">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="a2162-424">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="a2162-425">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="a2162-425">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="a2162-426">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="a2162-426">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="a2162-427">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="a2162-427">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="a2162-428">Para que os escopos sempre sejam validados, incluindo no ambiente de produção, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) com [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) no construtor do host:</span><span class="sxs-lookup"><span data-stu-id="a2162-428">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="a2162-429">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a2162-429">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
