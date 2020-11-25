---
title: Configuração no ASP.NET Core
author: rick-anderson
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 97ee00dd37ed4eef1c013e0f45b598a79f3f260c
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035860"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="93bdc-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93bdc-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="93bdc-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="93bdc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="93bdc-105">A configuração no ASP.NET Core é executada usando um ou mais [provedores de configuração](#cp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="93bdc-106">Os provedores de configuração lêem dados de configuração de pares chave-valor usando uma variedade de fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="93bdc-107">Arquivos de configurações, como *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="93bdc-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="93bdc-108">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-108">Environment variables</span></span>
* <span data-ttu-id="93bdc-109">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-109">Azure Key Vault</span></span>
* <span data-ttu-id="93bdc-110">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-110">Azure App Configuration</span></span>
* <span data-ttu-id="93bdc-111">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-111">Command-line arguments</span></span>
* <span data-ttu-id="93bdc-112">Provedores personalizados, instalados ou criados</span><span class="sxs-lookup"><span data-stu-id="93bdc-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="93bdc-113">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="93bdc-113">Directory files</span></span>
* <span data-ttu-id="93bdc-114">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="93bdc-114">In-memory .NET objects</span></span>

<span data-ttu-id="93bdc-115">Este tópico fornece informações sobre a configuração no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="93bdc-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="93bdc-116">Para obter informações sobre como usar a configuração em aplicativos de console, consulte [configuração do .net](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="93bdc-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="93bdc-117">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="93bdc-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="93bdc-118">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="93bdc-118">Default configuration</span></span>

<span data-ttu-id="93bdc-119">ASP.NET Core aplicativos Web criados com [dotnet novo](/dotnet/core/tools/dotnet-new) ou o Visual Studio geram o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="93bdc-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="93bdc-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="93bdc-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="93bdc-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Adiciona um existente `IConfiguration` como uma origem.</span><span class="sxs-lookup"><span data-stu-id="93bdc-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="93bdc-122">No caso de configuração padrão, o adiciona a configuração do [host](#hvac) e a define como a primeira origem para a configuração do _aplicativo_ .</span><span class="sxs-lookup"><span data-stu-id="93bdc-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="93bdc-123">[appsettings.json](#appsettingsjson) usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="93bdc-124">*appSettings.* `Environment` *. JSON* usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="93bdc-125">Por exemplo, *appSettings*. ***Produção \* \* _._json* e *appSettings*. \* \* \* desenvolvimento** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="93bdc-126">[Segredos do aplicativo](xref:security/app-secrets) quando o aplicativo é executado no `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="93bdc-127">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="93bdc-128">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="93bdc-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="93bdc-129">Os provedores de configuração adicionados posteriormente substituem as configurações de chave anteriores.</span><span class="sxs-lookup"><span data-stu-id="93bdc-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="93bdc-130">Por exemplo, se `MyKey` for definido no *appsettings.json* e no ambiente, o valor do ambiente será usado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="93bdc-131">Usando os provedores de configuração padrão, o  [provedor de configuração de linha de comando](#clcp) substitui todos os outros provedores.</span><span class="sxs-lookup"><span data-stu-id="93bdc-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="93bdc-132">Para obter mais informações sobre o `CreateDefaultBuilder` , consulte [configurações padrão do Construtor](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="93bdc-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="93bdc-133">O código a seguir exibe os provedores de configuração habilitados na ordem em que foram adicionados:</span><span class="sxs-lookup"><span data-stu-id="93bdc-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="93bdc-134">Considere o seguinte *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="93bdc-135">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-136">O padrão <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="93bdc-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="93bdc-137">*appSettings.* `Environment` *. JSON* : por exemplo, *appSettings*. ***Produção \* \* _._json* e *appSettings*. \* \* \* arquivos de desenvolvimento** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="93bdc-138">A versão de ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="93bdc-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="93bdc-139">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="93bdc-140">*appSettings*. `Environment` . valores *JSON* substituem chaves em *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="93bdc-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="93bdc-141">Por exemplo, por padrão:</span><span class="sxs-lookup"><span data-stu-id="93bdc-141">For example, by default:</span></span>

* <span data-ttu-id="93bdc-142">Em desenvolvimento, *appSettings*. \***desenvolvimento** _._json \* a configuração substitui os valores encontrados em *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="93bdc-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="93bdc-143">Em produção, *appSettings*. \* _._Json de **produção**\* a configuração substitui os valores encontrados em *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="93bdc-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="93bdc-144">Por exemplo, ao implantar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="93bdc-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="93bdc-145">Associar dados de configuração hierárquica usando o padrão de opções</span><span class="sxs-lookup"><span data-stu-id="93bdc-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="93bdc-146">Usando a configuração [padrão](#default) , o *appsettings.json* e *appSettings.* `Environment` os arquivos *. JSON* são habilitados com [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="93bdc-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="93bdc-147">As alterações feitas no *appsettings.json* e *appSettings.* `Environment` o arquivo *. JSON* \***após** _, o aplicativo iniciado é lido pelo [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \***after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="93bdc-148">Consulte [provedor de configuração JSON](#jcp) neste documento para obter informações sobre como adicionar arquivos de configuração JSON adicionais.</span><span class="sxs-lookup"><span data-stu-id="93bdc-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="93bdc-149">Combinando coleção de serviços</span><span class="sxs-lookup"><span data-stu-id="93bdc-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="93bdc-150">Segurança e segredos do usuário</span><span class="sxs-lookup"><span data-stu-id="93bdc-150">Security and user secrets</span></span>

<span data-ttu-id="93bdc-151">Diretrizes de dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-151">Configuration data guidelines:</span></span>

<span data-ttu-id="93bdc-152">_ Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou em arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="93bdc-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="93bdc-153">A ferramenta [Gerenciador de segredo](xref:security/app-secrets) pode ser usada para armazenar segredos no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="93bdc-154">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="93bdc-155">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="93bdc-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="93bdc-156">Por [padrão](#default), a fonte de configuração de segredos do usuário é registrada após as fontes de configuração do JSON.</span><span class="sxs-lookup"><span data-stu-id="93bdc-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="93bdc-157">Portanto, as chaves de segredos do usuário têm precedência sobre as chaves em *appsettings.json* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="93bdc-158">Para obter mais informações sobre como armazenar senhas ou outros dados confidenciais:</span><span class="sxs-lookup"><span data-stu-id="93bdc-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="93bdc-159"><xref:security/app-secrets>: Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="93bdc-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="93bdc-160">A ferramenta Gerenciador de segredo usa o [provedor de configuração de arquivo](#fcp) para armazenar segredos de usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="93bdc-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="93bdc-161">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="93bdc-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="93bdc-162">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="93bdc-163">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-163">Environment variables</span></span>

<span data-ttu-id="93bdc-164">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração de pares chave-valor da variável de ambiente após a leitura *appsettings.json* , *appSettings.* `Environment` *. JSON* e [segredos do usuário](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="93bdc-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="93bdc-165">Portanto, os valores de chave lidos dos valores de substituição de ambiente lidos de *appsettings.json* , *appSettings.* `Environment` *. JSON* e segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="93bdc-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="93bdc-166">Os seguintes `set` comandos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-166">The following `set` commands:</span></span>

* <span data-ttu-id="93bdc-167">Defina as chaves de ambiente e os valores do [exemplo anterior](#appsettingsjson) no Windows.</span><span class="sxs-lookup"><span data-stu-id="93bdc-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="93bdc-168">Teste as configurações ao usar o [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="93bdc-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="93bdc-169">O `dotnet run` comando deve ser executado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="93bdc-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="93bdc-170">As configurações de ambiente anteriores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-170">The preceding environment settings:</span></span>

* <span data-ttu-id="93bdc-171">São definidos apenas em processos iniciados na janela de comando em que foram definidos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="93bdc-172">Não serão lidos por navegadores iniciados com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="93bdc-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="93bdc-173">Os comandos [setx](/windows-server/administration/windows-commands/setx) a seguir podem ser usados para definir as chaves de ambiente e os valores no Windows.</span><span class="sxs-lookup"><span data-stu-id="93bdc-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="93bdc-174">Ao contrário `set` de, `setx` as configurações são persistidas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="93bdc-175">`/M` define a variável no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="93bdc-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="93bdc-176">Se a `/M` opção não for usada, uma variável de ambiente do usuário será definida.</span><span class="sxs-lookup"><span data-stu-id="93bdc-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="93bdc-177">Para testar se os comandos anteriores substituem *appsettings.json* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="93bdc-178">Com o Visual Studio: saia e reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="93bdc-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="93bdc-179">Com a CLI: iniciar uma nova janela de comando e Enter `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="93bdc-180">Chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> com uma cadeia de caracteres para especificar um prefixo para variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="93bdc-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="93bdc-181">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="93bdc-181">In the preceding code:</span></span>

* <span data-ttu-id="93bdc-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="93bdc-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="93bdc-183">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="93bdc-184">Variáveis de ambiente definidas com o `MyCustomPrefix_` prefixo substituem os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="93bdc-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="93bdc-185">Isso inclui variáveis de ambiente sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="93bdc-186">O prefixo é eliminado quando os pares chave-valor de configuração são lidos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="93bdc-187">Os comandos a seguir testam o prefixo personalizado:</span><span class="sxs-lookup"><span data-stu-id="93bdc-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="93bdc-188">A [configuração padrão](#default) carrega variáveis de ambiente e argumentos de linha de comando prefixados com `DOTNET_` and `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="93bdc-189">Os `DOTNET_` `ASPNETCORE_` prefixos e são usados por ASP.NET Core para [configuração de host e aplicativo](xref:fundamentals/host/generic-host#host-configuration), mas não para a configuração do usuário.</span><span class="sxs-lookup"><span data-stu-id="93bdc-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="93bdc-190">Para obter mais informações sobre a configuração de host e aplicativo, consulte [host genérico .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="93bdc-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="93bdc-191">Em [Azure app serviço](https://azure.microsoft.com/services/app-service/), selecione **nova configuração de aplicativo** na página **configurações > configuração** .</span><span class="sxs-lookup"><span data-stu-id="93bdc-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="93bdc-192">Azure App configurações do aplicativo de serviço são:</span><span class="sxs-lookup"><span data-stu-id="93bdc-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="93bdc-193">Criptografado em repouso e transmitido por um canal criptografado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="93bdc-194">Exposto como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-194">Exposed as environment variables.</span></span>

<span data-ttu-id="93bdc-195">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="93bdc-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="93bdc-196">Consulte [prefixos de cadeia de conexão](#constr) para obter informações sobre cadeias de conexão do banco de dados</span><span class="sxs-lookup"><span data-stu-id="93bdc-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="93bdc-197">Nomenclatura de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-197">Naming of environment variables</span></span>

<span data-ttu-id="93bdc-198">Os nomes de variáveis de ambiente refletem a estrutura de um *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="93bdc-199">Cada elemento na hierarquia é separado por um sublinhado duplo (preferível) ou dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="93bdc-200">Quando a estrutura do elemento inclui uma matriz, o índice da matriz deve ser tratado como um nome de elemento adicional nesse caminho.</span><span class="sxs-lookup"><span data-stu-id="93bdc-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="93bdc-201">Considere o seguinte *appsettings.json* arquivo e seus valores equivalentes representados como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="93bdc-202">**variáveis de ambiente**</span><span class="sxs-lookup"><span data-stu-id="93bdc-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="93bdc-203">Variáveis de ambiente definidas no launchSettings.jsem</span><span class="sxs-lookup"><span data-stu-id="93bdc-203">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="93bdc-204">As variáveis de ambiente definidas no *launchSettings.js* substituir aquelas definidas no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="93bdc-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="93bdc-205">Linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-205">Command-line</span></span>

<span data-ttu-id="93bdc-206">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração de pares de chave-valor de argumento de linha de comando após as seguintes fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-206">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="93bdc-207">*appsettings.json* e *appSettings*. `Environment` . arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="93bdc-207">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="93bdc-208">[Segredos do aplicativo](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-208">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="93bdc-209">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-209">Environment variables.</span></span>

<span data-ttu-id="93bdc-210">Por [padrão](#default), os valores de configuração definidos nos valores de configuração de substituição de linha de comando são definidos com todos os outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-210">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="93bdc-211">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-211">Command-line arguments</span></span>

<span data-ttu-id="93bdc-212">O comando a seguir define chaves e valores usando `=` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-212">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="93bdc-213">O comando a seguir define chaves e valores usando `/` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-213">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="93bdc-214">O comando a seguir define chaves e valores usando `--` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-214">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="93bdc-215">O valor da chave:</span><span class="sxs-lookup"><span data-stu-id="93bdc-215">The key value:</span></span>

* <span data-ttu-id="93bdc-216">Deve seguir `=` , ou a chave deve ter um prefixo `--` ou `/` quando o valor segue um espaço.</span><span class="sxs-lookup"><span data-stu-id="93bdc-216">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="93bdc-217">Não será necessário se `=` for usado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-217">Isn't required if `=` is used.</span></span> <span data-ttu-id="93bdc-218">Por exemplo, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-218">For example, `MySetting=`.</span></span>

<span data-ttu-id="93bdc-219">No mesmo comando, não misture pares de chave-valor de argumento de linha de comando que usam `=` com pares de chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="93bdc-219">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="93bdc-220">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="93bdc-220">Switch mappings</span></span>

<span data-ttu-id="93bdc-221">Os mapeamentos de switch permitem a lógica de substituição de nome de **chave** .</span><span class="sxs-lookup"><span data-stu-id="93bdc-221">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="93bdc-222">Forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="93bdc-222">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="93bdc-223">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="93bdc-223">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="93bdc-224">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-224">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="93bdc-225">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="93bdc-225">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="93bdc-226">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="93bdc-226">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="93bdc-227">Os comutadores devem começar com `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-227">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="93bdc-228">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-228">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="93bdc-229">Para usar um dicionário de mapeamentos de opção, passe-o para a chamada para `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-229">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="93bdc-230">O código a seguir mostra os valores de chave para as chaves substituídas:</span><span class="sxs-lookup"><span data-stu-id="93bdc-230">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-231">O comando a seguir funciona para testar a substituição da chave:</span><span class="sxs-lookup"><span data-stu-id="93bdc-231">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="93bdc-232">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-232">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="93bdc-233">A `CreateDefaultBuilder` chamada do método `AddCommandLine` não inclui opções mapeadas e não há como passar o dicionário de mapeamento de opção para `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-233">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="93bdc-234">A solução não é passar os argumentos para `CreateDefaultBuilder` , mas sim permitir que o `ConfigurationBuilder` método do método `AddCommandLine` processe os argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="93bdc-234">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="93bdc-235">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="93bdc-235">Hierarchical configuration data</span></span>

<span data-ttu-id="93bdc-236">A API de configuração lê dados de configuração hierárquicas mesclando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-236">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="93bdc-237">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte  *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-237">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="93bdc-238">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações:</span><span class="sxs-lookup"><span data-stu-id="93bdc-238">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-239">A maneira preferida de ler dados de configuração hierárquicos é usar o padrão de opções.</span><span class="sxs-lookup"><span data-stu-id="93bdc-239">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="93bdc-240">Para obter mais informações, consulte [associar dados de configuração hierárquica](#optpat) neste documento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-240">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="93bdc-241">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-241"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="93bdc-242">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="93bdc-242">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="93bdc-243">Chaves de configuração e valores</span><span class="sxs-lookup"><span data-stu-id="93bdc-243">Configuration keys and values</span></span>

<span data-ttu-id="93bdc-244">Chaves de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-244">Configuration keys:</span></span>

* <span data-ttu-id="93bdc-245">Não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-245">Are case-insensitive.</span></span> <span data-ttu-id="93bdc-246">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="93bdc-246">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="93bdc-247">Se uma chave e um valor for definido em mais de um provedor de configuração, o valor do último provedor adicionado será usado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-247">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="93bdc-248">Para obter mais informações, consulte [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="93bdc-248">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="93bdc-249">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="93bdc-249">Hierarchical keys</span></span>
  * <span data-ttu-id="93bdc-250">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-250">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="93bdc-251">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-251">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="93bdc-252">Um sublinhado duplo, `__` , tem suporte em todas as plataformas e é automaticamente convertido em dois-pontos `:` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-252">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="93bdc-253">Em Azure Key Vault, as chaves hierárquicas usam `--` como um separador.</span><span class="sxs-lookup"><span data-stu-id="93bdc-253">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="93bdc-254">O [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration) substitui automaticamente `--` por um `:` quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-254">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="93bdc-255">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-255">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="93bdc-256">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="93bdc-256">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="93bdc-257">Valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-257">Configuration values:</span></span>

* <span data-ttu-id="93bdc-258">São cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="93bdc-258">Are strings.</span></span>
* <span data-ttu-id="93bdc-259">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-259">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="93bdc-260">Provedores de configuração</span><span class="sxs-lookup"><span data-stu-id="93bdc-260">Configuration providers</span></span>

<span data-ttu-id="93bdc-261">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="93bdc-261">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="93bdc-262">Provedor</span><span class="sxs-lookup"><span data-stu-id="93bdc-262">Provider</span></span> | <span data-ttu-id="93bdc-263">Fornece a configuração de </span><span class="sxs-lookup"><span data-stu-id="93bdc-263">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="93bdc-264">Provedor de configuração de Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="93bdc-264">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="93bdc-265">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-265">Azure Key Vault</span></span> |
| [<span data-ttu-id="93bdc-266">Provedor de configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="93bdc-266">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="93bdc-267">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-267">Azure App Configuration</span></span> |
| [<span data-ttu-id="93bdc-268">Provedor de configuração de linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-268">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="93bdc-269">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-269">Command-line parameters</span></span> |
| [<span data-ttu-id="93bdc-270">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="93bdc-270">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="93bdc-271">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="93bdc-271">Custom source</span></span> |
| [<span data-ttu-id="93bdc-272">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-272">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="93bdc-273">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-273">Environment variables</span></span> |
| [<span data-ttu-id="93bdc-274">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="93bdc-274">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="93bdc-275">Arquivos INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="93bdc-275">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="93bdc-276">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="93bdc-276">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="93bdc-277">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="93bdc-277">Directory files</span></span> |
| [<span data-ttu-id="93bdc-278">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="93bdc-278">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="93bdc-279">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="93bdc-279">In-memory collections</span></span> |
| [<span data-ttu-id="93bdc-280">Segredos do usuário</span><span class="sxs-lookup"><span data-stu-id="93bdc-280">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="93bdc-281">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="93bdc-281">File in the user profile directory</span></span> |

<span data-ttu-id="93bdc-282">As fontes de configuração são lidas na ordem em que seus provedores de configuração são especificados.</span><span class="sxs-lookup"><span data-stu-id="93bdc-282">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="93bdc-283">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="93bdc-283">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="93bdc-284">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="93bdc-284">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="93bdc-285">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="93bdc-285">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="93bdc-286">Segredos do usuário</span><span class="sxs-lookup"><span data-stu-id="93bdc-286">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="93bdc-287">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-287">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="93bdc-288">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-288">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="93bdc-289">Uma prática comum é adicionar o provedor de configuração de linha de comando pela última vez em uma série de provedores para permitir que argumentos de linha de comando substituam a configuração definida pelos outros provedores.</span><span class="sxs-lookup"><span data-stu-id="93bdc-289">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="93bdc-290">A sequência anterior de provedores é usada na [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="93bdc-290">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="93bdc-291">Prefixos de cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="93bdc-291">Connection string prefixes</span></span>

<span data-ttu-id="93bdc-292">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="93bdc-292">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="93bdc-293">Essas cadeias de conexão estão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-293">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="93bdc-294">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo com a [configuração padrão](#default) ou quando nenhum prefixo é fornecido para `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-294">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="93bdc-295">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="93bdc-295">Connection string prefix</span></span> | <span data-ttu-id="93bdc-296">Provedor</span><span class="sxs-lookup"><span data-stu-id="93bdc-296">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="93bdc-297">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="93bdc-297">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="93bdc-298">MySQL</span><span class="sxs-lookup"><span data-stu-id="93bdc-298">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="93bdc-299">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-299">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="93bdc-300">SQL Server</span><span class="sxs-lookup"><span data-stu-id="93bdc-300">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="93bdc-301">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="93bdc-301">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="93bdc-302">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="93bdc-302">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="93bdc-303">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="93bdc-303">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="93bdc-304">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-304">Environment variable key</span></span> | <span data-ttu-id="93bdc-305">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="93bdc-305">Converted configuration key</span></span> | <span data-ttu-id="93bdc-306">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="93bdc-306">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="93bdc-307">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="93bdc-307">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="93bdc-308">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="93bdc-309">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="93bdc-309">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="93bdc-310">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-310">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="93bdc-311">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="93bdc-311">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="93bdc-312">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-312">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="93bdc-313">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="93bdc-313">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="93bdc-314">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="93bdc-314">File configuration provider</span></span>

<span data-ttu-id="93bdc-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="93bdc-316">Os seguintes provedores de configuração derivam de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-316">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="93bdc-317">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="93bdc-317">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="93bdc-318">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="93bdc-318">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="93bdc-319">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="93bdc-319">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="93bdc-320">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="93bdc-320">INI configuration provider</span></span>

<span data-ttu-id="93bdc-321">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="93bdc-321">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="93bdc-322">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="93bdc-323">No código anterior, as configurações no *MyIniConfig.ini* e  *MyIniConfig*. `Environment` . os arquivos *ini* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="93bdc-323">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="93bdc-324">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-324">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="93bdc-325">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-325">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="93bdc-326">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo de *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="93bdc-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="93bdc-327">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="93bdc-328">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="93bdc-328">JSON configuration provider</span></span>

<span data-ttu-id="93bdc-329">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor de arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="93bdc-329">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="93bdc-330">Sobrecargas podem especificar:</span><span class="sxs-lookup"><span data-stu-id="93bdc-330">Overloads can specify:</span></span>

* <span data-ttu-id="93bdc-331">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="93bdc-331">Whether the file is optional.</span></span>
* <span data-ttu-id="93bdc-332">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-332">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="93bdc-333">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="93bdc-333">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="93bdc-334">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="93bdc-334">The preceding code:</span></span>

* <span data-ttu-id="93bdc-335">Configura o provedor de configuração JSON para carregar o *MyConfig.jsno* arquivo com as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="93bdc-335">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="93bdc-336">`optional: true`: O arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="93bdc-336">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="93bdc-337">`reloadOnChange: true` : O arquivo é recarregado quando as alterações são salvas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-337">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="93bdc-338">Lê os [provedores de configuração padrão](#default) antes da *MyConfig.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-338">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="93bdc-339">As configurações no *MyConfig.jsna* configuração de substituição de arquivo nos provedores de configuração padrão, incluindo o [provedor de configuração de variáveis de ambiente](#evcp) e o provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-339">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="93bdc-340">Normalmente, você \***não** precisa de um arquivo JSON personalizado que substitua valores definidos no [provedor de configuração de variáveis de ambiente](#evcp) e no provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-340">You typically \***don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="93bdc-341">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-341">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="93bdc-342">No código anterior, as configurações na _MyConfig.jsem \* e  *myconfig*. `Environment` . arquivos *JSON* :</span><span class="sxs-lookup"><span data-stu-id="93bdc-342">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="93bdc-343">Substituir as configurações no *appsettings.json* e *appSettings*. `Environment` . arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="93bdc-343">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="93bdc-344">São substituídos por configurações no [provedor de configuração de variáveis de ambiente](#evcp) e no [provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-344">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="93bdc-345">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém os seguintes  *MyConfig.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-345">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="93bdc-346">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-346">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="93bdc-347">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="93bdc-347">XML configuration provider</span></span>

<span data-ttu-id="93bdc-348">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="93bdc-348">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="93bdc-349">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="93bdc-350">No código anterior, as configurações no *MyXMLFile.xml* e  *MyXMLFile*. `Environment` . os arquivos *XML* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="93bdc-350">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="93bdc-351">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="93bdc-352">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="93bdc-353">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo de *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="93bdc-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="93bdc-354">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-355">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-355">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="93bdc-356">O código a seguir lê o arquivo de configuração anterior e exibe as chaves e valores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-356">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-357">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-357">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="93bdc-358">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-358">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="93bdc-359">key:attribute</span><span class="sxs-lookup"><span data-stu-id="93bdc-359">key:attribute</span></span>
* <span data-ttu-id="93bdc-360">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="93bdc-360">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="93bdc-361">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="93bdc-361">Key-per-file configuration provider</span></span>

<span data-ttu-id="93bdc-362">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-362">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="93bdc-363">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-363">The key is the file name.</span></span> <span data-ttu-id="93bdc-364">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-364">The value contains the file's contents.</span></span> <span data-ttu-id="93bdc-365">O provedor de configuração de chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="93bdc-365">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="93bdc-366">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-366">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="93bdc-367">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="93bdc-367">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="93bdc-368">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="93bdc-368">Overloads permit specifying:</span></span>

* <span data-ttu-id="93bdc-369">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="93bdc-369">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="93bdc-370">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="93bdc-370">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="93bdc-371">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-371">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="93bdc-372">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-372">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="93bdc-373">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-373">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="93bdc-374">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="93bdc-374">Memory configuration provider</span></span>

<span data-ttu-id="93bdc-375">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-375">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="93bdc-376">O código a seguir adiciona uma coleção de memória ao sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-376">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="93bdc-377">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe as configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-377">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-378">No código anterior, `config.AddInMemoryCollection(Dict)` é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="93bdc-378">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="93bdc-379">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="93bdc-379">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="93bdc-380">Consulte [associar uma matriz](#boa) para outro exemplo usando `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-380">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="93bdc-381">GetValue</span><span class="sxs-lookup"><span data-stu-id="93bdc-381">GetValue</span></span>

<span data-ttu-id="93bdc-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o no tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="93bdc-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-383">No código anterior, se `NumberKey` não for encontrado na configuração, o valor padrão de `99` será usado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-383">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="93bdc-384">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="93bdc-384">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="93bdc-385">Para os exemplos a seguir, considere o seguinte *MySubsection.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-385">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="93bdc-386">O código a seguir adiciona *MySubsection.js* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-386">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="93bdc-387">GetSection</span><span class="sxs-lookup"><span data-stu-id="93bdc-387">GetSection</span></span>

<span data-ttu-id="93bdc-388">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retorna uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="93bdc-388">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="93bdc-389">O código a seguir retorna valores para `section1` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-389">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-390">O código a seguir retorna valores para `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-390">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-391">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-391">`GetSection` never returns `null`.</span></span> <span data-ttu-id="93bdc-392">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-392">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="93bdc-393">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="93bdc-393">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="93bdc-394"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="93bdc-394">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="93bdc-395">GetChildren e existe</span><span class="sxs-lookup"><span data-stu-id="93bdc-395">GetChildren and Exists</span></span>

<span data-ttu-id="93bdc-396">O código a seguir chama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e retorna valores para `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-396">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-397">O código anterior chama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para verificar se a seção existe:</span><span class="sxs-lookup"><span data-stu-id="93bdc-397">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="93bdc-398">Associar uma matriz</span><span class="sxs-lookup"><span data-stu-id="93bdc-398">Bind an array</span></span>

<span data-ttu-id="93bdc-399">O [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) dá suporte a matrizes de associação a objetos usando índices de matriz em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-399">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="93bdc-400">Qualquer formato de matriz que expõe um segmento de chave numérica é capaz de associar a matriz a uma matriz de classe [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="93bdc-400">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="93bdc-401">Considere *MyArray.js* do download de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="93bdc-401">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="93bdc-402">O código a seguir adiciona *MyArray.js* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-402">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="93bdc-403">O código a seguir lê a configuração e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-403">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-404">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="93bdc-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="93bdc-405">Na saída anterior, o índice 3 tem valor `value40` , correspondente a `"4": "value40",` no *MyArray.jsem*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-405">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="93bdc-406">Os índices de matriz associados são contínuos e não associados ao índice de chave de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-406">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="93bdc-407">O associador de configuração não é capaz de ligar valores nulos ou criar entradas nulas em objetos associados</span><span class="sxs-lookup"><span data-stu-id="93bdc-407">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="93bdc-408">O código a seguir carrega a `array:entries` configuração com o <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> método de extensão:</span><span class="sxs-lookup"><span data-stu-id="93bdc-408">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="93bdc-409">O código a seguir lê a configuração no `arrayDict` `Dictionary` e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-409">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-410">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="93bdc-410">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="93bdc-411">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-411">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="93bdc-412">Quando os dados de configuração que contêm uma matriz são associados, os índices de matriz nas chaves de configuração são usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="93bdc-412">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="93bdc-413">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="93bdc-413">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="93bdc-414">O item de configuração ausente para &num; o índice 3 pode ser fornecido antes da associação à `ArrayExample` instância por qualquer provedor de configuração que leia o &num; par chave/valor do índice 3.</span><span class="sxs-lookup"><span data-stu-id="93bdc-414">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="93bdc-415">Considere o seguinte *Value3.jsno* arquivo do download de exemplo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-415">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="93bdc-416">O código a seguir inclui a configuração para o *Value3.jsno* e no `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-416">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="93bdc-417">O código a seguir lê a configuração anterior e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-417">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-418">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="93bdc-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="93bdc-419">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="93bdc-419">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="93bdc-420">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="93bdc-420">Custom configuration provider</span></span>

<span data-ttu-id="93bdc-421">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="93bdc-421">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="93bdc-422">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="93bdc-422">The provider has the following characteristics:</span></span>

* <span data-ttu-id="93bdc-423">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-423">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="93bdc-424">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-424">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="93bdc-425">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="93bdc-425">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="93bdc-426">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="93bdc-426">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="93bdc-427">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-427">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="93bdc-428">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="93bdc-428">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="93bdc-429">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-429">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="93bdc-430">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="93bdc-430">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="93bdc-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="93bdc-432">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-432">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="93bdc-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="93bdc-434">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-434">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="93bdc-435">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="93bdc-435">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="93bdc-436">Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="93bdc-436">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="93bdc-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="93bdc-438">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-438">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="93bdc-439">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-439">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="93bdc-440">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-440">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="93bdc-441">Configuração de acesso na inicialização</span><span class="sxs-lookup"><span data-stu-id="93bdc-441">Access configuration in Startup</span></span>

<span data-ttu-id="93bdc-442">O código a seguir exibe dados de configuração em `Startup` métodos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-442">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="93bdc-443">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="93bdc-443">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="93bdc-444">Configuração de acesso em Razor páginas</span><span class="sxs-lookup"><span data-stu-id="93bdc-444">Access configuration in Razor Pages</span></span>

<span data-ttu-id="93bdc-445">O código a seguir exibe os dados de configuração em uma Razor página:</span><span class="sxs-lookup"><span data-stu-id="93bdc-445">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="93bdc-446">No código a seguir, `MyOptions` é adicionado ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associado à configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-446">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="93bdc-447">A marcação a seguir usa a [`@inject`](xref:mvc/views/razor#inject) Razor diretiva para resolver e exibir os valores de opções:</span><span class="sxs-lookup"><span data-stu-id="93bdc-447">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="93bdc-448">Acessar a configuração em um arquivo de exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="93bdc-448">Access configuration in a MVC view file</span></span>

<span data-ttu-id="93bdc-449">O código a seguir exibe os dados de configuração em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="93bdc-449">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="93bdc-450">Configurar opções com um delegado</span><span class="sxs-lookup"><span data-stu-id="93bdc-450">Configure options with a delegate</span></span>

<span data-ttu-id="93bdc-451">As opções configuradas em um delegado substituem valores definidos nos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-451">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="93bdc-452">A configuração de opções com um delegado é demonstrada como o exemplo 2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-452">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="93bdc-453">No código a seguir, um <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serviço é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="93bdc-453">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="93bdc-454">Ele usa um delegado para configurar valores para `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-454">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="93bdc-455">O código a seguir exibe os valores de opções:</span><span class="sxs-lookup"><span data-stu-id="93bdc-455">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="93bdc-456">No exemplo anterior, os valores de `Option1` e `Option2` são especificados em *appsettings.json* e, em seguida, substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-456">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="93bdc-457">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="93bdc-457">Host versus app configuration</span></span>

<span data-ttu-id="93bdc-458">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="93bdc-459">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="93bdc-460">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="93bdc-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="93bdc-461">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="93bdc-462">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="93bdc-463">Configuração de host padrão</span><span class="sxs-lookup"><span data-stu-id="93bdc-463">Default host configuration</span></span>

<span data-ttu-id="93bdc-464">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="93bdc-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="93bdc-465">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="93bdc-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="93bdc-466">Variáveis de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT` ) usando o [provedor de configuração de variáveis de ambiente](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="93bdc-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="93bdc-467">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="93bdc-468">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="93bdc-469">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="93bdc-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="93bdc-470">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="93bdc-471">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="93bdc-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="93bdc-472">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="93bdc-473">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="93bdc-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="93bdc-474">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="93bdc-474">Other configuration</span></span>

<span data-ttu-id="93bdc-475">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="93bdc-476">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="93bdc-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="93bdc-477">*launch.jsem* / As *launchSettings.jsno* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="93bdc-478">Em <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="93bdc-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="93bdc-479">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="93bdc-480">*web.config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="93bdc-481">As variáveis de ambiente definidas no *launchSettings.js* substituir aquelas definidas no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="93bdc-481">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="93bdc-482">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="93bdc-482">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="93bdc-483">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="93bdc-483">Add configuration from an external assembly</span></span>

<span data-ttu-id="93bdc-484">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-484">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="93bdc-485">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-485">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93bdc-486">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="93bdc-486">Additional resources</span></span>

* [<span data-ttu-id="93bdc-487">Código-fonte de configuração</span><span class="sxs-lookup"><span data-stu-id="93bdc-487">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="93bdc-488">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-488">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="93bdc-489">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-489">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="93bdc-490">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-490">Azure Key Vault</span></span>
* <span data-ttu-id="93bdc-491">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-491">Azure App Configuration</span></span>
* <span data-ttu-id="93bdc-492">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-492">Command-line arguments</span></span>
* <span data-ttu-id="93bdc-493">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="93bdc-493">Custom providers (installed or created)</span></span>
* <span data-ttu-id="93bdc-494">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="93bdc-494">Directory files</span></span>
* <span data-ttu-id="93bdc-495">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-495">Environment variables</span></span>
* <span data-ttu-id="93bdc-496">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="93bdc-496">In-memory .NET objects</span></span>
* <span data-ttu-id="93bdc-497">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="93bdc-497">Settings files</span></span>

<span data-ttu-id="93bdc-498">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="93bdc-498">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="93bdc-499">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="93bdc-499">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="93bdc-500">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="93bdc-500">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="93bdc-501">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-501">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="93bdc-502">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="93bdc-503">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="93bdc-503">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="93bdc-504">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="93bdc-504">Host versus app configuration</span></span>

<span data-ttu-id="93bdc-505">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-505">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="93bdc-506">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-506">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="93bdc-507">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="93bdc-507">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="93bdc-508">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-508">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="93bdc-509">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-509">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="93bdc-510">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="93bdc-510">Other configuration</span></span>

<span data-ttu-id="93bdc-511">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-511">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="93bdc-512">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="93bdc-512">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="93bdc-513">*launch.jsem* / As *launchSettings.jsno* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-513">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="93bdc-514">Em <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="93bdc-514">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="93bdc-515">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-515">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="93bdc-516">*web.config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-516">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="93bdc-517">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="93bdc-517">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="93bdc-518">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="93bdc-518">Default configuration</span></span>

<span data-ttu-id="93bdc-519">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="93bdc-519">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="93bdc-520">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="93bdc-520">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="93bdc-521">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="93bdc-521">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="93bdc-522">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="93bdc-522">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="93bdc-523">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="93bdc-523">Host configuration is provided from:</span></span>
  * <span data-ttu-id="93bdc-524">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-524">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="93bdc-525">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-525">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="93bdc-526">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="93bdc-527">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="93bdc-527">App configuration is provided from:</span></span>
  * <span data-ttu-id="93bdc-528">*appsettings.json* usando o [provedor de configuração de arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-528">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="93bdc-529">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-529">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="93bdc-530">[Segredos do usuário](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="93bdc-530">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="93bdc-531">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-531">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="93bdc-532">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="93bdc-532">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="93bdc-533">Segurança</span><span class="sxs-lookup"><span data-stu-id="93bdc-533">Security</span></span>

<span data-ttu-id="93bdc-534">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="93bdc-534">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="93bdc-535">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="93bdc-535">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="93bdc-536">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-536">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="93bdc-537">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="93bdc-537">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="93bdc-538">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-538">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="93bdc-539"><xref:security/app-secrets>: Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="93bdc-539"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="93bdc-540">A ferramenta Gerenciador de segredo usa o provedor de configuração de arquivo para armazenar segredos de usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="93bdc-540">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="93bdc-541">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="93bdc-541">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="93bdc-542">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="93bdc-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="93bdc-543">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-543">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="93bdc-544">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="93bdc-544">Hierarchical configuration data</span></span>

<span data-ttu-id="93bdc-545">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-545">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="93bdc-546">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="93bdc-546">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="93bdc-547">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-547">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="93bdc-548">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="93bdc-548">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="93bdc-549">section0:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-549">section0:key0</span></span>
* <span data-ttu-id="93bdc-550">section0:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-550">section0:key1</span></span>
* <span data-ttu-id="93bdc-551">section1:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-551">section1:key0</span></span>
* <span data-ttu-id="93bdc-552">section1:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-552">section1:key1</span></span>

<span data-ttu-id="93bdc-553">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-553"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="93bdc-554">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="93bdc-554">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="93bdc-555">Convenções</span><span class="sxs-lookup"><span data-stu-id="93bdc-555">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="93bdc-556">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="93bdc-556">Sources and providers</span></span>

<span data-ttu-id="93bdc-557">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-557">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="93bdc-558">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="93bdc-558">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="93bdc-559">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-559">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="93bdc-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="93bdc-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetado em uma Razor página <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="93bdc-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="93bdc-562">Nos exemplos a seguir, o `_config` campo é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-562">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="93bdc-563">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="93bdc-563">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="93bdc-564">simétricas</span><span class="sxs-lookup"><span data-stu-id="93bdc-564">Keys</span></span>

<span data-ttu-id="93bdc-565">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="93bdc-565">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="93bdc-566">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-566">Keys are case-insensitive.</span></span> <span data-ttu-id="93bdc-567">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="93bdc-567">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="93bdc-568">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-568">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="93bdc-569">Para obter mais informações sobre chaves JSON duplicadas, consulte [este problema do GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="93bdc-569">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="93bdc-570">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="93bdc-570">Hierarchical keys</span></span>
  * <span data-ttu-id="93bdc-571">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-571">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="93bdc-572">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-572">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="93bdc-573">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-573">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="93bdc-574">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="93bdc-574">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="93bdc-575">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-575">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="93bdc-576">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-576">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="93bdc-577">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="93bdc-577">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="93bdc-578">Valores</span><span class="sxs-lookup"><span data-stu-id="93bdc-578">Values</span></span>

<span data-ttu-id="93bdc-579">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="93bdc-579">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="93bdc-580">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="93bdc-580">Values are strings.</span></span>
* <span data-ttu-id="93bdc-581">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-581">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="93bdc-582">Provedores</span><span class="sxs-lookup"><span data-stu-id="93bdc-582">Providers</span></span>

<span data-ttu-id="93bdc-583">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="93bdc-583">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="93bdc-584">Provedor</span><span class="sxs-lookup"><span data-stu-id="93bdc-584">Provider</span></span> | <span data-ttu-id="93bdc-585">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="93bdc-585">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="93bdc-586">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="93bdc-586">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="93bdc-587">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-587">Azure Key Vault</span></span> |
| <span data-ttu-id="93bdc-588">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="93bdc-588">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="93bdc-589">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-589">Azure App Configuration</span></span> |
| [<span data-ttu-id="93bdc-590">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-590">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="93bdc-591">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-591">Command-line parameters</span></span> |
| [<span data-ttu-id="93bdc-592">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="93bdc-592">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="93bdc-593">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="93bdc-593">Custom source</span></span> |
| [<span data-ttu-id="93bdc-594">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-594">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="93bdc-595">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-595">Environment variables</span></span> |
| [<span data-ttu-id="93bdc-596">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="93bdc-596">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="93bdc-597">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="93bdc-597">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="93bdc-598">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="93bdc-598">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="93bdc-599">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="93bdc-599">Directory files</span></span> |
| [<span data-ttu-id="93bdc-600">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="93bdc-600">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="93bdc-601">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="93bdc-601">In-memory collections</span></span> |
| <span data-ttu-id="93bdc-602">[Segredos do usuário](xref:security/app-secrets) (tópicos de *segurança* )</span><span class="sxs-lookup"><span data-stu-id="93bdc-602">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="93bdc-603">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="93bdc-603">File in the user profile directory</span></span> |

<span data-ttu-id="93bdc-604">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-604">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="93bdc-605">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="93bdc-605">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="93bdc-606">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="93bdc-606">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="93bdc-607">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="93bdc-607">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="93bdc-608">Arquivos ( *appsettings.json* , *appSettings. { Ambiente}. JSON*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="93bdc-608">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="93bdc-609">Cofre da Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-609">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="93bdc-610">[Segredos do usuário](xref:security/app-secrets) (somente ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="93bdc-610">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="93bdc-611">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-611">Environment variables</span></span>
1. <span data-ttu-id="93bdc-612">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-612">Command-line arguments</span></span>

<span data-ttu-id="93bdc-613">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="93bdc-613">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="93bdc-614">A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com o `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-614">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="93bdc-615">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="93bdc-615">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="93bdc-616">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="93bdc-616">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="93bdc-617">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-617">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="93bdc-618">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="93bdc-618">ConfigureAppConfiguration</span></span>

<span data-ttu-id="93bdc-619">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-619">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="93bdc-620">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-620">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="93bdc-621">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="93bdc-621">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="93bdc-622">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="93bdc-622">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="93bdc-623">Para remover os provedores adicionados pelo `CreateDefaultBuilder` , chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="93bdc-623">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="93bdc-624">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="93bdc-624">Consume configuration during app startup</span></span>

<span data-ttu-id="93bdc-625">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-625">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="93bdc-626">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="93bdc-626">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="93bdc-627">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="93bdc-627">Command-line Configuration Provider</span></span>

<span data-ttu-id="93bdc-628">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="93bdc-628">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="93bdc-629">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-629">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="93bdc-630">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-630">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="93bdc-631">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="93bdc-631">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="93bdc-632">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="93bdc-632">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="93bdc-633">Configuração opcional de *appsettings.json* e *appSettings. { Arquivos. JSON do ambiente}* .</span><span class="sxs-lookup"><span data-stu-id="93bdc-633">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="93bdc-634">[Segredos do usuário](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-634">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="93bdc-635">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-635">Environment variables.</span></span>

<span data-ttu-id="93bdc-636">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="93bdc-636">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="93bdc-637">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="93bdc-637">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="93bdc-638">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="93bdc-638">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="93bdc-639">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-639">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="93bdc-640">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-640">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="93bdc-641">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="93bdc-641">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="93bdc-642">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="93bdc-642">**Example**</span></span>

<span data-ttu-id="93bdc-643">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-643">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="93bdc-644">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="93bdc-644">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="93bdc-645">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-645">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="93bdc-646">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-646">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="93bdc-647">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-647">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="93bdc-648">Argumentos</span><span class="sxs-lookup"><span data-stu-id="93bdc-648">Arguments</span></span>

<span data-ttu-id="93bdc-649">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="93bdc-649">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="93bdc-650">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="93bdc-650">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="93bdc-651">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="93bdc-651">Key prefix</span></span>               | <span data-ttu-id="93bdc-652">Exemplo</span><span class="sxs-lookup"><span data-stu-id="93bdc-652">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="93bdc-653">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="93bdc-653">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="93bdc-654">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="93bdc-654">Two dashes (`--`)</span></span>        | <span data-ttu-id="93bdc-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="93bdc-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="93bdc-656">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="93bdc-656">Forward slash (`/`)</span></span>      | <span data-ttu-id="93bdc-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="93bdc-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="93bdc-658">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="93bdc-658">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="93bdc-659">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-659">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="93bdc-660">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="93bdc-660">Switch mappings</span></span>

<span data-ttu-id="93bdc-661">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="93bdc-661">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="93bdc-662">Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="93bdc-662">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="93bdc-663">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="93bdc-663">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="93bdc-664">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-664">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="93bdc-665">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="93bdc-665">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="93bdc-666">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="93bdc-666">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="93bdc-667">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="93bdc-667">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="93bdc-668">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-668">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="93bdc-669">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="93bdc-669">Create a switch mappings dictionary.</span></span> <span data-ttu-id="93bdc-670">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="93bdc-670">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="93bdc-671">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="93bdc-671">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="93bdc-672">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-672">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="93bdc-673">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-673">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="93bdc-674">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="93bdc-674">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="93bdc-675">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="93bdc-675">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="93bdc-676">Chave</span><span class="sxs-lookup"><span data-stu-id="93bdc-676">Key</span></span>       | <span data-ttu-id="93bdc-677">Valor</span><span class="sxs-lookup"><span data-stu-id="93bdc-677">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="93bdc-678">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="93bdc-678">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="93bdc-679">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="93bdc-679">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="93bdc-680">Chave</span><span class="sxs-lookup"><span data-stu-id="93bdc-680">Key</span></span>               | <span data-ttu-id="93bdc-681">Valor</span><span class="sxs-lookup"><span data-stu-id="93bdc-681">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="93bdc-682">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-682">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="93bdc-683">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="93bdc-683">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="93bdc-684">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-684">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="93bdc-685">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-685">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="93bdc-686">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="93bdc-686">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="93bdc-687">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-687">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="93bdc-688">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="93bdc-688">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="93bdc-689">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="93bdc-689">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="93bdc-690">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-690">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="93bdc-691">Configuração opcional de *appsettings.json* e *appSettings. { Arquivos. JSON do ambiente}* .</span><span class="sxs-lookup"><span data-stu-id="93bdc-691">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="93bdc-692">[Segredos do usuário](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-692">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="93bdc-693">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="93bdc-693">Command-line arguments.</span></span>

<span data-ttu-id="93bdc-694">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-694">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="93bdc-695">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-695">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="93bdc-696">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-696">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="93bdc-697">Chame `AddEnvironmentVariables` Last para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="93bdc-697">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="93bdc-698">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="93bdc-698">**Example**</span></span>

<span data-ttu-id="93bdc-699">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-699">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="93bdc-700">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-700">Run the sample app.</span></span> <span data-ttu-id="93bdc-701">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-701">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="93bdc-702">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-702">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="93bdc-703">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-703">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="93bdc-704">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-704">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="93bdc-705">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-705">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="93bdc-706">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="93bdc-706">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="93bdc-707">Prefixos</span><span class="sxs-lookup"><span data-stu-id="93bdc-707">Prefixes</span></span>

<span data-ttu-id="93bdc-708">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o `AddEnvironmentVariables` método.</span><span class="sxs-lookup"><span data-stu-id="93bdc-708">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="93bdc-709">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-709">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="93bdc-710">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-710">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="93bdc-711">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-711">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="93bdc-712">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="93bdc-712">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="93bdc-713">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="93bdc-713">**Connection string prefixes**</span></span>

<span data-ttu-id="93bdc-714">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-714">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="93bdc-715">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-715">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="93bdc-716">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="93bdc-716">Connection string prefix</span></span> | <span data-ttu-id="93bdc-717">Provedor</span><span class="sxs-lookup"><span data-stu-id="93bdc-717">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="93bdc-718">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="93bdc-718">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="93bdc-719">MySQL</span><span class="sxs-lookup"><span data-stu-id="93bdc-719">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="93bdc-720">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="93bdc-720">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="93bdc-721">SQL Server</span><span class="sxs-lookup"><span data-stu-id="93bdc-721">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="93bdc-722">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="93bdc-722">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="93bdc-723">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="93bdc-723">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="93bdc-724">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="93bdc-724">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="93bdc-725">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="93bdc-725">Environment variable key</span></span> | <span data-ttu-id="93bdc-726">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="93bdc-726">Converted configuration key</span></span> | <span data-ttu-id="93bdc-727">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="93bdc-727">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="93bdc-728">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="93bdc-728">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="93bdc-729">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="93bdc-730">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="93bdc-730">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="93bdc-731">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="93bdc-732">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="93bdc-732">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="93bdc-733">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-733">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="93bdc-734">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="93bdc-734">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="93bdc-735">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="93bdc-735">**Example**</span></span>

<span data-ttu-id="93bdc-736">Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:</span><span class="sxs-lookup"><span data-stu-id="93bdc-736">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="93bdc-737">Nome: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="93bdc-737">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="93bdc-738">Valor: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="93bdc-738">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="93bdc-739">Se `IConfiguration` for injetado e atribuído a um campo chamado `_config` , leia o valor:</span><span class="sxs-lookup"><span data-stu-id="93bdc-739">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="93bdc-740">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="93bdc-740">File Configuration Provider</span></span>

<span data-ttu-id="93bdc-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="93bdc-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="93bdc-742">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-742">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="93bdc-743">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="93bdc-743">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="93bdc-744">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="93bdc-744">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="93bdc-745">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="93bdc-745">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="93bdc-746">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="93bdc-746">INI Configuration Provider</span></span>

<span data-ttu-id="93bdc-747">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="93bdc-747">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="93bdc-748">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-748">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="93bdc-749">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="93bdc-749">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="93bdc-750">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="93bdc-750">Overloads permit specifying:</span></span>

* <span data-ttu-id="93bdc-751">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="93bdc-751">Whether the file is optional.</span></span>
* <span data-ttu-id="93bdc-752">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-752">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="93bdc-753">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-753">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="93bdc-754">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-754">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="93bdc-755">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="93bdc-755">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="93bdc-756">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-756">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="93bdc-757">section0:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-757">section0:key0</span></span>
* <span data-ttu-id="93bdc-758">section0:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-758">section0:key1</span></span>
* <span data-ttu-id="93bdc-759">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="93bdc-759">section1:subsection:key</span></span>
* <span data-ttu-id="93bdc-760">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="93bdc-760">section2:subsection0:key</span></span>
* <span data-ttu-id="93bdc-761">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="93bdc-761">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="93bdc-762">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="93bdc-762">JSON Configuration Provider</span></span>

<span data-ttu-id="93bdc-763">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="93bdc-763">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="93bdc-764">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-764">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="93bdc-765">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="93bdc-765">Overloads permit specifying:</span></span>

* <span data-ttu-id="93bdc-766">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="93bdc-766">Whether the file is optional.</span></span>
* <span data-ttu-id="93bdc-767">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-767">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="93bdc-768">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-768">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="93bdc-769">`AddJsonFile` é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com o `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-769">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="93bdc-770">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="93bdc-770">The method is called to load configuration from:</span></span>

* <span data-ttu-id="93bdc-771">*appsettings.json*: Este arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="93bdc-771">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="93bdc-772">A versão de ambiente do arquivo pode substituir os valores fornecidos pelo *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-772">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="93bdc-773">*appSettings. {Environment}. JSON*: a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="93bdc-773">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="93bdc-774">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="93bdc-774">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="93bdc-775">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="93bdc-775">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="93bdc-776">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="93bdc-776">Environment variables.</span></span>
* <span data-ttu-id="93bdc-777">[Segredos do usuário](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-777">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="93bdc-778">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="93bdc-778">Command-line arguments.</span></span>

<span data-ttu-id="93bdc-779">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="93bdc-779">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="93bdc-780">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-780">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="93bdc-781">Chame `ConfigureAppConfiguration` ao compilar o host para especificar a configuração do aplicativo para arquivos diferentes de *appsettings.json* e *appSettings. { Ambiente}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-781">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="93bdc-782">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="93bdc-782">**Example**</span></span>

<span data-ttu-id="93bdc-783">O aplicativo de exemplo aproveita o método de conveniência estática `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="93bdc-783">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="93bdc-784">A primeira chamada para `AddJsonFile` carrega a configuração de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="93bdc-784">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="93bdc-785">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="93bdc-785">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="93bdc-786">Para *appsettings.Development.js* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="93bdc-786">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="93bdc-787">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-787">Run the sample app.</span></span> <span data-ttu-id="93bdc-788">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-788">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="93bdc-789">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-789">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="93bdc-790">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="93bdc-790">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="93bdc-791">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="93bdc-791">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="93bdc-792">Abra as *Propriedades/launchSettings.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-792">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="93bdc-793">No `ConfigurationSample` perfil, altere o valor da variável de `ASPNETCORE_ENVIRONMENT` ambiente para `Production` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-793">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="93bdc-794">Salve o arquivo e execute o aplicativo com o `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="93bdc-794">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="93bdc-795">As configurações no *appsettings.Development.js* não substituem mais as configurações em *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="93bdc-795">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="93bdc-796">O nível de log para a chave `Logging:LogLevel:Default` é `Warning` .</span><span class="sxs-lookup"><span data-stu-id="93bdc-796">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="93bdc-797">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="93bdc-797">XML Configuration Provider</span></span>

<span data-ttu-id="93bdc-798">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="93bdc-798">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="93bdc-799">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-799">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="93bdc-800">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="93bdc-800">Overloads permit specifying:</span></span>

* <span data-ttu-id="93bdc-801">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="93bdc-801">Whether the file is optional.</span></span>
* <span data-ttu-id="93bdc-802">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-802">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="93bdc-803">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-803">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="93bdc-804">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="93bdc-804">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="93bdc-805">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-805">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="93bdc-806">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-806">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="93bdc-807">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="93bdc-807">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="93bdc-808">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="93bdc-809">section0:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-809">section0:key0</span></span>
* <span data-ttu-id="93bdc-810">section0:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-810">section0:key1</span></span>
* <span data-ttu-id="93bdc-811">section1:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-811">section1:key0</span></span>
* <span data-ttu-id="93bdc-812">section1:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-812">section1:key1</span></span>

<span data-ttu-id="93bdc-813">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="93bdc-813">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="93bdc-814">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="93bdc-815">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-815">section:section0:key:key0</span></span>
* <span data-ttu-id="93bdc-816">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-816">section:section0:key:key1</span></span>
* <span data-ttu-id="93bdc-817">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-817">section:section1:key:key0</span></span>
* <span data-ttu-id="93bdc-818">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-818">section:section1:key:key1</span></span>

<span data-ttu-id="93bdc-819">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="93bdc-819">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="93bdc-820">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-820">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="93bdc-821">key:attribute</span><span class="sxs-lookup"><span data-stu-id="93bdc-821">key:attribute</span></span>
* <span data-ttu-id="93bdc-822">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="93bdc-822">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="93bdc-823">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="93bdc-823">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="93bdc-824">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-824">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="93bdc-825">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-825">The key is the file name.</span></span> <span data-ttu-id="93bdc-826">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-826">The value contains the file's contents.</span></span> <span data-ttu-id="93bdc-827">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="93bdc-827">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="93bdc-828">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-828">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="93bdc-829">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="93bdc-829">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="93bdc-830">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="93bdc-830">Overloads permit specifying:</span></span>

* <span data-ttu-id="93bdc-831">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="93bdc-831">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="93bdc-832">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="93bdc-832">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="93bdc-833">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-833">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="93bdc-834">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-834">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="93bdc-835">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="93bdc-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="93bdc-836">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="93bdc-836">Memory Configuration Provider</span></span>

<span data-ttu-id="93bdc-837">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-837">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="93bdc-838">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-838">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="93bdc-839">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-839">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="93bdc-840">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-840">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="93bdc-841">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="93bdc-841">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="93bdc-842">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-842">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="93bdc-843">GetValue</span><span class="sxs-lookup"><span data-stu-id="93bdc-843">GetValue</span></span>

<span data-ttu-id="93bdc-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="93bdc-845">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="93bdc-845">An overload accepts a default value.</span></span>

<span data-ttu-id="93bdc-846">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="93bdc-846">The following example:</span></span>

* <span data-ttu-id="93bdc-847">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-847">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="93bdc-848">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-848">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="93bdc-849">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-849">Types the value as an `int`.</span></span>
* <span data-ttu-id="93bdc-850">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="93bdc-850">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="93bdc-851">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="93bdc-851">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="93bdc-852">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="93bdc-852">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="93bdc-853">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="93bdc-853">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="93bdc-854">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-854">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="93bdc-855">section0:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-855">section0:key0</span></span>
* <span data-ttu-id="93bdc-856">section0:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-856">section0:key1</span></span>
* <span data-ttu-id="93bdc-857">section1:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-857">section1:key0</span></span>
* <span data-ttu-id="93bdc-858">section1:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-858">section1:key1</span></span>
* <span data-ttu-id="93bdc-859">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-859">section2:subsection0:key0</span></span>
* <span data-ttu-id="93bdc-860">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-860">section2:subsection0:key1</span></span>
* <span data-ttu-id="93bdc-861">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="93bdc-861">section2:subsection1:key0</span></span>
* <span data-ttu-id="93bdc-862">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="93bdc-862">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="93bdc-863">GetSection</span><span class="sxs-lookup"><span data-stu-id="93bdc-863">GetSection</span></span>

<span data-ttu-id="93bdc-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="93bdc-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="93bdc-865">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="93bdc-865">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="93bdc-866">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="93bdc-866">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="93bdc-867">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="93bdc-867">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="93bdc-868">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-868">`GetSection` never returns `null`.</span></span> <span data-ttu-id="93bdc-869">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-869">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="93bdc-870">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="93bdc-870">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="93bdc-871"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="93bdc-871">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="93bdc-872">GetChildren</span><span class="sxs-lookup"><span data-stu-id="93bdc-872">GetChildren</span></span>

<span data-ttu-id="93bdc-873">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="93bdc-873">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="93bdc-874">Exists</span><span class="sxs-lookup"><span data-stu-id="93bdc-874">Exists</span></span>

<span data-ttu-id="93bdc-875">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-875">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="93bdc-876">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-876">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="93bdc-877">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="93bdc-877">Bind to an object graph</span></span>

<span data-ttu-id="93bdc-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="93bdc-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="93bdc-879">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="93bdc-879">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="93bdc-880">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="93bdc-880">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="93bdc-881">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-881">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="93bdc-882">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-882">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="93bdc-883">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="93bdc-883">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="93bdc-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="93bdc-885">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-885">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="93bdc-886">O código a seguir mostra como usar `Get<T>` o com o exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="93bdc-886">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="93bdc-887">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="93bdc-887">Bind an array to a class</span></span>

<span data-ttu-id="93bdc-888">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="93bdc-888">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="93bdc-889">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-889">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="93bdc-890">Qualquer formato de matriz que expõe um segmento de chave numérico ( `:0:` , `:1:` , &hellip; `:{n}:` ) é capaz de associar a matriz a uma matriz de classe poco.</span><span class="sxs-lookup"><span data-stu-id="93bdc-890">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="93bdc-891">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="93bdc-891">Binding is provided by convention.</span></span> <span data-ttu-id="93bdc-892">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="93bdc-892">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="93bdc-893">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="93bdc-893">**In-memory array processing**</span></span>

<span data-ttu-id="93bdc-894">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="93bdc-894">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="93bdc-895">Chave</span><span class="sxs-lookup"><span data-stu-id="93bdc-895">Key</span></span>             | <span data-ttu-id="93bdc-896">Valor</span><span class="sxs-lookup"><span data-stu-id="93bdc-896">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="93bdc-897">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="93bdc-897">array:entries:0</span></span> | <span data-ttu-id="93bdc-898">value0</span><span class="sxs-lookup"><span data-stu-id="93bdc-898">value0</span></span> |
| <span data-ttu-id="93bdc-899">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="93bdc-899">array:entries:1</span></span> | <span data-ttu-id="93bdc-900">value1</span><span class="sxs-lookup"><span data-stu-id="93bdc-900">value1</span></span> |
| <span data-ttu-id="93bdc-901">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="93bdc-901">array:entries:2</span></span> | <span data-ttu-id="93bdc-902">value2</span><span class="sxs-lookup"><span data-stu-id="93bdc-902">value2</span></span> |
| <span data-ttu-id="93bdc-903">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="93bdc-903">array:entries:4</span></span> | <span data-ttu-id="93bdc-904">value4</span><span class="sxs-lookup"><span data-stu-id="93bdc-904">value4</span></span> |
| <span data-ttu-id="93bdc-905">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="93bdc-905">array:entries:5</span></span> | <span data-ttu-id="93bdc-906">value5</span><span class="sxs-lookup"><span data-stu-id="93bdc-906">value5</span></span> |

<span data-ttu-id="93bdc-907">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="93bdc-907">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="93bdc-908">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="93bdc-908">The array skips a value for index &num;3.</span></span> <span data-ttu-id="93bdc-909">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="93bdc-909">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="93bdc-910">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="93bdc-910">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="93bdc-911">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="93bdc-911">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="93bdc-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) a sintaxe também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="93bdc-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="93bdc-913">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-913">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="93bdc-914">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="93bdc-914">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="93bdc-915">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="93bdc-915">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="93bdc-916">0</span><span class="sxs-lookup"><span data-stu-id="93bdc-916">0</span></span>                            | <span data-ttu-id="93bdc-917">value0</span><span class="sxs-lookup"><span data-stu-id="93bdc-917">value0</span></span>                       |
| <span data-ttu-id="93bdc-918">1</span><span class="sxs-lookup"><span data-stu-id="93bdc-918">1</span></span>                            | <span data-ttu-id="93bdc-919">value1</span><span class="sxs-lookup"><span data-stu-id="93bdc-919">value1</span></span>                       |
| <span data-ttu-id="93bdc-920">2</span><span class="sxs-lookup"><span data-stu-id="93bdc-920">2</span></span>                            | <span data-ttu-id="93bdc-921">value2</span><span class="sxs-lookup"><span data-stu-id="93bdc-921">value2</span></span>                       |
| <span data-ttu-id="93bdc-922">3</span><span class="sxs-lookup"><span data-stu-id="93bdc-922">3</span></span>                            | <span data-ttu-id="93bdc-923">value4</span><span class="sxs-lookup"><span data-stu-id="93bdc-923">value4</span></span>                       |
| <span data-ttu-id="93bdc-924">4</span><span class="sxs-lookup"><span data-stu-id="93bdc-924">4</span></span>                            | <span data-ttu-id="93bdc-925">value5</span><span class="sxs-lookup"><span data-stu-id="93bdc-925">value5</span></span>                       |

<span data-ttu-id="93bdc-926">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-926">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="93bdc-927">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="93bdc-927">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="93bdc-928">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="93bdc-928">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="93bdc-929">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-929">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="93bdc-930">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="93bdc-930">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="93bdc-931">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-931">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="93bdc-932">Em `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="93bdc-932">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="93bdc-933">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-933">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="93bdc-934">Chave</span><span class="sxs-lookup"><span data-stu-id="93bdc-934">Key</span></span>             | <span data-ttu-id="93bdc-935">Valor</span><span class="sxs-lookup"><span data-stu-id="93bdc-935">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="93bdc-936">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="93bdc-936">array:entries:3</span></span> | <span data-ttu-id="93bdc-937">value3</span><span class="sxs-lookup"><span data-stu-id="93bdc-937">value3</span></span> |

<span data-ttu-id="93bdc-938">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="93bdc-938">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="93bdc-939">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="93bdc-939">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="93bdc-940">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="93bdc-940">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="93bdc-941">0</span><span class="sxs-lookup"><span data-stu-id="93bdc-941">0</span></span>                            | <span data-ttu-id="93bdc-942">value0</span><span class="sxs-lookup"><span data-stu-id="93bdc-942">value0</span></span>                       |
| <span data-ttu-id="93bdc-943">1</span><span class="sxs-lookup"><span data-stu-id="93bdc-943">1</span></span>                            | <span data-ttu-id="93bdc-944">value1</span><span class="sxs-lookup"><span data-stu-id="93bdc-944">value1</span></span>                       |
| <span data-ttu-id="93bdc-945">2</span><span class="sxs-lookup"><span data-stu-id="93bdc-945">2</span></span>                            | <span data-ttu-id="93bdc-946">value2</span><span class="sxs-lookup"><span data-stu-id="93bdc-946">value2</span></span>                       |
| <span data-ttu-id="93bdc-947">3</span><span class="sxs-lookup"><span data-stu-id="93bdc-947">3</span></span>                            | <span data-ttu-id="93bdc-948">value3</span><span class="sxs-lookup"><span data-stu-id="93bdc-948">value3</span></span>                       |
| <span data-ttu-id="93bdc-949">4</span><span class="sxs-lookup"><span data-stu-id="93bdc-949">4</span></span>                            | <span data-ttu-id="93bdc-950">value4</span><span class="sxs-lookup"><span data-stu-id="93bdc-950">value4</span></span>                       |
| <span data-ttu-id="93bdc-951">5</span><span class="sxs-lookup"><span data-stu-id="93bdc-951">5</span></span>                            | <span data-ttu-id="93bdc-952">value5</span><span class="sxs-lookup"><span data-stu-id="93bdc-952">value5</span></span>                       |

<span data-ttu-id="93bdc-953">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="93bdc-953">**JSON array processing**</span></span>

<span data-ttu-id="93bdc-954">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="93bdc-954">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="93bdc-955">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="93bdc-955">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="93bdc-956">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="93bdc-956">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="93bdc-957">Chave</span><span class="sxs-lookup"><span data-stu-id="93bdc-957">Key</span></span>                     | <span data-ttu-id="93bdc-958">Valor</span><span class="sxs-lookup"><span data-stu-id="93bdc-958">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="93bdc-959">json_array:key</span><span class="sxs-lookup"><span data-stu-id="93bdc-959">json_array:key</span></span>          | <span data-ttu-id="93bdc-960">valueA</span><span class="sxs-lookup"><span data-stu-id="93bdc-960">valueA</span></span> |
| <span data-ttu-id="93bdc-961">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="93bdc-961">json_array:subsection:0</span></span> | <span data-ttu-id="93bdc-962">valueB</span><span class="sxs-lookup"><span data-stu-id="93bdc-962">valueB</span></span> |
| <span data-ttu-id="93bdc-963">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="93bdc-963">json_array:subsection:1</span></span> | <span data-ttu-id="93bdc-964">valueC</span><span class="sxs-lookup"><span data-stu-id="93bdc-964">valueC</span></span> |
| <span data-ttu-id="93bdc-965">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="93bdc-965">json_array:subsection:2</span></span> | <span data-ttu-id="93bdc-966">valueD</span><span class="sxs-lookup"><span data-stu-id="93bdc-966">valueD</span></span> |

<span data-ttu-id="93bdc-967">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="93bdc-967">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="93bdc-968">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-968">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="93bdc-969">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-969">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="93bdc-970">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="93bdc-970">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="93bdc-971">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="93bdc-971">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="93bdc-972">0</span><span class="sxs-lookup"><span data-stu-id="93bdc-972">0</span></span>                                   | <span data-ttu-id="93bdc-973">valueB</span><span class="sxs-lookup"><span data-stu-id="93bdc-973">valueB</span></span>                              |
| <span data-ttu-id="93bdc-974">1</span><span class="sxs-lookup"><span data-stu-id="93bdc-974">1</span></span>                                   | <span data-ttu-id="93bdc-975">valueC</span><span class="sxs-lookup"><span data-stu-id="93bdc-975">valueC</span></span>                              |
| <span data-ttu-id="93bdc-976">2</span><span class="sxs-lookup"><span data-stu-id="93bdc-976">2</span></span>                                   | <span data-ttu-id="93bdc-977">valueD</span><span class="sxs-lookup"><span data-stu-id="93bdc-977">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="93bdc-978">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="93bdc-978">Custom configuration provider</span></span>

<span data-ttu-id="93bdc-979">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="93bdc-979">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="93bdc-980">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="93bdc-980">The provider has the following characteristics:</span></span>

* <span data-ttu-id="93bdc-981">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-981">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="93bdc-982">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="93bdc-982">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="93bdc-983">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="93bdc-983">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="93bdc-984">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="93bdc-984">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="93bdc-985">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-985">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="93bdc-986">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="93bdc-986">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="93bdc-987">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-987">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="93bdc-988">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="93bdc-988">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="93bdc-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="93bdc-990">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-990">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="93bdc-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="93bdc-992">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-992">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="93bdc-993">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="93bdc-993">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="93bdc-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="93bdc-995">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-995">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="93bdc-996">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-996">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="93bdc-997">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="93bdc-997">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="93bdc-998">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="93bdc-998">Access configuration during startup</span></span>

<span data-ttu-id="93bdc-999">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="93bdc-999">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="93bdc-1000">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="93bdc-1000">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="93bdc-1001">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="93bdc-1001">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="93bdc-1002">Acessar a configuração em uma Razor página de páginas ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="93bdc-1002">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="93bdc-1003">Para acessar as definições de configuração em uma Razor página de páginas ou em uma exibição do MVC, adicione uma [diretiva using](xref:mvc/views/razor#using) ([referência C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [ namespaceMicrosoft.Extensions.Configuração](xref:Microsoft.Extensions.Configuration) e insira <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="93bdc-1003">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="93bdc-1004">Em uma Razor página de páginas:</span><span class="sxs-lookup"><span data-stu-id="93bdc-1004">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="93bdc-1005">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="93bdc-1005">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="93bdc-1006">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="93bdc-1006">Add configuration from an external assembly</span></span>

<span data-ttu-id="93bdc-1007">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="93bdc-1007">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="93bdc-1008">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="93bdc-1008">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93bdc-1009">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="93bdc-1009">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
