---
title: Configuração no ASP.NET Core
author: rick-anderson
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
ms.openlocfilehash: 0f069b049889f7caade493e238ac7a23db5e79af
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536270"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="7ba9f-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ba9f-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="7ba9f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ba9f-105">A configuração no ASP.NET Core é executada usando um ou mais [provedores de configuração](#cp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="7ba9f-106">Os provedores de configuração lêem dados de configuração de pares chave-valor usando uma variedade de fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="7ba9f-107">Arquivos de configurações, como *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="7ba9f-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="7ba9f-108">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-108">Environment variables</span></span>
* <span data-ttu-id="7ba9f-109">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-109">Azure Key Vault</span></span>
* <span data-ttu-id="7ba9f-110">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-110">Azure App Configuration</span></span>
* <span data-ttu-id="7ba9f-111">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-111">Command-line arguments</span></span>
* <span data-ttu-id="7ba9f-112">Provedores personalizados, instalados ou criados</span><span class="sxs-lookup"><span data-stu-id="7ba9f-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="7ba9f-113">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="7ba9f-113">Directory files</span></span>
* <span data-ttu-id="7ba9f-114">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="7ba9f-114">In-memory .NET objects</span></span>

<span data-ttu-id="7ba9f-115">Este tópico fornece informações sobre a configuração no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="7ba9f-116">Para obter informações sobre como usar a configuração em aplicativos de console, consulte [configuração do .net](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="7ba9f-117">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ba9f-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="7ba9f-118">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="7ba9f-118">Default configuration</span></span>

<span data-ttu-id="7ba9f-119">ASP.NET Core aplicativos Web criados com [dotnet novo](/dotnet/core/tools/dotnet-new) ou o Visual Studio geram o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="7ba9f-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="7ba9f-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Adiciona um existente `IConfiguration` como uma origem.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="7ba9f-122">No caso de configuração padrão, o adiciona a configuração do [host](#hvac) e a define como a primeira origem para a configuração do _aplicativo_ .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="7ba9f-123">[appsettings.json](#appsettingsjson) usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="7ba9f-124">*appSettings.* `Environment` *. JSON* usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="7ba9f-125">Por exemplo, *appSettings*. ***Produção \* \* _._json* e *appSettings*. \* \* \* desenvolvimento** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="7ba9f-126">[Segredos do aplicativo](xref:security/app-secrets) quando o aplicativo é executado no `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="7ba9f-127">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="7ba9f-128">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="7ba9f-129">Os provedores de configuração adicionados posteriormente substituem as configurações de chave anteriores.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="7ba9f-130">Por exemplo, se `MyKey` for definido no *appsettings.json* e no ambiente, o valor do ambiente será usado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="7ba9f-131">Usando os provedores de configuração padrão, o  [provedor de configuração de linha de comando](#clcp) substitui todos os outros provedores.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="7ba9f-132">Para obter mais informações sobre o `CreateDefaultBuilder` , consulte [configurações padrão do Construtor](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="7ba9f-133">O código a seguir exibe os provedores de configuração habilitados na ordem em que foram adicionados:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="7ba9f-134">Considere o seguinte *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="7ba9f-135">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-136">O padrão <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="7ba9f-137">*appSettings.* `Environment` *. JSON* : por exemplo, *appSettings*. ***Produção \* \* _._json* e *appSettings*. \* \* \* arquivos de desenvolvimento** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="7ba9f-138">A versão de ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="7ba9f-139">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="7ba9f-140">*appSettings*. `Environment` . valores *JSON* substituem chaves em *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="7ba9f-141">Por exemplo, por padrão:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-141">For example, by default:</span></span>

* <span data-ttu-id="7ba9f-142">Em desenvolvimento, *appSettings*. \***desenvolvimento** _._json \* a configuração substitui os valores encontrados em *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="7ba9f-143">Em produção, *appSettings*. \* _._Json de **produção**\* a configuração substitui os valores encontrados em *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="7ba9f-144">Por exemplo, ao implantar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="7ba9f-145">Associar dados de configuração hierárquica usando o padrão de opções</span><span class="sxs-lookup"><span data-stu-id="7ba9f-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="7ba9f-146">Usando a configuração [padrão](#default) , o *appsettings.json* e *appSettings.* `Environment` os arquivos *. JSON* são habilitados com [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="7ba9f-147">As alterações feitas no *appsettings.json* e *appSettings.* `Environment` o arquivo *. JSON* ***após*** o início do aplicativo é lido pelo [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="7ba9f-148">Consulte [provedor de configuração JSON](#jcp) neste documento para obter informações sobre como adicionar arquivos de configuração JSON adicionais.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="7ba9f-149">Combinando coleção de serviços</span><span class="sxs-lookup"><span data-stu-id="7ba9f-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="7ba9f-150">Segurança e segredos do usuário</span><span class="sxs-lookup"><span data-stu-id="7ba9f-150">Security and user secrets</span></span>

<span data-ttu-id="7ba9f-151">Diretrizes de dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="7ba9f-152">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="7ba9f-153">A ferramenta [Gerenciador de segredo](xref:security/app-secrets) pode ser usada para armazenar segredos no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="7ba9f-154">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="7ba9f-155">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="7ba9f-156">Por [padrão](#default), a fonte de configuração de segredos do usuário é registrada após as fontes de configuração do JSON.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="7ba9f-157">Portanto, as chaves de segredos do usuário têm precedência sobre as chaves em *appsettings.json* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="7ba9f-158">Para obter mais informações sobre como armazenar senhas ou outros dados confidenciais:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="7ba9f-159"><xref:security/app-secrets>: Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="7ba9f-160">A ferramenta Gerenciador de segredo usa o [provedor de configuração de arquivo](#fcp) para armazenar segredos de usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="7ba9f-161">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="7ba9f-162">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="7ba9f-163">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-163">Environment variables</span></span>

<span data-ttu-id="7ba9f-164">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração de pares chave-valor da variável de ambiente após a leitura *appsettings.json* , *appSettings.* `Environment` *. JSON* e [segredos do usuário](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="7ba9f-165">Portanto, os valores de chave lidos dos valores de substituição de ambiente lidos de *appsettings.json* , *appSettings.* `Environment` *. JSON* e segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="7ba9f-166">Os seguintes `set` comandos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-166">The following `set` commands:</span></span>

* <span data-ttu-id="7ba9f-167">Defina as chaves de ambiente e os valores do [exemplo anterior](#appsettingsjson) no Windows.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="7ba9f-168">Teste as configurações ao usar o [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="7ba9f-169">O `dotnet run` comando deve ser executado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="7ba9f-170">As configurações de ambiente anteriores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-170">The preceding environment settings:</span></span>

* <span data-ttu-id="7ba9f-171">São definidos apenas em processos iniciados na janela de comando em que foram definidos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="7ba9f-172">Não serão lidos por navegadores iniciados com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="7ba9f-173">Os comandos [setx](/windows-server/administration/windows-commands/setx) a seguir podem ser usados para definir as chaves de ambiente e os valores no Windows.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="7ba9f-174">Ao contrário `set` de, `setx` as configurações são persistidas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="7ba9f-175">`/M` define a variável no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="7ba9f-176">Se a `/M` opção não for usada, uma variável de ambiente do usuário será definida.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="7ba9f-177">Para testar se os comandos anteriores substituem *appsettings.json* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="7ba9f-178">Com o Visual Studio: saia e reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="7ba9f-179">Com a CLI: iniciar uma nova janela de comando e Enter `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="7ba9f-180">Chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> com uma cadeia de caracteres para especificar um prefixo para variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="7ba9f-181">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-181">In the preceding code:</span></span>

* <span data-ttu-id="7ba9f-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="7ba9f-183">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="7ba9f-184">Variáveis de ambiente definidas com o `MyCustomPrefix_` prefixo substituem os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="7ba9f-185">Isso inclui variáveis de ambiente sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="7ba9f-186">O prefixo é eliminado quando os pares chave-valor de configuração são lidos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="7ba9f-187">Os comandos a seguir testam o prefixo personalizado:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="7ba9f-188">A [configuração padrão](#default) carrega variáveis de ambiente e argumentos de linha de comando prefixados com `DOTNET_` and `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="7ba9f-189">Os `DOTNET_` `ASPNETCORE_` prefixos e são usados por ASP.NET Core para [configuração de host e aplicativo](xref:fundamentals/host/generic-host#host-configuration), mas não para a configuração do usuário.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="7ba9f-190">Para obter mais informações sobre a configuração de host e aplicativo, consulte [host genérico .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="7ba9f-191">Em [Azure app serviço](https://azure.microsoft.com/services/app-service/), selecione **nova configuração de aplicativo** na página **configurações > configuração** .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="7ba9f-192">Azure App configurações do aplicativo de serviço são:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="7ba9f-193">Criptografado em repouso e transmitido por um canal criptografado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="7ba9f-194">Exposto como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-194">Exposed as environment variables.</span></span>

<span data-ttu-id="7ba9f-195">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="7ba9f-196">Consulte [prefixos de cadeia de conexão](#constr) para obter informações sobre cadeias de conexão do banco de dados</span><span class="sxs-lookup"><span data-stu-id="7ba9f-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="7ba9f-197">Nomenclatura de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-197">Naming of environment variables</span></span>

<span data-ttu-id="7ba9f-198">Os nomes de variáveis de ambiente refletem a estrutura de um *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="7ba9f-199">Cada elemento na hierarquia é separado por um sublinhado duplo (preferível) ou dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="7ba9f-200">Quando a estrutura do elemento inclui uma matriz, o índice da matriz deve ser tratado como um nome de elemento adicional nesse caminho.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="7ba9f-201">Considere o seguinte *appsettings.json* arquivo e seus valores equivalentes representados como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="7ba9f-202">**variáveis de ambiente**</span><span class="sxs-lookup"><span data-stu-id="7ba9f-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="7ba9f-203">Variáveis de ambiente definidas no launchSettings.jsgerado em</span><span class="sxs-lookup"><span data-stu-id="7ba9f-203">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="7ba9f-204">As variáveis de ambiente definidas no *launchSettings.js* substituir aquelas definidas no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="7ba9f-205">Por exemplo, os modelos da Web ASP.NET Core geram uma *launchSettings.jsno* arquivo que define a configuração do ponto de extremidade como:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-205">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="7ba9f-206">Configurar o `applicationUrl` define a `ASPNETCORE_URLS` variável de ambiente e substitui os valores definidos no ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-206">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="7ba9f-207">Variáveis de ambiente de escape no Linux</span><span class="sxs-lookup"><span data-stu-id="7ba9f-207">Escape environment variables on Linux</span></span>

<span data-ttu-id="7ba9f-208">No Linux, o valor de variáveis de ambiente de URL deve ser ignorado para que `systemd` possa analisá-lo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-208">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="7ba9f-209">Use a ferramenta Linux `systemd-escape` que produz `http:--localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-209">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="7ba9f-210">Exibir variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-210">Display environment variables</span></span>

<span data-ttu-id="7ba9f-211">O código a seguir exibe as variáveis de ambiente e os valores na inicialização do aplicativo, o que pode ser útil ao depurar configurações de ambiente:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-211">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="7ba9f-212">Linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-212">Command-line</span></span>

<span data-ttu-id="7ba9f-213">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração de pares de chave-valor de argumento de linha de comando após as seguintes fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-213">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="7ba9f-214">*appsettings.json* e *appSettings*. `Environment` . arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-214">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="7ba9f-215">[Segredos do aplicativo](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-215">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="7ba9f-216">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-216">Environment variables.</span></span>

<span data-ttu-id="7ba9f-217">Por [padrão](#default), os valores de configuração definidos nos valores de configuração de substituição de linha de comando são definidos com todos os outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-217">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="7ba9f-218">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-218">Command-line arguments</span></span>

<span data-ttu-id="7ba9f-219">O comando a seguir define chaves e valores usando `=` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-219">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="7ba9f-220">O comando a seguir define chaves e valores usando `/` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-220">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="7ba9f-221">O comando a seguir define chaves e valores usando `--` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-221">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="7ba9f-222">O valor da chave:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-222">The key value:</span></span>

* <span data-ttu-id="7ba9f-223">Deve seguir `=` , ou a chave deve ter um prefixo `--` ou `/` quando o valor segue um espaço.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-223">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="7ba9f-224">Não será necessário se `=` for usado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-224">Isn't required if `=` is used.</span></span> <span data-ttu-id="7ba9f-225">Por exemplo, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-225">For example, `MySetting=`.</span></span>

<span data-ttu-id="7ba9f-226">No mesmo comando, não misture pares de chave-valor de argumento de linha de comando que usam `=` com pares de chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-226">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="7ba9f-227">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="7ba9f-227">Switch mappings</span></span>

<span data-ttu-id="7ba9f-228">Os mapeamentos de switch permitem a lógica de substituição de nome de **chave** .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-228">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="7ba9f-229">Forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-229">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="7ba9f-230">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-230">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="7ba9f-231">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-231">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="7ba9f-232">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-232">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="7ba9f-233">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-233">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="7ba9f-234">Os comutadores devem começar com `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-234">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="7ba9f-235">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-235">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="7ba9f-236">Para usar um dicionário de mapeamentos de opção, passe-o para a chamada para `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-236">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="7ba9f-237">O código a seguir mostra os valores de chave para as chaves substituídas:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-237">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-238">O comando a seguir funciona para testar a substituição da chave:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="7ba9f-239">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="7ba9f-240">A `CreateDefaultBuilder` chamada do método `AddCommandLine` não inclui opções mapeadas e não há como passar o dicionário de mapeamento de opção para `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7ba9f-241">A solução não é passar os argumentos para `CreateDefaultBuilder` , mas sim permitir que o `ConfigurationBuilder` método do método `AddCommandLine` processe os argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="7ba9f-242">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="7ba9f-242">Hierarchical configuration data</span></span>

<span data-ttu-id="7ba9f-243">A API de configuração lê dados de configuração hierárquicas mesclando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="7ba9f-244">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte  *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="7ba9f-245">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-246">A maneira preferida de ler dados de configuração hierárquicos é usar o padrão de opções.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="7ba9f-247">Para obter mais informações, consulte [associar dados de configuração hierárquica](#optpat) neste documento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="7ba9f-248">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="7ba9f-249">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="7ba9f-250">Chaves de configuração e valores</span><span class="sxs-lookup"><span data-stu-id="7ba9f-250">Configuration keys and values</span></span>

<span data-ttu-id="7ba9f-251">Chaves de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-251">Configuration keys:</span></span>

* <span data-ttu-id="7ba9f-252">Não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-252">Are case-insensitive.</span></span> <span data-ttu-id="7ba9f-253">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="7ba9f-254">Se uma chave e um valor for definido em mais de um provedor de configuração, o valor do último provedor adicionado será usado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="7ba9f-255">Para obter mais informações, consulte [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="7ba9f-256">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="7ba9f-256">Hierarchical keys</span></span>
  * <span data-ttu-id="7ba9f-257">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="7ba9f-258">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="7ba9f-259">Um sublinhado duplo, `__` , tem suporte em todas as plataformas e é automaticamente convertido em dois-pontos `:` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="7ba9f-260">Em Azure Key Vault, as chaves hierárquicas usam `--` como um separador.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="7ba9f-261">O [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration) substitui automaticamente `--` por um `:` quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="7ba9f-262">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="7ba9f-263">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="7ba9f-264">Valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-264">Configuration values:</span></span>

* <span data-ttu-id="7ba9f-265">São cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-265">Are strings.</span></span>
* <span data-ttu-id="7ba9f-266">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="7ba9f-267">Provedores de configuração</span><span class="sxs-lookup"><span data-stu-id="7ba9f-267">Configuration providers</span></span>

<span data-ttu-id="7ba9f-268">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="7ba9f-269">Provedor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-269">Provider</span></span> | <span data-ttu-id="7ba9f-270">Fornece a configuração de </span><span class="sxs-lookup"><span data-stu-id="7ba9f-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="7ba9f-271">Provedor de configuração de Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7ba9f-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="7ba9f-272">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="7ba9f-273">Provedor de configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="7ba9f-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="7ba9f-274">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="7ba9f-275">Provedor de configuração de linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="7ba9f-276">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-276">Command-line parameters</span></span> |
| [<span data-ttu-id="7ba9f-277">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="7ba9f-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="7ba9f-278">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="7ba9f-278">Custom source</span></span> |
| [<span data-ttu-id="7ba9f-279">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="7ba9f-280">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-280">Environment variables</span></span> |
| [<span data-ttu-id="7ba9f-281">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="7ba9f-282">Arquivos INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="7ba9f-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="7ba9f-283">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="7ba9f-284">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="7ba9f-284">Directory files</span></span> |
| [<span data-ttu-id="7ba9f-285">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="7ba9f-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="7ba9f-286">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="7ba9f-286">In-memory collections</span></span> |
| [<span data-ttu-id="7ba9f-287">Segredos do usuário</span><span class="sxs-lookup"><span data-stu-id="7ba9f-287">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="7ba9f-288">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="7ba9f-288">File in the user profile directory</span></span> |

<span data-ttu-id="7ba9f-289">As fontes de configuração são lidas na ordem em que seus provedores de configuração são especificados.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="7ba9f-290">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="7ba9f-291">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-291">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="7ba9f-292">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="7ba9f-292">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="7ba9f-293">Segredos do usuário</span><span class="sxs-lookup"><span data-stu-id="7ba9f-293">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="7ba9f-294">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-294">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="7ba9f-295">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-295">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="7ba9f-296">Uma prática comum é adicionar o provedor de configuração de linha de comando pela última vez em uma série de provedores para permitir que argumentos de linha de comando substituam a configuração definida pelos outros provedores.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-296">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="7ba9f-297">A sequência anterior de provedores é usada na [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-297">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="7ba9f-298">Prefixos de cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="7ba9f-298">Connection string prefixes</span></span>

<span data-ttu-id="7ba9f-299">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-299">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="7ba9f-300">Essas cadeias de conexão estão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-300">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="7ba9f-301">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo com a [configuração padrão](#default) ou quando nenhum prefixo é fornecido para `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-301">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="7ba9f-302">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="7ba9f-302">Connection string prefix</span></span> | <span data-ttu-id="7ba9f-303">Provedor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-303">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="7ba9f-304">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="7ba9f-304">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="7ba9f-305">MySQL</span><span class="sxs-lookup"><span data-stu-id="7ba9f-305">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="7ba9f-306">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-306">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="7ba9f-307">SQL Server</span><span class="sxs-lookup"><span data-stu-id="7ba9f-307">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="7ba9f-308">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-308">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="7ba9f-309">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-309">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="7ba9f-310">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-310">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="7ba9f-311">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-311">Environment variable key</span></span> | <span data-ttu-id="7ba9f-312">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="7ba9f-312">Converted configuration key</span></span> | <span data-ttu-id="7ba9f-313">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-313">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="7ba9f-314">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-314">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="7ba9f-315">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-315">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="7ba9f-316">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-316">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="7ba9f-317">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="7ba9f-318">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-318">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="7ba9f-319">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="7ba9f-320">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-320">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="7ba9f-321">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-321">File configuration provider</span></span>

<span data-ttu-id="7ba9f-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="7ba9f-323">Os seguintes provedores de configuração derivam de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-323">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="7ba9f-324">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="7ba9f-324">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="7ba9f-325">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="7ba9f-325">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="7ba9f-326">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="7ba9f-326">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="7ba9f-327">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="7ba9f-327">INI configuration provider</span></span>

<span data-ttu-id="7ba9f-328">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-328">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="7ba9f-329">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-329">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="7ba9f-330">No código anterior, as configurações no *MyIniConfig.ini* e  *MyIniConfig*. `Environment` . os arquivos *ini* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-330">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="7ba9f-331">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-331">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="7ba9f-332">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-332">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="7ba9f-333">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo de *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-333">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="7ba9f-334">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-334">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="7ba9f-335">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="7ba9f-335">JSON configuration provider</span></span>

<span data-ttu-id="7ba9f-336">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor de arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-336">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="7ba9f-337">Sobrecargas podem especificar:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-337">Overloads can specify:</span></span>

* <span data-ttu-id="7ba9f-338">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-338">Whether the file is optional.</span></span>
* <span data-ttu-id="7ba9f-339">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-339">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="7ba9f-340">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-340">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="7ba9f-341">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-341">The preceding code:</span></span>

* <span data-ttu-id="7ba9f-342">Configura o provedor de configuração JSON para carregar o *MyConfig.jsno* arquivo com as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-342">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="7ba9f-343">`optional: true`: O arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-343">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="7ba9f-344">`reloadOnChange: true` : O arquivo é recarregado quando as alterações são salvas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-344">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="7ba9f-345">Lê os [provedores de configuração padrão](#default) antes da *MyConfig.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-345">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="7ba9f-346">As configurações no *MyConfig.jsna* configuração de substituição de arquivo nos provedores de configuração padrão, incluindo o [provedor de configuração de variáveis de ambiente](#evcp) e o provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-346">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="7ba9f-347">Normalmente, você ***não*** deseja que um arquivo JSON personalizado substitua valores definidos no [provedor de configuração de variáveis de ambiente](#evcp) e no provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-347">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="7ba9f-348">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="7ba9f-349">No código anterior, as configurações na *MyConfig.jsem* e  *myconfig*. `Environment` . arquivos *JSON* :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-349">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="7ba9f-350">Substituir as configurações no *appsettings.json* e *appSettings*. `Environment` . arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-350">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="7ba9f-351">São substituídos por configurações no [provedor de configuração de variáveis de ambiente](#evcp) e no [provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-351">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="7ba9f-352">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém os seguintes  *MyConfig.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="7ba9f-353">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="7ba9f-354">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="7ba9f-354">XML configuration provider</span></span>

<span data-ttu-id="7ba9f-355">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-355">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="7ba9f-356">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-356">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="7ba9f-357">No código anterior, as configurações no *MyXMLFile.xml* e  *MyXMLFile*. `Environment` . os arquivos *XML* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-357">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="7ba9f-358">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-358">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="7ba9f-359">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-359">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="7ba9f-360">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo de *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-360">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="7ba9f-361">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-361">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-362">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-362">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="7ba9f-363">O código a seguir lê o arquivo de configuração anterior e exibe as chaves e valores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-363">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-364">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-364">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="7ba9f-365">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-365">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ba9f-366">key:attribute</span><span class="sxs-lookup"><span data-stu-id="7ba9f-366">key:attribute</span></span>
* <span data-ttu-id="7ba9f-367">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="7ba9f-367">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="7ba9f-368">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-368">Key-per-file configuration provider</span></span>

<span data-ttu-id="7ba9f-369">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-369">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="7ba9f-370">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-370">The key is the file name.</span></span> <span data-ttu-id="7ba9f-371">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-371">The value contains the file's contents.</span></span> <span data-ttu-id="7ba9f-372">O provedor de configuração de chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-372">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="7ba9f-373">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-373">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="7ba9f-374">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-374">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="7ba9f-375">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ba9f-376">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-376">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="7ba9f-377">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-377">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="7ba9f-378">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-378">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="7ba9f-379">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-379">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="7ba9f-380">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-380">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="7ba9f-381">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="7ba9f-381">Memory configuration provider</span></span>

<span data-ttu-id="7ba9f-382">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-382">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="7ba9f-383">O código a seguir adiciona uma coleção de memória ao sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-383">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="7ba9f-384">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe as configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-384">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-385">No código anterior, `config.AddInMemoryCollection(Dict)` é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-385">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="7ba9f-386">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-386">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="7ba9f-387">Consulte [associar uma matriz](#boa) para outro exemplo usando `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-387">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="7ba9f-388">Configuração de ponto de extremidade do Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ba9f-388">Kestrel endpoint configuration</span></span>

<span data-ttu-id="7ba9f-389">A configuração de ponto de extremidade específico do Kestrel substitui todas as configurações de ponto [de extremidade entre servidores](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-389">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="7ba9f-390">As configurações de ponto de extremidade entre servidores incluem:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-390">Cross-server endpoint configurations include:</span></span>

  * [<span data-ttu-id="7ba9f-391">UseUrls</span><span class="sxs-lookup"><span data-stu-id="7ba9f-391">UseUrls</span></span>](xref:fundamentals/host/web-host#server-urls)
  * <span data-ttu-id="7ba9f-392">`--urls` na [linha de comando](xref:fundamentals/configuration/index#command-line)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-392">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="7ba9f-393">A [variável de ambiente](xref:fundamentals/configuration/index#environment-variables)`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-393">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="7ba9f-394">Considere o seguinte *appsettings.json* arquivo usado em um aplicativo web ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-394">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="7ba9f-395">Quando a marcação realçada anterior é usada em um aplicativo Web ASP.NET Core ***e*** o aplicativo é iniciado na linha de comando com a seguinte configuração de ponto de extremidade entre servidores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-395">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="7ba9f-396">Kestrel associa-se ao ponto de extremidade configurado especificamente para Kestrel no *appsettings.json* arquivo ( `https://localhost:9999` ) e não `https://localhost:7777` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-396">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="7ba9f-397">Considere o ponto de extremidade específico do Kestrel configurado como uma variável de ambiente:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-397">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="7ba9f-398">Na variável de ambiente anterior, `Https` é o nome do ponto de extremidade específico do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-398">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="7ba9f-399">O *appsettings.json* arquivo anterior também define um ponto de extremidade específico de Kestrel chamado `Https` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-399">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="7ba9f-400">Por [padrão](#default-configuration), as variáveis de ambiente que usam o [provedor de configuração de variáveis de ambiente](#evcp) são lidas após *appSettings.* `Environment` *. JSON*, portanto, a variável de ambiente anterior é usada para o `Https` ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-400">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="7ba9f-401">GetValue</span><span class="sxs-lookup"><span data-stu-id="7ba9f-401">GetValue</span></span>

<span data-ttu-id="7ba9f-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o no tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-403">No código anterior, se `NumberKey` não for encontrado na configuração, o valor padrão de `99` será usado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-403">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="7ba9f-404">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="7ba9f-404">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="7ba9f-405">Para os exemplos a seguir, considere o seguinte *MySubsection.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-405">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="7ba9f-406">O código a seguir adiciona *MySubsection.js* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-406">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="7ba9f-407">GetSection</span><span class="sxs-lookup"><span data-stu-id="7ba9f-407">GetSection</span></span>

<span data-ttu-id="7ba9f-408">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retorna uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-408">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="7ba9f-409">O código a seguir retorna valores para `section1` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-409">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-410">O código a seguir retorna valores para `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-410">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-411">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-411">`GetSection` never returns `null`.</span></span> <span data-ttu-id="7ba9f-412">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-412">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="7ba9f-413">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-413">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="7ba9f-414"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-414">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="7ba9f-415">GetChildren e existe</span><span class="sxs-lookup"><span data-stu-id="7ba9f-415">GetChildren and Exists</span></span>

<span data-ttu-id="7ba9f-416">O código a seguir chama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e retorna valores para `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-416">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-417">O código anterior chama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para verificar se a seção existe:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-417">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="7ba9f-418">Associar uma matriz</span><span class="sxs-lookup"><span data-stu-id="7ba9f-418">Bind an array</span></span>

<span data-ttu-id="7ba9f-419">O [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) dá suporte a matrizes de associação a objetos usando índices de matriz em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-419">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="7ba9f-420">Qualquer formato de matriz que expõe um segmento de chave numérica é capaz de associar a matriz a uma matriz de classe [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-420">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="7ba9f-421">Considere *MyArray.js* do download de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="7ba9f-421">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="7ba9f-422">O código a seguir adiciona *MyArray.js* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-422">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="7ba9f-423">O código a seguir lê a configuração e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-423">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-424">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-424">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="7ba9f-425">Na saída anterior, o índice 3 tem valor `value40` , correspondente a `"4": "value40",` no *MyArray.jsem*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-425">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="7ba9f-426">Os índices de matriz associados são contínuos e não associados ao índice de chave de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-426">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="7ba9f-427">O associador de configuração não é capaz de ligar valores nulos ou criar entradas nulas em objetos associados</span><span class="sxs-lookup"><span data-stu-id="7ba9f-427">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="7ba9f-428">O código a seguir carrega a `array:entries` configuração com o <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> método de extensão:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-428">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="7ba9f-429">O código a seguir lê a configuração no `arrayDict` `Dictionary` e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-429">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-430">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-430">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="7ba9f-431">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-431">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="7ba9f-432">Quando os dados de configuração que contêm uma matriz são associados, os índices de matriz nas chaves de configuração são usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-432">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="7ba9f-433">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-433">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="7ba9f-434">O item de configuração ausente para &num; o índice 3 pode ser fornecido antes da associação à `ArrayExample` instância por qualquer provedor de configuração que leia o &num; par chave/valor do índice 3.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-434">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="7ba9f-435">Considere o seguinte *Value3.jsno* arquivo do download de exemplo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-435">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="7ba9f-436">O código a seguir inclui a configuração para o *Value3.jsno* e no `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-436">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="7ba9f-437">O código a seguir lê a configuração anterior e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-437">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-438">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-438">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="7ba9f-439">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-439">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="7ba9f-440">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="7ba9f-440">Custom configuration provider</span></span>

<span data-ttu-id="7ba9f-441">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-441">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="7ba9f-442">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-442">The provider has the following characteristics:</span></span>

* <span data-ttu-id="7ba9f-443">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-443">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="7ba9f-444">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-444">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="7ba9f-445">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-445">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="7ba9f-446">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-446">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="7ba9f-447">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-447">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="7ba9f-448">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-448">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="7ba9f-449">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-449">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="7ba9f-450">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-450">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="7ba9f-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="7ba9f-452">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-452">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="7ba9f-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="7ba9f-454">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-454">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="7ba9f-455">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-455">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="7ba9f-456">Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-456">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="7ba9f-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="7ba9f-458">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-458">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="7ba9f-459">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-459">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="7ba9f-460">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-460">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="7ba9f-461">Configuração de acesso na inicialização</span><span class="sxs-lookup"><span data-stu-id="7ba9f-461">Access configuration in Startup</span></span>

<span data-ttu-id="7ba9f-462">O código a seguir exibe dados de configuração em `Startup` métodos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-462">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="7ba9f-463">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-463">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="7ba9f-464">Configuração de acesso em Razor páginas</span><span class="sxs-lookup"><span data-stu-id="7ba9f-464">Access configuration in Razor Pages</span></span>

<span data-ttu-id="7ba9f-465">O código a seguir exibe os dados de configuração em uma Razor página:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-465">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="7ba9f-466">No código a seguir, `MyOptions` é adicionado ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associado à configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-466">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="7ba9f-467">A marcação a seguir usa a [`@inject`](xref:mvc/views/razor#inject) Razor diretiva para resolver e exibir os valores de opções:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-467">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="7ba9f-468">Acessar a configuração em um arquivo de exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="7ba9f-468">Access configuration in a MVC view file</span></span>

<span data-ttu-id="7ba9f-469">O código a seguir exibe os dados de configuração em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-469">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="7ba9f-470">Configurar opções com um delegado</span><span class="sxs-lookup"><span data-stu-id="7ba9f-470">Configure options with a delegate</span></span>

<span data-ttu-id="7ba9f-471">As opções configuradas em um delegado substituem valores definidos nos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-471">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="7ba9f-472">A configuração de opções com um delegado é demonstrada como o exemplo 2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-472">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="7ba9f-473">No código a seguir, um <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serviço é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-473">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7ba9f-474">Ele usa um delegado para configurar valores para `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-474">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="7ba9f-475">O código a seguir exibe os valores de opções:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-475">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="7ba9f-476">No exemplo anterior, os valores de `Option1` e `Option2` são especificados em *appsettings.json* e, em seguida, substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-476">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="7ba9f-477">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-477">Host versus app configuration</span></span>

<span data-ttu-id="7ba9f-478">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-478">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="7ba9f-479">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-479">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="7ba9f-480">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-480">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="7ba9f-481">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-481">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="7ba9f-482">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-482">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="7ba9f-483">Configuração de host padrão</span><span class="sxs-lookup"><span data-stu-id="7ba9f-483">Default host configuration</span></span>

<span data-ttu-id="7ba9f-484">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](?view=aspnetcore-2.2&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-484">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="7ba9f-485">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-485">Host configuration is provided from:</span></span>
  * <span data-ttu-id="7ba9f-486">Variáveis de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT` ) usando o [provedor de configuração de variáveis de ambiente](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-486">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="7ba9f-487">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-487">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="7ba9f-488">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-488">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="7ba9f-489">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="7ba9f-489">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="7ba9f-490">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-490">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="7ba9f-491">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-491">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="7ba9f-492">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-492">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="7ba9f-493">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-493">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="7ba9f-494">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="7ba9f-494">Other configuration</span></span>

<span data-ttu-id="7ba9f-495">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-495">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="7ba9f-496">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-496">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="7ba9f-497">*launch.jsem* / As *launchSettings.jsno* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-497">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="7ba9f-498">Em <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-498">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="7ba9f-499">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-499">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="7ba9f-500">*web.config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-500">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="7ba9f-501">As variáveis de ambiente definidas no *launchSettings.js* substituir aquelas definidas no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-501">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="7ba9f-502">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-502">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="7ba9f-503">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-503">Add configuration from an external assembly</span></span>

<span data-ttu-id="7ba9f-504">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-504">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="7ba9f-505">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-505">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ba9f-506">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7ba9f-506">Additional resources</span></span>

* [<span data-ttu-id="7ba9f-507">Código-fonte de configuração</span><span class="sxs-lookup"><span data-stu-id="7ba9f-507">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ba9f-508">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-508">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="7ba9f-509">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-509">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="7ba9f-510">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-510">Azure Key Vault</span></span>
* <span data-ttu-id="7ba9f-511">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-511">Azure App Configuration</span></span>
* <span data-ttu-id="7ba9f-512">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-512">Command-line arguments</span></span>
* <span data-ttu-id="7ba9f-513">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-513">Custom providers (installed or created)</span></span>
* <span data-ttu-id="7ba9f-514">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="7ba9f-514">Directory files</span></span>
* <span data-ttu-id="7ba9f-515">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-515">Environment variables</span></span>
* <span data-ttu-id="7ba9f-516">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="7ba9f-516">In-memory .NET objects</span></span>
* <span data-ttu-id="7ba9f-517">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="7ba9f-517">Settings files</span></span>

<span data-ttu-id="7ba9f-518">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-518">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ba9f-519">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-519">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="7ba9f-520">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-520">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="7ba9f-521">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-521">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="7ba9f-522">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-522">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="7ba9f-523">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ba9f-523">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="7ba9f-524">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-524">Host versus app configuration</span></span>

<span data-ttu-id="7ba9f-525">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-525">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="7ba9f-526">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-526">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="7ba9f-527">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-527">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="7ba9f-528">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-528">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="7ba9f-529">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-529">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="7ba9f-530">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="7ba9f-530">Other configuration</span></span>

<span data-ttu-id="7ba9f-531">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-531">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="7ba9f-532">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-532">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="7ba9f-533">*launch.jsem* / As *launchSettings.jsno* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-533">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="7ba9f-534">Em <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-534">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="7ba9f-535">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-535">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="7ba9f-536">*web.config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-536">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="7ba9f-537">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-537">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="7ba9f-538">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="7ba9f-538">Default configuration</span></span>

<span data-ttu-id="7ba9f-539">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-539">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="7ba9f-540">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-540">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="7ba9f-541">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-541">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="7ba9f-542">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-542">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="7ba9f-543">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-543">Host configuration is provided from:</span></span>
  * <span data-ttu-id="7ba9f-544">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-544">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="7ba9f-545">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-545">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="7ba9f-546">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-546">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="7ba9f-547">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-547">App configuration is provided from:</span></span>
  * <span data-ttu-id="7ba9f-548">*appsettings.json* usando o [provedor de configuração de arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-548">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="7ba9f-549">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-549">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="7ba9f-550">[Segredos do usuário](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-550">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="7ba9f-551">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-551">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="7ba9f-552">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-552">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="7ba9f-553">Segurança</span><span class="sxs-lookup"><span data-stu-id="7ba9f-553">Security</span></span>

<span data-ttu-id="7ba9f-554">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-554">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="7ba9f-555">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-555">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="7ba9f-556">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-556">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="7ba9f-557">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-557">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="7ba9f-558">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-558">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="7ba9f-559"><xref:security/app-secrets>: Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-559"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="7ba9f-560">A ferramenta Gerenciador de segredo usa o provedor de configuração de arquivo para armazenar segredos de usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-560">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="7ba9f-561">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-561">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="7ba9f-562">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="7ba9f-563">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-563">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="7ba9f-564">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="7ba9f-564">Hierarchical configuration data</span></span>

<span data-ttu-id="7ba9f-565">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-565">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="7ba9f-566">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-566">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="7ba9f-567">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-567">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="7ba9f-568">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-568">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="7ba9f-569">section0:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-569">section0:key0</span></span>
* <span data-ttu-id="7ba9f-570">section0:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-570">section0:key1</span></span>
* <span data-ttu-id="7ba9f-571">section1:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-571">section1:key0</span></span>
* <span data-ttu-id="7ba9f-572">section1:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-572">section1:key1</span></span>

<span data-ttu-id="7ba9f-573">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="7ba9f-574">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-574">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="7ba9f-575">Convenções</span><span class="sxs-lookup"><span data-stu-id="7ba9f-575">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="7ba9f-576">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="7ba9f-576">Sources and providers</span></span>

<span data-ttu-id="7ba9f-577">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-577">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="7ba9f-578">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-578">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="7ba9f-579">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-579">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="7ba9f-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7ba9f-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetado em uma Razor página <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="7ba9f-582">Nos exemplos a seguir, o `_config` campo é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-582">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="7ba9f-583">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-583">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="7ba9f-584">simétricas</span><span class="sxs-lookup"><span data-stu-id="7ba9f-584">Keys</span></span>

<span data-ttu-id="7ba9f-585">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-585">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="7ba9f-586">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-586">Keys are case-insensitive.</span></span> <span data-ttu-id="7ba9f-587">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-587">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="7ba9f-588">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-588">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="7ba9f-589">Para obter mais informações sobre chaves JSON duplicadas, consulte [este problema do GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-589">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="7ba9f-590">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="7ba9f-590">Hierarchical keys</span></span>
  * <span data-ttu-id="7ba9f-591">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-591">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="7ba9f-592">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-592">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="7ba9f-593">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-593">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="7ba9f-594">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-594">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="7ba9f-595">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-595">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="7ba9f-596">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-596">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="7ba9f-597">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-597">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="7ba9f-598">Valores</span><span class="sxs-lookup"><span data-stu-id="7ba9f-598">Values</span></span>

<span data-ttu-id="7ba9f-599">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-599">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="7ba9f-600">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-600">Values are strings.</span></span>
* <span data-ttu-id="7ba9f-601">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-601">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="7ba9f-602">Provedores</span><span class="sxs-lookup"><span data-stu-id="7ba9f-602">Providers</span></span>

<span data-ttu-id="7ba9f-603">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-603">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="7ba9f-604">Provedor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-604">Provider</span></span> | <span data-ttu-id="7ba9f-605">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="7ba9f-605">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="7ba9f-606">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-606">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="7ba9f-607">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-607">Azure Key Vault</span></span> |
| <span data-ttu-id="7ba9f-608">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-608">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="7ba9f-609">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-609">Azure App Configuration</span></span> |
| [<span data-ttu-id="7ba9f-610">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-610">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="7ba9f-611">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-611">Command-line parameters</span></span> |
| [<span data-ttu-id="7ba9f-612">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="7ba9f-612">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="7ba9f-613">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="7ba9f-613">Custom source</span></span> |
| [<span data-ttu-id="7ba9f-614">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-614">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="7ba9f-615">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-615">Environment variables</span></span> |
| [<span data-ttu-id="7ba9f-616">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-616">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="7ba9f-617">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-617">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="7ba9f-618">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-618">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="7ba9f-619">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="7ba9f-619">Directory files</span></span> |
| [<span data-ttu-id="7ba9f-620">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="7ba9f-620">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="7ba9f-621">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="7ba9f-621">In-memory collections</span></span> |
| <span data-ttu-id="7ba9f-622">[Segredos do usuário](xref:security/app-secrets) (tópicos de *segurança* )</span><span class="sxs-lookup"><span data-stu-id="7ba9f-622">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="7ba9f-623">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="7ba9f-623">File in the user profile directory</span></span> |

<span data-ttu-id="7ba9f-624">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-624">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="7ba9f-625">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-625">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="7ba9f-626">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-626">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="7ba9f-627">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-627">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="7ba9f-628">Arquivos ( *appsettings.json* , *appSettings. { Ambiente}. JSON*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-628">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="7ba9f-629">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7ba9f-629">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="7ba9f-630">[Segredos do usuário](xref:security/app-secrets) (somente ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-630">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="7ba9f-631">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-631">Environment variables</span></span>
1. <span data-ttu-id="7ba9f-632">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-632">Command-line arguments</span></span>

<span data-ttu-id="7ba9f-633">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-633">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="7ba9f-634">A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com o `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-634">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7ba9f-635">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-635">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="7ba9f-636">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="7ba9f-636">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="7ba9f-637">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-637">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="7ba9f-638">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="7ba9f-638">ConfigureAppConfiguration</span></span>

<span data-ttu-id="7ba9f-639">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-639">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="7ba9f-640">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-640">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="7ba9f-641">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-641">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="7ba9f-642">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="7ba9f-642">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="7ba9f-643">Para remover os provedores adicionados pelo `CreateDefaultBuilder` , chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-643">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="7ba9f-644">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-644">Consume configuration during app startup</span></span>

<span data-ttu-id="7ba9f-645">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-645">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7ba9f-646">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-646">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="7ba9f-647">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="7ba9f-647">Command-line Configuration Provider</span></span>

<span data-ttu-id="7ba9f-648">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-648">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="7ba9f-649">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-649">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ba9f-650">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-650">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="7ba9f-651">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-651">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="7ba9f-652">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-652">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="7ba9f-653">Configuração opcional de *appsettings.json* e *appSettings. { Arquivos. JSON do ambiente}* .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-653">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="7ba9f-654">[Segredos do usuário](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-654">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="7ba9f-655">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-655">Environment variables.</span></span>

<span data-ttu-id="7ba9f-656">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-656">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="7ba9f-657">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-657">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="7ba9f-658">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-658">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="7ba9f-659">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-659">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="7ba9f-660">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-660">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7ba9f-661">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-661">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="7ba9f-662">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="7ba9f-662">**Example**</span></span>

<span data-ttu-id="7ba9f-663">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-663">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="7ba9f-664">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-664">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="7ba9f-665">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-665">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="7ba9f-666">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-666">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="7ba9f-667">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-667">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="7ba9f-668">Argumentos</span><span class="sxs-lookup"><span data-stu-id="7ba9f-668">Arguments</span></span>

<span data-ttu-id="7ba9f-669">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-669">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="7ba9f-670">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-670">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="7ba9f-671">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="7ba9f-671">Key prefix</span></span>               | <span data-ttu-id="7ba9f-672">Exemplo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-672">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="7ba9f-673">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-673">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="7ba9f-674">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-674">Two dashes (`--`)</span></span>        | <span data-ttu-id="7ba9f-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="7ba9f-676">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="7ba9f-676">Forward slash (`/`)</span></span>      | <span data-ttu-id="7ba9f-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="7ba9f-678">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-678">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="7ba9f-679">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-679">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="7ba9f-680">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="7ba9f-680">Switch mappings</span></span>

<span data-ttu-id="7ba9f-681">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-681">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="7ba9f-682">Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-682">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="7ba9f-683">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-683">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="7ba9f-684">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-684">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="7ba9f-685">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-685">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="7ba9f-686">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-686">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="7ba9f-687">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-687">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="7ba9f-688">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-688">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="7ba9f-689">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-689">Create a switch mappings dictionary.</span></span> <span data-ttu-id="7ba9f-690">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-690">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="7ba9f-691">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-691">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="7ba9f-692">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-692">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="7ba9f-693">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-693">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7ba9f-694">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-694">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="7ba9f-695">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-695">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="7ba9f-696">Chave</span><span class="sxs-lookup"><span data-stu-id="7ba9f-696">Key</span></span>       | <span data-ttu-id="7ba9f-697">Valor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-697">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="7ba9f-698">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-698">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="7ba9f-699">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-699">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="7ba9f-700">Chave</span><span class="sxs-lookup"><span data-stu-id="7ba9f-700">Key</span></span>               | <span data-ttu-id="7ba9f-701">Valor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-701">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="7ba9f-702">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-702">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="7ba9f-703">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-703">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="7ba9f-704">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-704">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="7ba9f-705">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="7ba9f-706">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-706">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="7ba9f-707">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-707">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="7ba9f-708">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-708">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="7ba9f-709">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-709">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="7ba9f-710">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-710">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="7ba9f-711">Configuração opcional de *appsettings.json* e *appSettings. { Arquivos. JSON do ambiente}* .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-711">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="7ba9f-712">[Segredos do usuário](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-712">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="7ba9f-713">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-713">Command-line arguments.</span></span>

<span data-ttu-id="7ba9f-714">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-714">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="7ba9f-715">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-715">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="7ba9f-716">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-716">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="7ba9f-717">Chame `AddEnvironmentVariables` Last para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-717">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="7ba9f-718">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="7ba9f-718">**Example**</span></span>

<span data-ttu-id="7ba9f-719">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-719">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="7ba9f-720">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-720">Run the sample app.</span></span> <span data-ttu-id="7ba9f-721">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-721">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="7ba9f-722">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-722">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="7ba9f-723">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-723">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="7ba9f-724">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-724">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="7ba9f-725">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-725">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="7ba9f-726">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-726">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="7ba9f-727">Prefixos</span><span class="sxs-lookup"><span data-stu-id="7ba9f-727">Prefixes</span></span>

<span data-ttu-id="7ba9f-728">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o `AddEnvironmentVariables` método.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-728">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="7ba9f-729">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-729">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="7ba9f-730">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-730">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="7ba9f-731">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-731">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="7ba9f-732">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-732">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="7ba9f-733">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="7ba9f-733">**Connection string prefixes**</span></span>

<span data-ttu-id="7ba9f-734">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-734">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="7ba9f-735">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-735">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="7ba9f-736">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="7ba9f-736">Connection string prefix</span></span> | <span data-ttu-id="7ba9f-737">Provedor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-737">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="7ba9f-738">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="7ba9f-738">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="7ba9f-739">MySQL</span><span class="sxs-lookup"><span data-stu-id="7ba9f-739">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="7ba9f-740">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba9f-740">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="7ba9f-741">SQL Server</span><span class="sxs-lookup"><span data-stu-id="7ba9f-741">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="7ba9f-742">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-742">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="7ba9f-743">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-743">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="7ba9f-744">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-744">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="7ba9f-745">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba9f-745">Environment variable key</span></span> | <span data-ttu-id="7ba9f-746">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="7ba9f-746">Converted configuration key</span></span> | <span data-ttu-id="7ba9f-747">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-747">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="7ba9f-748">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-748">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="7ba9f-749">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-749">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="7ba9f-750">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-750">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="7ba9f-751">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="7ba9f-752">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-752">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="7ba9f-753">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="7ba9f-754">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-754">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="7ba9f-755">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="7ba9f-755">**Example**</span></span>

<span data-ttu-id="7ba9f-756">Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-756">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="7ba9f-757">Nome: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-757">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="7ba9f-758">Valor: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-758">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="7ba9f-759">Se `IConfiguration` for injetado e atribuído a um campo chamado `_config` , leia o valor:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-759">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="7ba9f-760">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-760">File Configuration Provider</span></span>

<span data-ttu-id="7ba9f-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="7ba9f-762">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-762">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="7ba9f-763">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="7ba9f-763">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="7ba9f-764">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="7ba9f-764">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="7ba9f-765">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="7ba9f-765">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="7ba9f-766">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="7ba9f-766">INI Configuration Provider</span></span>

<span data-ttu-id="7ba9f-767">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-767">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="7ba9f-768">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-768">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ba9f-769">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-769">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="7ba9f-770">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-770">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ba9f-771">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-771">Whether the file is optional.</span></span>
* <span data-ttu-id="7ba9f-772">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-772">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="7ba9f-773">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-773">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="7ba9f-774">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="7ba9f-775">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-775">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="7ba9f-776">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-776">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ba9f-777">section0:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-777">section0:key0</span></span>
* <span data-ttu-id="7ba9f-778">section0:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-778">section0:key1</span></span>
* <span data-ttu-id="7ba9f-779">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="7ba9f-779">section1:subsection:key</span></span>
* <span data-ttu-id="7ba9f-780">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="7ba9f-780">section2:subsection0:key</span></span>
* <span data-ttu-id="7ba9f-781">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="7ba9f-781">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="7ba9f-782">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="7ba9f-782">JSON Configuration Provider</span></span>

<span data-ttu-id="7ba9f-783">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-783">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="7ba9f-784">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-784">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ba9f-785">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-785">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ba9f-786">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-786">Whether the file is optional.</span></span>
* <span data-ttu-id="7ba9f-787">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-787">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="7ba9f-788">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-788">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="7ba9f-789">`AddJsonFile` é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com o `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-789">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7ba9f-790">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-790">The method is called to load configuration from:</span></span>

* <span data-ttu-id="7ba9f-791">*appsettings.json*: Este arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-791">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="7ba9f-792">A versão de ambiente do arquivo pode substituir os valores fornecidos pelo *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-792">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="7ba9f-793">*appSettings. {Environment}. JSON*: a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-793">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="7ba9f-794">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-794">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="7ba9f-795">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-795">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="7ba9f-796">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-796">Environment variables.</span></span>
* <span data-ttu-id="7ba9f-797">[Segredos do usuário](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-797">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="7ba9f-798">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-798">Command-line arguments.</span></span>

<span data-ttu-id="7ba9f-799">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-799">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="7ba9f-800">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-800">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="7ba9f-801">Chame `ConfigureAppConfiguration` ao compilar o host para especificar a configuração do aplicativo para arquivos diferentes de *appsettings.json* e *appSettings. { Ambiente}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="7ba9f-802">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="7ba9f-802">**Example**</span></span>

<span data-ttu-id="7ba9f-803">O aplicativo de exemplo aproveita o método de conveniência estática `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-803">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="7ba9f-804">A primeira chamada para `AddJsonFile` carrega a configuração de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="7ba9f-804">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="7ba9f-805">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-805">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="7ba9f-806">Para *appsettings.Development.js* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-806">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="7ba9f-807">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-807">Run the sample app.</span></span> <span data-ttu-id="7ba9f-808">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-808">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="7ba9f-809">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-809">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="7ba9f-810">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-810">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="7ba9f-811">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-811">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="7ba9f-812">Abra as *Propriedades/launchSettings.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-812">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="7ba9f-813">No `ConfigurationSample` perfil, altere o valor da variável de `ASPNETCORE_ENVIRONMENT` ambiente para `Production` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-813">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="7ba9f-814">Salve o arquivo e execute o aplicativo com o `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-814">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="7ba9f-815">As configurações no *appsettings.Development.js* não substituem mais as configurações em *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-815">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="7ba9f-816">O nível de log para a chave `Logging:LogLevel:Default` é `Warning` .</span><span class="sxs-lookup"><span data-stu-id="7ba9f-816">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="7ba9f-817">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="7ba9f-817">XML Configuration Provider</span></span>

<span data-ttu-id="7ba9f-818">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-818">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="7ba9f-819">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-819">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ba9f-820">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-820">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ba9f-821">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-821">Whether the file is optional.</span></span>
* <span data-ttu-id="7ba9f-822">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-822">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="7ba9f-823">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-823">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="7ba9f-824">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-824">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="7ba9f-825">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-825">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="7ba9f-826">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-826">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="7ba9f-827">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-827">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="7ba9f-828">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-828">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ba9f-829">section0:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-829">section0:key0</span></span>
* <span data-ttu-id="7ba9f-830">section0:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-830">section0:key1</span></span>
* <span data-ttu-id="7ba9f-831">section1:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-831">section1:key0</span></span>
* <span data-ttu-id="7ba9f-832">section1:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-832">section1:key1</span></span>

<span data-ttu-id="7ba9f-833">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-833">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="7ba9f-834">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-834">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ba9f-835">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-835">section:section0:key:key0</span></span>
* <span data-ttu-id="7ba9f-836">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-836">section:section0:key:key1</span></span>
* <span data-ttu-id="7ba9f-837">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-837">section:section1:key:key0</span></span>
* <span data-ttu-id="7ba9f-838">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-838">section:section1:key:key1</span></span>

<span data-ttu-id="7ba9f-839">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-839">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="7ba9f-840">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-840">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ba9f-841">key:attribute</span><span class="sxs-lookup"><span data-stu-id="7ba9f-841">key:attribute</span></span>
* <span data-ttu-id="7ba9f-842">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="7ba9f-842">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="7ba9f-843">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-843">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="7ba9f-844">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-844">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="7ba9f-845">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-845">The key is the file name.</span></span> <span data-ttu-id="7ba9f-846">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-846">The value contains the file's contents.</span></span> <span data-ttu-id="7ba9f-847">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-847">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="7ba9f-848">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-848">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="7ba9f-849">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-849">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="7ba9f-850">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-850">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ba9f-851">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-851">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="7ba9f-852">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-852">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="7ba9f-853">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-853">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="7ba9f-854">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-854">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="7ba9f-855">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-855">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="7ba9f-856">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="7ba9f-856">Memory Configuration Provider</span></span>

<span data-ttu-id="7ba9f-857">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-857">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="7ba9f-858">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-858">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ba9f-859">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-859">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="7ba9f-860">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-860">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="7ba9f-861">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-861">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="7ba9f-862">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-862">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="7ba9f-863">GetValue</span><span class="sxs-lookup"><span data-stu-id="7ba9f-863">GetValue</span></span>

<span data-ttu-id="7ba9f-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="7ba9f-865">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-865">An overload accepts a default value.</span></span>

<span data-ttu-id="7ba9f-866">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-866">The following example:</span></span>

* <span data-ttu-id="7ba9f-867">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-867">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="7ba9f-868">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-868">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="7ba9f-869">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-869">Types the value as an `int`.</span></span>
* <span data-ttu-id="7ba9f-870">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-870">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="7ba9f-871">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="7ba9f-871">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="7ba9f-872">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-872">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="7ba9f-873">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-873">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="7ba9f-874">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-874">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="7ba9f-875">section0:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-875">section0:key0</span></span>
* <span data-ttu-id="7ba9f-876">section0:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-876">section0:key1</span></span>
* <span data-ttu-id="7ba9f-877">section1:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-877">section1:key0</span></span>
* <span data-ttu-id="7ba9f-878">section1:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-878">section1:key1</span></span>
* <span data-ttu-id="7ba9f-879">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-879">section2:subsection0:key0</span></span>
* <span data-ttu-id="7ba9f-880">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-880">section2:subsection0:key1</span></span>
* <span data-ttu-id="7ba9f-881">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-881">section2:subsection1:key0</span></span>
* <span data-ttu-id="7ba9f-882">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-882">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="7ba9f-883">GetSection</span><span class="sxs-lookup"><span data-stu-id="7ba9f-883">GetSection</span></span>

<span data-ttu-id="7ba9f-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="7ba9f-885">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-885">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="7ba9f-886">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-886">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="7ba9f-887">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-887">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="7ba9f-888">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-888">`GetSection` never returns `null`.</span></span> <span data-ttu-id="7ba9f-889">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-889">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="7ba9f-890">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-890">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="7ba9f-891"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-891">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="7ba9f-892">GetChildren</span><span class="sxs-lookup"><span data-stu-id="7ba9f-892">GetChildren</span></span>

<span data-ttu-id="7ba9f-893">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-893">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="7ba9f-894">Exists</span><span class="sxs-lookup"><span data-stu-id="7ba9f-894">Exists</span></span>

<span data-ttu-id="7ba9f-895">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-895">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="7ba9f-896">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-896">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="7ba9f-897">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="7ba9f-897">Bind to an object graph</span></span>

<span data-ttu-id="7ba9f-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="7ba9f-899">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-899">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="7ba9f-900">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="7ba9f-900">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="7ba9f-901">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-901">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="7ba9f-902">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-902">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="7ba9f-903">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-903">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="7ba9f-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="7ba9f-905">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-905">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="7ba9f-906">O código a seguir mostra como usar `Get<T>` o com o exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-906">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="7ba9f-907">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="7ba9f-907">Bind an array to a class</span></span>

<span data-ttu-id="7ba9f-908">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="7ba9f-908">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="7ba9f-909">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-909">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="7ba9f-910">Qualquer formato de matriz que expõe um segmento de chave numérico ( `:0:` , `:1:` , &hellip; `:{n}:` ) é capaz de associar a matriz a uma matriz de classe poco.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-910">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba9f-911">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-911">Binding is provided by convention.</span></span> <span data-ttu-id="7ba9f-912">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-912">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="7ba9f-913">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="7ba9f-913">**In-memory array processing**</span></span>

<span data-ttu-id="7ba9f-914">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-914">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="7ba9f-915">Chave</span><span class="sxs-lookup"><span data-stu-id="7ba9f-915">Key</span></span>             | <span data-ttu-id="7ba9f-916">Valor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-916">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="7ba9f-917">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-917">array:entries:0</span></span> | <span data-ttu-id="7ba9f-918">value0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-918">value0</span></span> |
| <span data-ttu-id="7ba9f-919">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-919">array:entries:1</span></span> | <span data-ttu-id="7ba9f-920">value1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-920">value1</span></span> |
| <span data-ttu-id="7ba9f-921">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="7ba9f-921">array:entries:2</span></span> | <span data-ttu-id="7ba9f-922">value2</span><span class="sxs-lookup"><span data-stu-id="7ba9f-922">value2</span></span> |
| <span data-ttu-id="7ba9f-923">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="7ba9f-923">array:entries:4</span></span> | <span data-ttu-id="7ba9f-924">value4</span><span class="sxs-lookup"><span data-stu-id="7ba9f-924">value4</span></span> |
| <span data-ttu-id="7ba9f-925">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="7ba9f-925">array:entries:5</span></span> | <span data-ttu-id="7ba9f-926">value5</span><span class="sxs-lookup"><span data-stu-id="7ba9f-926">value5</span></span> |

<span data-ttu-id="7ba9f-927">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-927">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="7ba9f-928">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-928">The array skips a value for index &num;3.</span></span> <span data-ttu-id="7ba9f-929">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-929">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="7ba9f-930">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-930">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="7ba9f-931">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-931">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="7ba9f-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) a sintaxe também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="7ba9f-933">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-933">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="7ba9f-934">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="7ba9f-935">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="7ba9f-936">0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-936">0</span></span>                            | <span data-ttu-id="7ba9f-937">value0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-937">value0</span></span>                       |
| <span data-ttu-id="7ba9f-938">1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-938">1</span></span>                            | <span data-ttu-id="7ba9f-939">value1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-939">value1</span></span>                       |
| <span data-ttu-id="7ba9f-940">2</span><span class="sxs-lookup"><span data-stu-id="7ba9f-940">2</span></span>                            | <span data-ttu-id="7ba9f-941">value2</span><span class="sxs-lookup"><span data-stu-id="7ba9f-941">value2</span></span>                       |
| <span data-ttu-id="7ba9f-942">3</span><span class="sxs-lookup"><span data-stu-id="7ba9f-942">3</span></span>                            | <span data-ttu-id="7ba9f-943">value4</span><span class="sxs-lookup"><span data-stu-id="7ba9f-943">value4</span></span>                       |
| <span data-ttu-id="7ba9f-944">4</span><span class="sxs-lookup"><span data-stu-id="7ba9f-944">4</span></span>                            | <span data-ttu-id="7ba9f-945">value5</span><span class="sxs-lookup"><span data-stu-id="7ba9f-945">value5</span></span>                       |

<span data-ttu-id="7ba9f-946">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-946">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="7ba9f-947">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-947">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="7ba9f-948">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-948">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="7ba9f-949">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-949">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="7ba9f-950">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-950">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="7ba9f-951">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-951">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="7ba9f-952">Em `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-952">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="7ba9f-953">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-953">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="7ba9f-954">Chave</span><span class="sxs-lookup"><span data-stu-id="7ba9f-954">Key</span></span>             | <span data-ttu-id="7ba9f-955">Valor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-955">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="7ba9f-956">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="7ba9f-956">array:entries:3</span></span> | <span data-ttu-id="7ba9f-957">value3</span><span class="sxs-lookup"><span data-stu-id="7ba9f-957">value3</span></span> |

<span data-ttu-id="7ba9f-958">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-958">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="7ba9f-959">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-959">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="7ba9f-960">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-960">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="7ba9f-961">0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-961">0</span></span>                            | <span data-ttu-id="7ba9f-962">value0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-962">value0</span></span>                       |
| <span data-ttu-id="7ba9f-963">1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-963">1</span></span>                            | <span data-ttu-id="7ba9f-964">value1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-964">value1</span></span>                       |
| <span data-ttu-id="7ba9f-965">2</span><span class="sxs-lookup"><span data-stu-id="7ba9f-965">2</span></span>                            | <span data-ttu-id="7ba9f-966">value2</span><span class="sxs-lookup"><span data-stu-id="7ba9f-966">value2</span></span>                       |
| <span data-ttu-id="7ba9f-967">3</span><span class="sxs-lookup"><span data-stu-id="7ba9f-967">3</span></span>                            | <span data-ttu-id="7ba9f-968">value3</span><span class="sxs-lookup"><span data-stu-id="7ba9f-968">value3</span></span>                       |
| <span data-ttu-id="7ba9f-969">4</span><span class="sxs-lookup"><span data-stu-id="7ba9f-969">4</span></span>                            | <span data-ttu-id="7ba9f-970">value4</span><span class="sxs-lookup"><span data-stu-id="7ba9f-970">value4</span></span>                       |
| <span data-ttu-id="7ba9f-971">5</span><span class="sxs-lookup"><span data-stu-id="7ba9f-971">5</span></span>                            | <span data-ttu-id="7ba9f-972">value5</span><span class="sxs-lookup"><span data-stu-id="7ba9f-972">value5</span></span>                       |

<span data-ttu-id="7ba9f-973">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="7ba9f-973">**JSON array processing**</span></span>

<span data-ttu-id="7ba9f-974">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-974">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="7ba9f-975">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-975">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="7ba9f-976">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-976">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="7ba9f-977">Chave</span><span class="sxs-lookup"><span data-stu-id="7ba9f-977">Key</span></span>                     | <span data-ttu-id="7ba9f-978">Valor</span><span class="sxs-lookup"><span data-stu-id="7ba9f-978">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="7ba9f-979">json_array:key</span><span class="sxs-lookup"><span data-stu-id="7ba9f-979">json_array:key</span></span>          | <span data-ttu-id="7ba9f-980">valueA</span><span class="sxs-lookup"><span data-stu-id="7ba9f-980">valueA</span></span> |
| <span data-ttu-id="7ba9f-981">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-981">json_array:subsection:0</span></span> | <span data-ttu-id="7ba9f-982">valueB</span><span class="sxs-lookup"><span data-stu-id="7ba9f-982">valueB</span></span> |
| <span data-ttu-id="7ba9f-983">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-983">json_array:subsection:1</span></span> | <span data-ttu-id="7ba9f-984">valueC</span><span class="sxs-lookup"><span data-stu-id="7ba9f-984">valueC</span></span> |
| <span data-ttu-id="7ba9f-985">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="7ba9f-985">json_array:subsection:2</span></span> | <span data-ttu-id="7ba9f-986">valueD</span><span class="sxs-lookup"><span data-stu-id="7ba9f-986">valueD</span></span> |

<span data-ttu-id="7ba9f-987">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-987">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="7ba9f-988">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-988">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="7ba9f-989">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-989">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="7ba9f-990">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-990">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="7ba9f-991">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="7ba9f-991">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="7ba9f-992">0</span><span class="sxs-lookup"><span data-stu-id="7ba9f-992">0</span></span>                                   | <span data-ttu-id="7ba9f-993">valueB</span><span class="sxs-lookup"><span data-stu-id="7ba9f-993">valueB</span></span>                              |
| <span data-ttu-id="7ba9f-994">1</span><span class="sxs-lookup"><span data-stu-id="7ba9f-994">1</span></span>                                   | <span data-ttu-id="7ba9f-995">valueC</span><span class="sxs-lookup"><span data-stu-id="7ba9f-995">valueC</span></span>                              |
| <span data-ttu-id="7ba9f-996">2</span><span class="sxs-lookup"><span data-stu-id="7ba9f-996">2</span></span>                                   | <span data-ttu-id="7ba9f-997">valueD</span><span class="sxs-lookup"><span data-stu-id="7ba9f-997">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="7ba9f-998">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="7ba9f-998">Custom configuration provider</span></span>

<span data-ttu-id="7ba9f-999">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-999">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="7ba9f-1000">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1000">The provider has the following characteristics:</span></span>

* <span data-ttu-id="7ba9f-1001">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1001">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="7ba9f-1002">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1002">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="7ba9f-1003">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1003">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="7ba9f-1004">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1004">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="7ba9f-1005">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1005">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="7ba9f-1006">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1006">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="7ba9f-1007">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1007">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="7ba9f-1008">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1008">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="7ba9f-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="7ba9f-1010">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1010">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="7ba9f-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="7ba9f-1012">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1012">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="7ba9f-1013">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1013">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="7ba9f-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="7ba9f-1015">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1015">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="7ba9f-1016">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1016">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="7ba9f-1017">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1017">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="7ba9f-1018">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1018">Access configuration during startup</span></span>

<span data-ttu-id="7ba9f-1019">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1019">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7ba9f-1020">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1020">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="7ba9f-1021">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1021">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="7ba9f-1022">Acessar a configuração em uma Razor página de páginas ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1022">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="7ba9f-1023">Para acessar as definições de configuração em uma Razor página de páginas ou em uma exibição do MVC, adicione uma [diretiva using](xref:mvc/views/razor#using) ([referência C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [ namespaceMicrosoft.Extensions.Configuração](xref:Microsoft.Extensions.Configuration) e insira <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1023">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="7ba9f-1024">Em uma Razor página de páginas:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1024">In a Razor Pages page:</span></span>

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

<span data-ttu-id="7ba9f-1025">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1025">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="7ba9f-1026">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1026">Add configuration from an external assembly</span></span>

<span data-ttu-id="7ba9f-1027">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1027">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="7ba9f-1028">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1028">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ba9f-1029">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7ba9f-1029">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
