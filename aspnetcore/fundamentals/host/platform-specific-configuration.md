---
title: Usar assemblies de inicialização de hospedagem no ASP.NET Core
author: rick-anderson
description: Descubra como aprimorar um aplicativo ASP.NET Core por meio de um assembly externo usando uma implementação de IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 0636c62c4373533234ab252d64052b476b123bbf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405088"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="def86-103">Usar assemblies de inicialização de hospedagem no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="def86-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="def86-104">Por [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="def86-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="def86-105">Uma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementação (inicialização de hospedagem) adiciona aprimoramentos a um aplicativo na inicialização por meio de um assembly externo.</span><span class="sxs-lookup"><span data-stu-id="def86-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="def86-106">Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="def86-107">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="def86-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="def86-108">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="def86-108">HostingStartup attribute</span></span>

<span data-ttu-id="def86-109">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica a presença de um assembly de inicialização de hospedagem para ativar em runtime.</span><span class="sxs-lookup"><span data-stu-id="def86-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="def86-110">O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="def86-111">A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no runtime da configuração em [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="def86-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="def86-112">A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="def86-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="def86-113">No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="def86-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="def86-114">A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="def86-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="def86-115">O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="def86-116">Descobrir assemblies de inicialização de hospedagem carregados</span><span class="sxs-lookup"><span data-stu-id="def86-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="def86-117">Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="def86-118">Erros que ocorrem quando os assemblies carregados são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="def86-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="def86-119">Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.</span><span class="sxs-lookup"><span data-stu-id="def86-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="def86-120">Desabilitar o carregamento automático de assemblies de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="def86-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="def86-121">Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="def86-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="def86-122">Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:</span><span class="sxs-lookup"><span data-stu-id="def86-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="def86-123">Impedir a configuração do host de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="def86-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="def86-124">A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="def86-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="def86-125">Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:</span><span class="sxs-lookup"><span data-stu-id="def86-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="def86-126">Hospedando a definição de configuração de host de assemblies de inicialização:</span><span class="sxs-lookup"><span data-stu-id="def86-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="def86-127">A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="def86-128">Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.</span><span class="sxs-lookup"><span data-stu-id="def86-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="def86-129">A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="def86-130">Project</span><span class="sxs-lookup"><span data-stu-id="def86-130">Project</span></span>

<span data-ttu-id="def86-131">Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:</span><span class="sxs-lookup"><span data-stu-id="def86-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="def86-132">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="def86-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="def86-133">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="def86-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="def86-134">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="def86-134">Class library</span></span>

<span data-ttu-id="def86-135">Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="def86-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="def86-136">A biblioteca contém um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="def86-137">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um Razor aplicativo de páginas, *HostingStartupApp*e uma biblioteca de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="def86-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="def86-138">A biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="def86-138">The class library:</span></span>

* <span data-ttu-id="def86-139">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="def86-140">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="def86-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="def86-141">Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="def86-142">O `ServiceKeyInjection` método da classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="def86-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="def86-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="def86-144">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="def86-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="def86-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="def86-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="def86-146">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="def86-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="def86-147">O pacote tem as mesmas características da biblioteca de classes descrita anteriormente.</span><span class="sxs-lookup"><span data-stu-id="def86-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="def86-148">O pacote:</span><span class="sxs-lookup"><span data-stu-id="def86-148">The package:</span></span>

* <span data-ttu-id="def86-149">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="def86-150">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="def86-151">Inclui um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="def86-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="def86-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="def86-153">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:</span><span class="sxs-lookup"><span data-stu-id="def86-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="def86-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="def86-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="def86-155">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="def86-155">Console app without an entry point</span></span>

<span data-ttu-id="def86-156">*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="def86-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="def86-157">Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="def86-158">Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de runtime.</span><span class="sxs-lookup"><span data-stu-id="def86-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="def86-159">Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:</span><span class="sxs-lookup"><span data-stu-id="def86-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="def86-160">Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="def86-161">Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="def86-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="def86-162">Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de runtime](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o runtime compartilhado.</span><span class="sxs-lookup"><span data-stu-id="def86-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="def86-163">Na criação de uma inicialização de hospedagem dinâmica:</span><span class="sxs-lookup"><span data-stu-id="def86-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="def86-164">Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="def86-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="def86-165">Inclui uma classe que contém a implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="def86-166">Inclui um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar a classe de implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="def86-167">O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="def86-168">Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.</span><span class="sxs-lookup"><span data-stu-id="def86-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="def86-169">O arquivo de dependências é modificado para definir o local de runtime do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="def86-170">O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de runtime.</span><span class="sxs-lookup"><span data-stu-id="def86-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="def86-171">Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="def86-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="def86-172">O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="def86-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="def86-173">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica uma classe como uma implementação de `IHostingStartup` para carregar e executar ao compilar o <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="def86-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="def86-174">No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="def86-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="def86-175">Uma classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="def86-176">O método da classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="def86-177">`IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo runtime antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="def86-178">Ao criar um projeto `IHostingStartup`, o arquivo de dependências (*.deps.json*) define o local `runtime` do assembly como a pasta *bin*:</span><span class="sxs-lookup"><span data-stu-id="def86-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="def86-179">Apenas uma parte do arquivo é mostrada.</span><span class="sxs-lookup"><span data-stu-id="def86-179">Only part of the file is shown.</span></span> <span data-ttu-id="def86-180">O nome do assembly no exemplo é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="def86-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="def86-181">Configuração fornecida pela inicialização da hospedagem</span><span class="sxs-lookup"><span data-stu-id="def86-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="def86-182">Existem duas abordagens para lidar com a configuração, dependendo se você deseja que a configuração da inicialização da hospedagem tenha precedência ou que a configuração do aplicativo tenha precedência:</span><span class="sxs-lookup"><span data-stu-id="def86-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="def86-183">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para carregar a configuração depois que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo forem executados.</span><span class="sxs-lookup"><span data-stu-id="def86-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="def86-184">A configuração de inicialização de hospedagem tem prioridade sobre a configuração do aplicativo que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="def86-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="def86-185">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para carregar a configuração antes que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo sejam executados.</span><span class="sxs-lookup"><span data-stu-id="def86-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="def86-186">Os valores de configuração do aplicativo têm prioridade sobre aqueles fornecidos pela inicialização da hospedagem que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="def86-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="def86-187">Especificar o assembly de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="def86-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="def86-188">Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="def86-189">A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="def86-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="def86-190">Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="def86-191">Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:</span><span class="sxs-lookup"><span data-stu-id="def86-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="def86-192">Um assembly de inicialização de hospedagem também pode ser definido usando a configuração hospedando a configuração de host assemblies de inicialização:</span><span class="sxs-lookup"><span data-stu-id="def86-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="def86-193">Quando vários conjuntos de inicialização de hospedagem estão presentes, seus <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> métodos são executados na ordem em que os assemblies são listados.</span><span class="sxs-lookup"><span data-stu-id="def86-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="def86-194">Ativação</span><span class="sxs-lookup"><span data-stu-id="def86-194">Activation</span></span>

<span data-ttu-id="def86-195">As opções para ativação da inicialização de hospedagem são:</span><span class="sxs-lookup"><span data-stu-id="def86-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="def86-196">[Store](#runtime-store)em tempo de execução: a ativação não requer uma referência de tempo de compilação para ativação.</span><span class="sxs-lookup"><span data-stu-id="def86-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="def86-197">O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador.</span><span class="sxs-lookup"><span data-stu-id="def86-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="def86-198">A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="def86-199">Referência de tempo de compilação necessária para a ativação</span><span class="sxs-lookup"><span data-stu-id="def86-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="def86-200">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="def86-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="def86-201">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="def86-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="def86-202">Repositório de runtime</span><span class="sxs-lookup"><span data-stu-id="def86-202">Runtime store</span></span>

<span data-ttu-id="def86-203">A implementação de inicialização de hospedagem é colocada no [repositório de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="def86-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="def86-204">Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="def86-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="def86-205">Depois que a inicialização de hospedagem é compilada, um repositório de runtime é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="def86-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="def86-206">No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="def86-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="def86-207">Para o runtime descobrir o repositório de runtime, o local do repositório de runtime é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="def86-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="def86-208">**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="def86-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="def86-209">Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do runtime com `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="def86-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="def86-210">`additionalDeps` permite que você:</span><span class="sxs-lookup"><span data-stu-id="def86-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="def86-211">Amplie o grafo de biblioteca do aplicativo, fornecendo um conjunto de arquivos *.deps.json* adicionais a serem mesclados com o arquivo *.deps.json* próprio do aplicativo na inicialização.</span><span class="sxs-lookup"><span data-stu-id="def86-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="def86-212">Torne o assembly de inicialização de hospedagem detectável e carregável.</span><span class="sxs-lookup"><span data-stu-id="def86-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="def86-213">A abordagem recomendada para gerar o arquivo de dependências adicionais é:</span><span class="sxs-lookup"><span data-stu-id="def86-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="def86-214">Executar o `dotnet publish` no arquivo de manifesto do repositório de runtime mencionado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="def86-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="def86-215">Remova a referência de manifesto das bibliotecas e a `runtime` seção do *.deps.jsresultante no* arquivo.</span><span class="sxs-lookup"><span data-stu-id="def86-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="def86-216">No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:</span><span class="sxs-lookup"><span data-stu-id="def86-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="def86-217">Coloque o arquivo *.deps.json* no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="def86-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="def86-218">`{ADDITIONAL DEPENDENCIES PATH}`: Local adicionado à `DOTNET_ADDITIONAL_DEPS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="def86-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="def86-219">`{SHARED FRAMEWORK NAME}`: Estrutura compartilhada necessária para este arquivo de dependências adicional.</span><span class="sxs-lookup"><span data-stu-id="def86-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="def86-220">`{SHARED FRAMEWORK VERSION}`: Versão mínima da estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="def86-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="def86-221">`{ENHANCEMENT ASSEMBLY NAME}`: O nome do assembly do aprimoramento.</span><span class="sxs-lookup"><span data-stu-id="def86-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="def86-222">No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="def86-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="def86-223">Para o runtime descobrir o local do repositório de runtime, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="def86-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="def86-224">No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="def86-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="def86-225">Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="def86-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="def86-226">**Implantação**</span><span class="sxs-lookup"><span data-stu-id="def86-226">**Deployment**</span></span>

<span data-ttu-id="def86-227">Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:</span><span class="sxs-lookup"><span data-stu-id="def86-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="def86-228">O repositório de runtime de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="def86-229">O arquivo de dependências de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="def86-230">Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="def86-231">Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.</span><span class="sxs-lookup"><span data-stu-id="def86-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="def86-232">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="def86-232">NuGet package</span></span>

<span data-ttu-id="def86-233">Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="def86-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="def86-234">O pacote tem um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="def86-235">Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="def86-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="def86-236">O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="def86-237">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="def86-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="def86-238">Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="def86-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="def86-239">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="def86-240">Para obter mais informações sobre pacotes do NuGet e o repositório de runtime, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="def86-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="def86-241">Como criar um pacote do NuGet com ferramentas de plataforma cruzada</span><span class="sxs-lookup"><span data-stu-id="def86-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="def86-242">Publicando pacotes</span><span class="sxs-lookup"><span data-stu-id="def86-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="def86-243">Repositório de pacotes de runtime</span><span class="sxs-lookup"><span data-stu-id="def86-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="def86-244">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="def86-244">Project bin folder</span></span>

<span data-ttu-id="def86-245">Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="def86-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="def86-246">Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="def86-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="def86-247">O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="def86-248">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="def86-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="def86-249">Essa abordagem se aplica quando o cenário de implantação demanda fazer uma referência de tempo de compilação ao assembly da inicialização de hospedagem (arquivo *.dll*) e mover o assembly para:</span><span class="sxs-lookup"><span data-stu-id="def86-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="def86-250">o projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="def86-250">The consuming project.</span></span>
  * <span data-ttu-id="def86-251">um local acessível ao projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="def86-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="def86-252">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="def86-253">Ao ter como destino o .NET Framework, o assembly é carregável no contexto de carga padrão, o que, no .NET Framework, significa que ele fica em um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="def86-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="def86-254">Caminho base do aplicativo: a pasta *bin* onde o executável do aplicativo (*. exe*) está localizado.</span><span class="sxs-lookup"><span data-stu-id="def86-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="def86-255">GAC (cache de assembly global): o GAC armazena os assemblies que vários aplicativos .NET Framework compartilham.</span><span class="sxs-lookup"><span data-stu-id="def86-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="def86-256">Para obter mais informações, consulte [como: instalar um assembly no cache de assembly global](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) na documentação do .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="def86-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="def86-257">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="def86-257">Sample code</span></span>

<span data-ttu-id="def86-258">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="def86-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="def86-259">Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:</span><span class="sxs-lookup"><span data-stu-id="def86-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="def86-260">Pacote do NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="def86-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="def86-261">Biblioteca de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="def86-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="def86-262">Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="def86-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="def86-263">O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:</span><span class="sxs-lookup"><span data-stu-id="def86-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="def86-264">Serviços registrados</span><span class="sxs-lookup"><span data-stu-id="def86-264">Registered services</span></span>
  * <span data-ttu-id="def86-265">Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)</span><span class="sxs-lookup"><span data-stu-id="def86-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="def86-266">Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)</span><span class="sxs-lookup"><span data-stu-id="def86-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="def86-267">Cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="def86-267">Request headers</span></span>
  * <span data-ttu-id="def86-268">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="def86-268">Environment variables</span></span>

<span data-ttu-id="def86-269">Para executar o exemplo:</span><span class="sxs-lookup"><span data-stu-id="def86-269">To run the sample:</span></span>

<span data-ttu-id="def86-270">**Ativação de um pacote do NuGet**</span><span class="sxs-lookup"><span data-stu-id="def86-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="def86-271">Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="def86-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="def86-272">Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="def86-273">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-273">Compile and run the app.</span></span> <span data-ttu-id="def86-274">Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="def86-275">Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote (*../HostingStartupPackage/bin/Debug*) como uma origem de pacote.</span><span class="sxs-lookup"><span data-stu-id="def86-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="def86-276">Isso permite que o aplicativo use o pacote sem carregar o pacote em [NuGet.org](https://www.nuget.org/). Para obter mais informações, consulte as observações no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="def86-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="def86-277">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.</span><span class="sxs-lookup"><span data-stu-id="def86-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="def86-278">Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local.</span><span class="sxs-lookup"><span data-stu-id="def86-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="def86-279">Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="def86-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="def86-280">**Ativação de uma biblioteca de classes**</span><span class="sxs-lookup"><span data-stu-id="def86-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="def86-281">Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="def86-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="def86-282">Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="def86-283">A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="def86-284">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-284">Compile and run the app.</span></span> <span data-ttu-id="def86-285">Um `<ItemGroup>` no arquivo de projeto do aplicativo referencia o assembly da biblioteca de classes (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="def86-286">Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="def86-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="def86-287">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="def86-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="def86-288">**Ativação de um assembly implantado pelo repositório de runtime**</span><span class="sxs-lookup"><span data-stu-id="def86-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="def86-289">O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="def86-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="def86-290">O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="def86-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="def86-291">Para obter o PowerShell em outras plataformas, consulte [Instalando várias versões do PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="def86-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="def86-292">Execute o script *build.ps1* na pasta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="def86-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="def86-293">O script:</span><span class="sxs-lookup"><span data-stu-id="def86-293">The script:</span></span>
   * <span data-ttu-id="def86-294">Gera o `StartupDiagnostics` pacote na pasta *obj\packages*</span><span class="sxs-lookup"><span data-stu-id="def86-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="def86-295">Gera o repositório de runtime para `StartupDiagnostics` na pasta de *armazenamento*.</span><span class="sxs-lookup"><span data-stu-id="def86-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="def86-296">O comando `dotnet store` no script usa o `win7-x64` [RID (identificador de runtime)](/dotnet/core/rid-catalog) para uma inicialização de host implantada no Windows.</span><span class="sxs-lookup"><span data-stu-id="def86-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="def86-297">Ao fornecer a inicialização de hospedagem para um runtime diferente, substitua pelo RID correto na linha 37 do script.</span><span class="sxs-lookup"><span data-stu-id="def86-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="def86-298">O armazenamento em tempo de execução para `StartupDiagnostics` posteriormente seria movido para o armazenamento de tempo de execução do usuário ou do sistema no computador em que o assembly será consumido.</span><span class="sxs-lookup"><span data-stu-id="def86-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="def86-299">O local de instalação do armazenamento em tempo de execução do usuário para o `StartupDiagnostics` assembly é *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="def86-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="def86-300">Gera o `additionalDeps` para `StartupDiagnostics` na pasta *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="def86-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="def86-301">As dependências adicionais posteriormente seriam movidas para as dependências adicionais do usuário ou do sistema.</span><span class="sxs-lookup"><span data-stu-id="def86-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="def86-302">O `StartupDiagnostics` local de instalação das dependências adicionais do usuário é *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NetCore. app/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="def86-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="def86-303">Coloca o arquivo *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="def86-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="def86-304">Execute o script *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="def86-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="def86-305">O script acrescenta:</span><span class="sxs-lookup"><span data-stu-id="def86-305">The script appends:</span></span>
   * <span data-ttu-id="def86-306">`StartupDiagnostics` à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="def86-307">O caminho de dependências de inicialização de hospedagem (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_ADDITIONAL_DEPS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="def86-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="def86-308">O caminho de armazenamento em tempo de execução (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_SHARED_STORE` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="def86-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="def86-309">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="def86-309">Run the sample app.</span></span>
1. <span data-ttu-id="def86-310">Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="def86-311">Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="def86-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="def86-312">Uma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementação (inicialização de hospedagem) adiciona aprimoramentos a um aplicativo na inicialização por meio de um assembly externo.</span><span class="sxs-lookup"><span data-stu-id="def86-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="def86-313">Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="def86-314">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="def86-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="def86-315">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="def86-315">HostingStartup attribute</span></span>

<span data-ttu-id="def86-316">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica a presença de um assembly de inicialização de hospedagem para ativar em runtime.</span><span class="sxs-lookup"><span data-stu-id="def86-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="def86-317">O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="def86-318">A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no runtime da configuração em [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="def86-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="def86-319">A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="def86-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="def86-320">Para obter mais informações, consulte [Host da Web: assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies) e [Host da Web: assemblies de exclusão da inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="def86-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="def86-321">No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="def86-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="def86-322">A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="def86-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="def86-323">O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="def86-324">Descobrir assemblies de inicialização de hospedagem carregados</span><span class="sxs-lookup"><span data-stu-id="def86-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="def86-325">Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="def86-326">Erros que ocorrem quando os assemblies carregados são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="def86-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="def86-327">Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.</span><span class="sxs-lookup"><span data-stu-id="def86-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="def86-328">Desabilitar o carregamento automático de assemblies de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="def86-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="def86-329">Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="def86-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="def86-330">Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:</span><span class="sxs-lookup"><span data-stu-id="def86-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="def86-331">Configuração do host [Impedir inicialização de hospedagem](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="def86-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="def86-332">A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="def86-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="def86-333">Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:</span><span class="sxs-lookup"><span data-stu-id="def86-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="def86-334">Configuração do host [Assemblies de exclusão de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="def86-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="def86-335">A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="def86-336">Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.</span><span class="sxs-lookup"><span data-stu-id="def86-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="def86-337">A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="def86-338">Project</span><span class="sxs-lookup"><span data-stu-id="def86-338">Project</span></span>

<span data-ttu-id="def86-339">Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:</span><span class="sxs-lookup"><span data-stu-id="def86-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="def86-340">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="def86-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="def86-341">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="def86-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="def86-342">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="def86-342">Class library</span></span>

<span data-ttu-id="def86-343">Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="def86-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="def86-344">A biblioteca contém um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="def86-345">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um Razor aplicativo de páginas, *HostingStartupApp*e uma biblioteca de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="def86-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="def86-346">A biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="def86-346">The class library:</span></span>

* <span data-ttu-id="def86-347">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="def86-348">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="def86-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="def86-349">Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="def86-350">O `ServiceKeyInjection` método da classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="def86-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="def86-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="def86-352">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="def86-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="def86-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="def86-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="def86-354">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="def86-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="def86-355">O pacote tem as mesmas características da biblioteca de classes descrita anteriormente.</span><span class="sxs-lookup"><span data-stu-id="def86-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="def86-356">O pacote:</span><span class="sxs-lookup"><span data-stu-id="def86-356">The package:</span></span>

* <span data-ttu-id="def86-357">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="def86-358">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="def86-359">Inclui um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="def86-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="def86-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="def86-361">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:</span><span class="sxs-lookup"><span data-stu-id="def86-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="def86-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="def86-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="def86-363">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="def86-363">Console app without an entry point</span></span>

<span data-ttu-id="def86-364">*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="def86-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="def86-365">Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="def86-366">Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de runtime.</span><span class="sxs-lookup"><span data-stu-id="def86-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="def86-367">Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:</span><span class="sxs-lookup"><span data-stu-id="def86-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="def86-368">Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="def86-369">Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="def86-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="def86-370">Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de runtime](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o runtime compartilhado.</span><span class="sxs-lookup"><span data-stu-id="def86-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="def86-371">Na criação de uma inicialização de hospedagem dinâmica:</span><span class="sxs-lookup"><span data-stu-id="def86-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="def86-372">Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="def86-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="def86-373">Inclui uma classe que contém a implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="def86-374">Inclui um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar a classe de implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="def86-375">O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="def86-376">Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.</span><span class="sxs-lookup"><span data-stu-id="def86-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="def86-377">O arquivo de dependências é modificado para definir o local de runtime do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="def86-378">O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de runtime.</span><span class="sxs-lookup"><span data-stu-id="def86-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="def86-379">Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="def86-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="def86-380">O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="def86-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="def86-381">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica uma classe como uma implementação de `IHostingStartup` para carregar e executar ao compilar o <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="def86-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="def86-382">No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="def86-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="def86-383">Uma classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="def86-384">O método da classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="def86-385">`IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo runtime antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="def86-386">Ao criar um projeto `IHostingStartup`, o arquivo de dependências (*.deps.json*) define o local `runtime` do assembly como a pasta *bin*:</span><span class="sxs-lookup"><span data-stu-id="def86-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="def86-387">Apenas uma parte do arquivo é mostrada.</span><span class="sxs-lookup"><span data-stu-id="def86-387">Only part of the file is shown.</span></span> <span data-ttu-id="def86-388">O nome do assembly no exemplo é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="def86-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="def86-389">Configuração fornecida pela inicialização da hospedagem</span><span class="sxs-lookup"><span data-stu-id="def86-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="def86-390">Existem duas abordagens para lidar com a configuração, dependendo se você deseja que a configuração da inicialização da hospedagem tenha precedência ou que a configuração do aplicativo tenha precedência:</span><span class="sxs-lookup"><span data-stu-id="def86-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="def86-391">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para carregar a configuração depois que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo forem executados.</span><span class="sxs-lookup"><span data-stu-id="def86-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="def86-392">A configuração de inicialização de hospedagem tem prioridade sobre a configuração do aplicativo que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="def86-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="def86-393">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para carregar a configuração antes que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo sejam executados.</span><span class="sxs-lookup"><span data-stu-id="def86-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="def86-394">Os valores de configuração do aplicativo têm prioridade sobre aqueles fornecidos pela inicialização da hospedagem que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="def86-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="def86-395">Especificar o assembly de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="def86-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="def86-396">Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="def86-397">A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="def86-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="def86-398">Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="def86-399">Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:</span><span class="sxs-lookup"><span data-stu-id="def86-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="def86-400">Um assembly de inicialização de hospedagem também pode ser definido usando a configuração do host [Assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="def86-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="def86-401">Quando vários conjuntos de inicialização de hospedagem estão presentes, seus <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> métodos são executados na ordem em que os assemblies são listados.</span><span class="sxs-lookup"><span data-stu-id="def86-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="def86-402">Ativação</span><span class="sxs-lookup"><span data-stu-id="def86-402">Activation</span></span>

<span data-ttu-id="def86-403">As opções para ativação da inicialização de hospedagem são:</span><span class="sxs-lookup"><span data-stu-id="def86-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="def86-404">[Store](#runtime-store)em tempo de execução: a ativação não requer uma referência de tempo de compilação para ativação.</span><span class="sxs-lookup"><span data-stu-id="def86-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="def86-405">O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador.</span><span class="sxs-lookup"><span data-stu-id="def86-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="def86-406">A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="def86-407">Referência de tempo de compilação necessária para a ativação</span><span class="sxs-lookup"><span data-stu-id="def86-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="def86-408">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="def86-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="def86-409">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="def86-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="def86-410">Repositório de runtime</span><span class="sxs-lookup"><span data-stu-id="def86-410">Runtime store</span></span>

<span data-ttu-id="def86-411">A implementação de inicialização de hospedagem é colocada no [repositório de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="def86-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="def86-412">Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="def86-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="def86-413">Depois que a inicialização de hospedagem é compilada, um repositório de runtime é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="def86-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="def86-414">No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="def86-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="def86-415">Para o runtime descobrir o repositório de runtime, o local do repositório de runtime é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="def86-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="def86-416">**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="def86-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="def86-417">Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do runtime com `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="def86-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="def86-418">`additionalDeps` permite que você:</span><span class="sxs-lookup"><span data-stu-id="def86-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="def86-419">Amplie o grafo de biblioteca do aplicativo, fornecendo um conjunto de arquivos *.deps.json* adicionais a serem mesclados com o arquivo *.deps.json* próprio do aplicativo na inicialização.</span><span class="sxs-lookup"><span data-stu-id="def86-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="def86-420">Torne o assembly de inicialização de hospedagem detectável e carregável.</span><span class="sxs-lookup"><span data-stu-id="def86-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="def86-421">A abordagem recomendada para gerar o arquivo de dependências adicionais é:</span><span class="sxs-lookup"><span data-stu-id="def86-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="def86-422">Executar o `dotnet publish` no arquivo de manifesto do repositório de runtime mencionado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="def86-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="def86-423">Remova a referência de manifesto das bibliotecas e a `runtime` seção do *.deps.jsresultante no* arquivo.</span><span class="sxs-lookup"><span data-stu-id="def86-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="def86-424">No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:</span><span class="sxs-lookup"><span data-stu-id="def86-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="def86-425">Coloque o arquivo *.deps.json* no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="def86-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="def86-426">`{ADDITIONAL DEPENDENCIES PATH}`: Local adicionado à `DOTNET_ADDITIONAL_DEPS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="def86-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="def86-427">`{SHARED FRAMEWORK NAME}`: Estrutura compartilhada necessária para este arquivo de dependências adicional.</span><span class="sxs-lookup"><span data-stu-id="def86-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="def86-428">`{SHARED FRAMEWORK VERSION}`: Versão mínima da estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="def86-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="def86-429">`{ENHANCEMENT ASSEMBLY NAME}`: O nome do assembly do aprimoramento.</span><span class="sxs-lookup"><span data-stu-id="def86-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="def86-430">No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="def86-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="def86-431">Para o runtime descobrir o local do repositório de runtime, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="def86-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="def86-432">No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="def86-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="def86-433">Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="def86-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="def86-434">**Implantação**</span><span class="sxs-lookup"><span data-stu-id="def86-434">**Deployment**</span></span>

<span data-ttu-id="def86-435">Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:</span><span class="sxs-lookup"><span data-stu-id="def86-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="def86-436">O repositório de runtime de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="def86-437">O arquivo de dependências de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="def86-438">Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="def86-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="def86-439">Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.</span><span class="sxs-lookup"><span data-stu-id="def86-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="def86-440">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="def86-440">NuGet package</span></span>

<span data-ttu-id="def86-441">Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="def86-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="def86-442">O pacote tem um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="def86-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="def86-443">Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="def86-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="def86-444">O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="def86-445">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="def86-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="def86-446">Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="def86-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="def86-447">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="def86-448">Para obter mais informações sobre pacotes do NuGet e o repositório de runtime, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="def86-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="def86-449">Como criar um pacote do NuGet com ferramentas de plataforma cruzada</span><span class="sxs-lookup"><span data-stu-id="def86-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="def86-450">Publicando pacotes</span><span class="sxs-lookup"><span data-stu-id="def86-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="def86-451">Repositório de pacotes de runtime</span><span class="sxs-lookup"><span data-stu-id="def86-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="def86-452">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="def86-452">Project bin folder</span></span>

<span data-ttu-id="def86-453">Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="def86-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="def86-454">Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="def86-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="def86-455">O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="def86-456">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="def86-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="def86-457">Essa abordagem se aplica quando o cenário de implantação demanda fazer uma referência de tempo de compilação ao assembly da inicialização de hospedagem (arquivo *.dll*) e mover o assembly para:</span><span class="sxs-lookup"><span data-stu-id="def86-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="def86-458">o projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="def86-458">The consuming project.</span></span>
  * <span data-ttu-id="def86-459">um local acessível ao projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="def86-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="def86-460">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="def86-461">Ao ter como destino o .NET Framework, o assembly é carregável no contexto de carga padrão, o que, no .NET Framework, significa que ele fica em um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="def86-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="def86-462">Caminho base do aplicativo: a pasta *bin* onde o executável do aplicativo (*. exe*) está localizado.</span><span class="sxs-lookup"><span data-stu-id="def86-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="def86-463">GAC (cache de assembly global): o GAC armazena os assemblies que vários aplicativos .NET Framework compartilham.</span><span class="sxs-lookup"><span data-stu-id="def86-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="def86-464">Para obter mais informações, consulte [como: instalar um assembly no cache de assembly global](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) na documentação do .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="def86-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="def86-465">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="def86-465">Sample code</span></span>

<span data-ttu-id="def86-466">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="def86-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="def86-467">Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:</span><span class="sxs-lookup"><span data-stu-id="def86-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="def86-468">Pacote do NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="def86-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="def86-469">Biblioteca de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="def86-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="def86-470">Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="def86-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="def86-471">O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:</span><span class="sxs-lookup"><span data-stu-id="def86-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="def86-472">Serviços registrados</span><span class="sxs-lookup"><span data-stu-id="def86-472">Registered services</span></span>
  * <span data-ttu-id="def86-473">Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)</span><span class="sxs-lookup"><span data-stu-id="def86-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="def86-474">Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)</span><span class="sxs-lookup"><span data-stu-id="def86-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="def86-475">Cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="def86-475">Request headers</span></span>
  * <span data-ttu-id="def86-476">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="def86-476">Environment variables</span></span>

<span data-ttu-id="def86-477">Para executar o exemplo:</span><span class="sxs-lookup"><span data-stu-id="def86-477">To run the sample:</span></span>

<span data-ttu-id="def86-478">**Ativação de um pacote do NuGet**</span><span class="sxs-lookup"><span data-stu-id="def86-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="def86-479">Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="def86-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="def86-480">Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="def86-481">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-481">Compile and run the app.</span></span> <span data-ttu-id="def86-482">Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="def86-483">Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote (*../HostingStartupPackage/bin/Debug*) como uma origem de pacote.</span><span class="sxs-lookup"><span data-stu-id="def86-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="def86-484">Isso permite que o aplicativo use o pacote sem carregar o pacote em [NuGet.org](https://www.nuget.org/). Para obter mais informações, consulte as observações no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="def86-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="def86-485">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.</span><span class="sxs-lookup"><span data-stu-id="def86-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="def86-486">Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local.</span><span class="sxs-lookup"><span data-stu-id="def86-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="def86-487">Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="def86-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="def86-488">**Ativação de uma biblioteca de classes**</span><span class="sxs-lookup"><span data-stu-id="def86-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="def86-489">Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="def86-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="def86-490">Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="def86-491">A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="def86-492">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-492">Compile and run the app.</span></span> <span data-ttu-id="def86-493">Um `<ItemGroup>` do arquivo de projeto do aplicativo referencia o assembly da biblioteca de classes (*.\lixeira\Depurar\netcoreapp2.1\HostingStartupLibrary.dll*) (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="def86-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="def86-494">Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="def86-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="def86-495">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="def86-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="def86-496">**Ativação de um assembly implantado pelo repositório de runtime**</span><span class="sxs-lookup"><span data-stu-id="def86-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="def86-497">O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="def86-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="def86-498">O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="def86-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="def86-499">Para obter o PowerShell em outras plataformas, consulte [Instalando várias versões do PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="def86-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="def86-500">Execute o script *build.ps1* na pasta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="def86-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="def86-501">O script:</span><span class="sxs-lookup"><span data-stu-id="def86-501">The script:</span></span>
   * <span data-ttu-id="def86-502">Gera o `StartupDiagnostics` pacote na pasta *obj\packages*</span><span class="sxs-lookup"><span data-stu-id="def86-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="def86-503">Gera o repositório de runtime para `StartupDiagnostics` na pasta de *armazenamento*.</span><span class="sxs-lookup"><span data-stu-id="def86-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="def86-504">O comando `dotnet store` no script usa o `win7-x64` [RID (identificador de runtime)](/dotnet/core/rid-catalog) para uma inicialização de host implantada no Windows.</span><span class="sxs-lookup"><span data-stu-id="def86-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="def86-505">Ao fornecer a inicialização de hospedagem para um runtime diferente, substitua pelo RID correto na linha 37 do script.</span><span class="sxs-lookup"><span data-stu-id="def86-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="def86-506">O armazenamento em tempo de execução para `StartupDiagnostics` posteriormente seria movido para o armazenamento de tempo de execução do usuário ou do sistema no computador em que o assembly será consumido.</span><span class="sxs-lookup"><span data-stu-id="def86-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="def86-507">O local de instalação do armazenamento em tempo de execução do usuário para o `StartupDiagnostics` assembly é *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="def86-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="def86-508">Gera o `additionalDeps` para `StartupDiagnostics` na pasta *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="def86-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="def86-509">As dependências adicionais posteriormente seriam movidas para as dependências adicionais do usuário ou do sistema.</span><span class="sxs-lookup"><span data-stu-id="def86-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="def86-510">O `StartupDiagnostics` local de instalação das dependências adicionais do usuário é *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NetCore. app/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="def86-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="def86-511">Coloca o arquivo *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="def86-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="def86-512">Execute o script *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="def86-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="def86-513">O script acrescenta:</span><span class="sxs-lookup"><span data-stu-id="def86-513">The script appends:</span></span>
   * <span data-ttu-id="def86-514">`StartupDiagnostics` à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="def86-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="def86-515">O caminho de dependências de inicialização de hospedagem (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_ADDITIONAL_DEPS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="def86-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="def86-516">O caminho de armazenamento em tempo de execução (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_SHARED_STORE` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="def86-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="def86-517">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="def86-517">Run the sample app.</span></span>
1. <span data-ttu-id="def86-518">Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="def86-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="def86-519">Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="def86-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
