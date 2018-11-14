---
title: Host Genérico .NET
author: guardrex
description: Saiba mais sobre o Host Genérico no .NET, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2018
uid: fundamentals/host/generic-host
ms.openlocfilehash: 9943c9dd2d6dd67a79186ee880b181a5915d06be
ms.sourcegitcommit: edb9d2d78c9a4d68b397e74ae2aff088b325a143
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51505707"
---
# <a name="net-generic-host"></a><span data-ttu-id="b5979-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="b5979-103">.NET Generic Host</span></span>

<span data-ttu-id="b5979-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b5979-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b5979-105">Os aplicativos .NET Core configuram e iniciam um *host*.</span><span class="sxs-lookup"><span data-stu-id="b5979-105">.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="b5979-106">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-106">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b5979-107">Este tópico aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é útil para a hospedagem de aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b5979-107">This topic covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is useful for hosting apps that don't process HTTP requests.</span></span> <span data-ttu-id="b5979-108">Para cobertura sobre o host da Web (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>), veja <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="b5979-108">For coverage of the Web Host (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>), see <xref:fundamentals/host/web-host>.</span></span>

<span data-ttu-id="b5979-109">O objetivo do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="b5979-109">The goal of the Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="b5979-110">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos abrangentes, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="b5979-110">Messaging, background tasks, and other non-HTTP workloads based on the Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="b5979-111">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="b5979-111">The Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="b5979-112">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="b5979-112">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="b5979-113">O Host Genérico está em desenvolvimento para substituir o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="b5979-113">The Generic Host is under development to replace the Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="b5979-114">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5979-114">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b5979-115">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="b5979-115">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="b5979-116">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="b5979-116">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="b5979-117">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="b5979-117">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="b5979-118">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="b5979-118">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="b5979-119">Na configuração **Inicialização do .NET Core (console)**, localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="b5979-119">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="b5979-120">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="b5979-120">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="b5979-121">Introdução</span><span class="sxs-lookup"><span data-stu-id="b5979-121">Introduction</span></span>

<span data-ttu-id="b5979-122">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="b5979-122">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="b5979-123">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="b5979-123">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="b5979-124"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="b5979-124"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="b5979-125">Cada implementação do `IHostedService` é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="b5979-125">Each `IHostedService` implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="b5979-126"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada `IHostedService` quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="b5979-126"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each `IHostedService` when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="b5979-127">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="b5979-127">Set up a host</span></span>

<span data-ttu-id="b5979-128"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="b5979-128"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="default-services"></a><span data-ttu-id="b5979-129">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="b5979-129">Default services</span></span>

<span data-ttu-id="b5979-130">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="b5979-130">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="b5979-131">[Ambiente](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="b5979-131">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="b5979-132">[Configuração](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="b5979-132">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="b5979-133"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="b5979-133"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="b5979-134"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="b5979-134"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="b5979-135">[Opções](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="b5979-135">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="b5979-136">[Registro em log](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="b5979-136">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="b5979-137">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="b5979-137">Host configuration</span></span>

<span data-ttu-id="b5979-138">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="b5979-138">Host configuration is created by:</span></span>

* <span data-ttu-id="b5979-139">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="b5979-139">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="b5979-140">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b5979-140">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="b5979-141">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="b5979-141">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="b5979-142">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="b5979-142">Application key (name)</span></span>

<span data-ttu-id="b5979-143">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="b5979-143">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="b5979-144">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="b5979-144">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="b5979-145">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="b5979-145">**Key**: applicationName</span></span>  
<span data-ttu-id="b5979-146">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="b5979-146">**Type**: *string*</span></span>  
<span data-ttu-id="b5979-147">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-147">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="b5979-148">**Definido usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="b5979-148">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="b5979-149">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` é [opcional e definida pelo usuário](#configuration-builder))</span><span class="sxs-lookup"><span data-stu-id="b5979-149">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configuration-builder))</span></span>

### <a name="content-root"></a><span data-ttu-id="b5979-150">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="b5979-150">Content root</span></span>

<span data-ttu-id="b5979-151">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="b5979-151">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="b5979-152">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="b5979-152">**Key**: contentRoot</span></span>  
<span data-ttu-id="b5979-153">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="b5979-153">**Type**: *string*</span></span>  
<span data-ttu-id="b5979-154">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="b5979-154">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="b5979-155">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="b5979-155">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="b5979-156">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configuration-builder))</span><span class="sxs-lookup"><span data-stu-id="b5979-156">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configuration-builder))</span></span>

<span data-ttu-id="b5979-157">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="b5979-157">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="b5979-158">Ambiente</span><span class="sxs-lookup"><span data-stu-id="b5979-158">Environment</span></span>

<span data-ttu-id="b5979-159">Define o [ambiente](xref:fundamentals/environments) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-159">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b5979-160">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="b5979-160">**Key**: environment</span></span>  
<span data-ttu-id="b5979-161">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="b5979-161">**Type**: *string*</span></span>  
<span data-ttu-id="b5979-162">**Padrão**: Production</span><span class="sxs-lookup"><span data-stu-id="b5979-162">**Default**: Production</span></span>  
<span data-ttu-id="b5979-163">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="b5979-163">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="b5979-164">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configuration-builder))</span><span class="sxs-lookup"><span data-stu-id="b5979-164">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configuration-builder))</span></span>

<span data-ttu-id="b5979-165">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="b5979-165">The environment can be set to any value.</span></span> <span data-ttu-id="b5979-166">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="b5979-166">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="b5979-167">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b5979-167">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="b5979-168">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="b5979-168">ConfigureHostConfiguration</span></span>

<span data-ttu-id="b5979-169">`ConfigureHostConfiguration` usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="b5979-169">`ConfigureHostConfiguration` uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="b5979-170">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-170">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="b5979-171">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="b5979-171">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b5979-172">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="b5979-172">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="b5979-173">A configuração do host flui automaticamente para a configuração do aplicativo ([ConfigureAppConfiguration](#configureappconfiguration) e o restante do aplicativo).</span><span class="sxs-lookup"><span data-stu-id="b5979-173">Host configuration automatically flows to app configuration ([ConfigureAppConfiguration](#configureappconfiguration) and the rest of the app).</span></span>

<span data-ttu-id="b5979-174">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="b5979-174">No providers are included by default.</span></span> <span data-ttu-id="b5979-175">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em `ConfigureHostConfiguration`, incluindo:</span><span class="sxs-lookup"><span data-stu-id="b5979-175">You must explicitly specify whatever configuration providers the app requires in `ConfigureHostConfiguration`, including:</span></span>

* <span data-ttu-id="b5979-176">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="b5979-176">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="b5979-177">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="b5979-177">Environment variable configuration.</span></span>
* <span data-ttu-id="b5979-178">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="b5979-178">Command-line argument configuration.</span></span>
* <span data-ttu-id="b5979-179">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="b5979-179">Any other required configuration providers.</span></span>

<span data-ttu-id="b5979-180">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5979-180">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="b5979-181">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b5979-181">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="b5979-182">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="b5979-182">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="b5979-183">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="b5979-183">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="b5979-184">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="b5979-184">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="b5979-185">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="b5979-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="b5979-186">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="b5979-186">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="b5979-187">Durante o desenvolvimento, ao usar o [Visual Studio](https://www.visualstudio.com/) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b5979-187">During development when using [Visual Studio](https://www.visualstudio.com/) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="b5979-188">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b5979-188">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="b5979-189">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b5979-189">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b5979-190">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="b5979-190">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="b5979-191">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="b5979-191">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="b5979-192">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b5979-192">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="b5979-193">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="b5979-193">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="b5979-194">Exemplo da configuração `HostBuilder` usando `ConfigureHostConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="b5979-194">Example `HostBuilder` configuration using `ConfigureHostConfiguration`:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="b5979-195">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b5979-195">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b5979-196">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b5979-196">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="b5979-197">`ConfigureAppConfiguration` usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-197">`ConfigureAppConfiguration` uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="b5979-198">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="b5979-198">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b5979-199">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="b5979-199">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="b5979-200">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="b5979-200">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="b5979-201">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="b5979-201">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="b5979-202">Exemplo da configuração de aplicativo usando `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="b5979-202">Example app configuration using `ConfigureAppConfiguration`:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="b5979-203">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b5979-203">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="b5979-204">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="b5979-204">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="b5979-205">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="b5979-205">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="b5979-206">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="b5979-206">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="b5979-207">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="b5979-207">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

## <a name="configureservices"></a><span data-ttu-id="b5979-208">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="b5979-208">ConfigureServices</span></span>

<span data-ttu-id="b5979-209"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-209"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b5979-210">`ConfigureServices` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="b5979-210">`ConfigureServices` can be called multiple times with additive results.</span></span>

<span data-ttu-id="b5979-211">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="b5979-211">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="b5979-212">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="b5979-212">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="b5979-213">O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b5979-213">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="b5979-214">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="b5979-214">ConfigureLogging</span></span>

<span data-ttu-id="b5979-215"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="b5979-215"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="b5979-216">`ConfigureLogging` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="b5979-216">`ConfigureLogging` may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="b5979-217">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="b5979-217">UseConsoleLifetime</span></span>

<span data-ttu-id="b5979-218"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escuta `Ctrl+C`/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="b5979-218"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for `Ctrl+C`/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="b5979-219">`UseConsoleLifetime` desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="b5979-219">`UseConsoleLifetime` unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="b5979-220"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="b5979-220"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="b5979-221">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="b5979-221">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="b5979-222">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="b5979-222">Container configuration</span></span>

<span data-ttu-id="b5979-223">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory`1>.</span><span class="sxs-lookup"><span data-stu-id="b5979-223">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory`1>.</span></span> <span data-ttu-id="b5979-224">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="b5979-224">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="b5979-225">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-225">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="b5979-226">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="b5979-226">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="b5979-227">`ConfigureContainer` fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="b5979-227">`ConfigureContainer` provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="b5979-228">`ConfigureContainer` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="b5979-228">`ConfigureContainer` can be called multiple times with additive results.</span></span>

<span data-ttu-id="b5979-229">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b5979-229">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="b5979-230">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="b5979-230">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="b5979-231">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b5979-231">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="b5979-232">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="b5979-232">Extensibility</span></span>

<span data-ttu-id="b5979-233">Extensibilidade de host é executada com métodos de extensão em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b5979-233">Host extensibility is performed with extension methods on `IHostBuilder`.</span></span> <span data-ttu-id="b5979-234">O exemplo a seguir mostra como um método de extensão estende uma implementação do `IHostBuilder` com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="b5979-234">The following example shows how an extension method extends an `IHostBuilder` implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="b5979-235">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="b5979-235">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="b5979-236">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="b5979-236">Manage the host</span></span>

<span data-ttu-id="b5979-237">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações `IHostedService` que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b5979-237">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the `IHostedService` implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="b5979-238">Executar</span><span class="sxs-lookup"><span data-stu-id="b5979-238">Run</span></span>

<span data-ttu-id="b5979-239"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="b5979-239"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="b5979-240">RunAsync</span><span class="sxs-lookup"><span data-stu-id="b5979-240">RunAsync</span></span>

<span data-ttu-id="b5979-241"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um `Task` que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="b5979-241"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a `Task` that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="b5979-242">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="b5979-242">RunConsoleAsync</span></span>

<span data-ttu-id="b5979-243"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte do console, compila e inicia o host e aguarda `Ctrl+C`/SIGINT ou SIGTERM desligar.</span><span class="sxs-lookup"><span data-stu-id="b5979-243"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for `Ctrl+C`/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="b5979-244">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="b5979-244">Start and StopAsync</span></span>

<span data-ttu-id="b5979-245"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="b5979-245"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="b5979-246"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="b5979-246"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="b5979-247">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="b5979-247">StartAsync and StopAsync</span></span>

<span data-ttu-id="b5979-248"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-248"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="b5979-249"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-249"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="b5979-250">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="b5979-250">WaitForShutdown</span></span>

<span data-ttu-id="b5979-251"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> é disparado por meio de <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (escuta `Ctrl+C`/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="b5979-251"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for `Ctrl+C`/SIGINT or SIGTERM).</span></span> <span data-ttu-id="b5979-252">`WaitForShutdown` chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b5979-252">`WaitForShutdown` calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="b5979-253">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="b5979-253">WaitForShutdownAsync</span></span>

<span data-ttu-id="b5979-254"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um `Task` que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b5979-254"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a `Task` that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="b5979-255">Controle externo</span><span class="sxs-lookup"><span data-stu-id="b5979-255">External control</span></span>

<span data-ttu-id="b5979-256">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="b5979-256">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="b5979-257"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="b5979-257"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="b5979-258">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="b5979-258">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="b5979-259">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="b5979-259">IHostingEnvironment interface</span></span>

<span data-ttu-id="b5979-260"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-260"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="b5979-261">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IHostingEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="b5979-261">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="b5979-262">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b5979-262">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="b5979-263">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b5979-263">IApplicationLifetime interface</span></span>

<span data-ttu-id="b5979-264"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="b5979-264"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="b5979-265">Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="b5979-265">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="b5979-266">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="b5979-266">Cancellation Token</span></span> | <span data-ttu-id="b5979-267">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="b5979-267">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="b5979-268">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="b5979-268">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="b5979-269">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="b5979-269">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="b5979-270">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="b5979-270">All requests should be processed.</span></span> <span data-ttu-id="b5979-271">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="b5979-271">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="b5979-272">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="b5979-272">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="b5979-273">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="b5979-273">Requests may still be processing.</span></span> <span data-ttu-id="b5979-274">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="b5979-274">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="b5979-275">O construtor injeta o serviço `IApplicationLifetime` em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="b5979-275">Constructor-inject the `IApplicationLifetime` service into any class.</span></span> <span data-ttu-id="b5979-276">O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação `IHostedService`) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="b5979-276">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an `IHostedService` implementation) to register the events.</span></span>

<span data-ttu-id="b5979-277">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5979-277">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="b5979-278"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5979-278"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="b5979-279">A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="b5979-279">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b5979-280">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b5979-280">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
* [<span data-ttu-id="b5979-281">Hospedagem de exemplos do repositório no GitHub</span><span class="sxs-lookup"><span data-stu-id="b5979-281">Hosting repo samples on GitHub</span></span>](https://github.com/aspnet/Hosting/tree/release/2.1/samples)
