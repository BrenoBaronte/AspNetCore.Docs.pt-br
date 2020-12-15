---
title: Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar e recuperar informações confidenciais durante o desenvolvimento de um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
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
uid: security/app-secrets
ms.openlocfilehash: 63032895ce45ad096612a8c39a2709628c12790f
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486194"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="64b49-103">Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64b49-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="64b49-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="64b49-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="64b49-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="64b49-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="64b49-106">Este documento explica como gerenciar dados confidenciais para um aplicativo ASP.NET Core em um computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="64b49-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="64b49-107">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="64b49-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="64b49-108">Os segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="64b49-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="64b49-109">Os segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="64b49-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="64b49-110">Em vez disso, os segredos de produção devem ser acessados por meio de um meio controlado, como variáveis de ambiente ou Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="64b49-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="64b49-111">Você pode armazenar e proteger os segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="64b49-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="64b49-112">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="64b49-112">Environment variables</span></span>

<span data-ttu-id="64b49-113">As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="64b49-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="64b49-114">Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="64b49-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="64b49-115">Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada.</span><span class="sxs-lookup"><span data-stu-id="64b49-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="64b49-116">Uma cadeia de conexão de banco de dados padrão é incluída no arquivo do projeto *appsettings.json* com a chave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="64b49-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="64b49-117">A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="64b49-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="64b49-118">Durante a implantação do aplicativo, o `DefaultConnection` valor da chave pode ser substituído por um valor de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="64b49-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="64b49-119">A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="64b49-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="64b49-120">Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="64b49-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="64b49-121">Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="64b49-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="64b49-122">Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="64b49-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="64b49-123">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-123">Secret Manager</span></span>

<span data-ttu-id="64b49-124">A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="64b49-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="64b49-125">Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="64b49-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="64b49-126">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="64b49-127">Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos.</span><span class="sxs-lookup"><span data-stu-id="64b49-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="64b49-128">Os segredos do aplicativo não são verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="64b49-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="64b49-129">A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável.</span><span class="sxs-lookup"><span data-stu-id="64b49-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="64b49-130">É apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="64b49-130">It's for development purposes only.</span></span> <span data-ttu-id="64b49-131">As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="64b49-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="64b49-132">Como funciona a ferramenta Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="64b49-133">A ferramenta Gerenciador de segredo oculta os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="64b49-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="64b49-134">Você pode usar a ferramenta sem conhecer esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="64b49-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="64b49-135">Os valores são armazenados em um arquivo JSON na pasta de perfil de usuário do computador local:</span><span class="sxs-lookup"><span data-stu-id="64b49-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="64b49-136">Windows</span><span class="sxs-lookup"><span data-stu-id="64b49-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="64b49-137">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="64b49-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="64b49-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="64b49-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="64b49-139">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="64b49-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="64b49-140">Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo `UserSecretsId` valor especificado no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="64b49-141">Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="64b49-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="64b49-142">Esses detalhes de implementação podem ser alterados.</span><span class="sxs-lookup"><span data-stu-id="64b49-142">These implementation details may change.</span></span> <span data-ttu-id="64b49-143">Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.</span><span class="sxs-lookup"><span data-stu-id="64b49-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="64b49-144">Habilitar o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="64b49-144">Enable secret storage</span></span>

<span data-ttu-id="64b49-145">A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="64b49-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="64b49-146">A ferramenta Gerenciador de segredo inclui um `init` comando no SDK do .NET Core 3.0.100 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="64b49-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="64b49-147">Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="64b49-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="64b49-148">O comando anterior adiciona um `UserSecretsId` elemento dentro de um `PropertyGroup` do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="64b49-149">Por padrão, o texto interno de `UserSecretsId` é um GUID.</span><span class="sxs-lookup"><span data-stu-id="64b49-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="64b49-150">O texto interno é arbitrário, mas é exclusivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="64b49-151">No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="64b49-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="64b49-152">Esse gesto adiciona um `UserSecretsId` elemento, populado com um GUID, ao arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="64b49-153">Definir um segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-153">Set a secret</span></span>

<span data-ttu-id="64b49-154">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="64b49-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="64b49-155">O segredo é associado ao valor do projeto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="64b49-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="64b49-156">Por exemplo, execute o seguinte comando do diretório no qual o arquivo de projeto existe:</span><span class="sxs-lookup"><span data-stu-id="64b49-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="64b49-157">No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma `ServiceApiKey` propriedade.</span><span class="sxs-lookup"><span data-stu-id="64b49-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="64b49-158">A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="64b49-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="64b49-159">Use a `--project` opção para fornecer o caminho do sistema de arquivos no qual o arquivo de projeto existe.</span><span class="sxs-lookup"><span data-stu-id="64b49-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="64b49-160">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64b49-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="64b49-161">Nivelamento de estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64b49-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="64b49-162">O gesto **gerenciar segredos do usuário** do Visual Studio abre uma *secrets.jsno* arquivo no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="64b49-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="64b49-163">Substitua o conteúdo de *secrets.js* com os pares chave-valor a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="64b49-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="64b49-164">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64b49-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="64b49-165">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="64b49-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="64b49-166">Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` a execução recolhe o `Movies` literal do objeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="64b49-167">O arquivo modificado é semelhante ao JSON a seguir:</span><span class="sxs-lookup"><span data-stu-id="64b49-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="64b49-168">Definir vários segredos</span><span class="sxs-lookup"><span data-stu-id="64b49-168">Set multiple secrets</span></span>

<span data-ttu-id="64b49-169">Um lote de segredos pode ser definido por meio de tubulação JSON para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="64b49-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="64b49-170">No exemplo a seguir, o *input.jsno* conteúdo do arquivo é canalizado para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="64b49-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="64b49-171">Windows</span><span class="sxs-lookup"><span data-stu-id="64b49-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="64b49-172">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64b49-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="64b49-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="64b49-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="64b49-174">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64b49-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="64b49-175">Acessar um segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-175">Access a secret</span></span>

<span data-ttu-id="64b49-176">Para acessar um segredo, conclua as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="64b49-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="64b49-177">Registrar a fonte de configuração de segredos do usuário</span><span class="sxs-lookup"><span data-stu-id="64b49-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="64b49-178">Ler o segredo por meio da API de configuração</span><span class="sxs-lookup"><span data-stu-id="64b49-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="64b49-179">Registrar a fonte de configuração de segredos do usuário</span><span class="sxs-lookup"><span data-stu-id="64b49-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="64b49-180">O provedor de [configuração](/dotnet/core/extensions/configuration-providers) de segredos do usuário registra a fonte de configuração apropriada com a [API de configuração](xref:fundamentals/configuration/index)do .net.</span><span class="sxs-lookup"><span data-stu-id="64b49-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="64b49-181">A fonte de configuração segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="64b49-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="64b49-182">`CreateDefaultBuilder` chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> é <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="64b49-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="64b49-183">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="64b49-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="64b49-184">Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="64b49-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="64b49-185">Ler o segredo por meio da API de configuração</span><span class="sxs-lookup"><span data-stu-id="64b49-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="64b49-186">Se a fonte de configuração de segredos do usuário estiver registrada, a API de configuração do .NET poderá ler os segredos.</span><span class="sxs-lookup"><span data-stu-id="64b49-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="64b49-187">A [injeção de Construtor](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) pode ser usada para obter acesso à API de configuração do .net.</span><span class="sxs-lookup"><span data-stu-id="64b49-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="64b49-188">Considere os seguintes exemplos de leitura da `Movies:ServiceApiKey` chave:</span><span class="sxs-lookup"><span data-stu-id="64b49-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="64b49-189">**Classe de inicialização:**</span><span class="sxs-lookup"><span data-stu-id="64b49-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="64b49-190">**Razor Modelo de página de páginas:**</span><span class="sxs-lookup"><span data-stu-id="64b49-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="64b49-191">Para obter mais informações, consulte [configuração de acesso na inicialização](xref:fundamentals/configuration/index#access-configuration-in-startup) e [configuração de acesso em Razor páginas](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span><span class="sxs-lookup"><span data-stu-id="64b49-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="64b49-192">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="64b49-192">Map secrets to a POCO</span></span>

<span data-ttu-id="64b49-193">O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="64b49-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="64b49-194">Para mapear os segredos anteriores para um POCO, use o recurso Associação de [gráfico de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API de configuração do .net.</span><span class="sxs-lookup"><span data-stu-id="64b49-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="64b49-195">O código a seguir é associado a um `MovieSettings` poco personalizado e acessa o `ServiceApiKey` valor da propriedade:</span><span class="sxs-lookup"><span data-stu-id="64b49-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="64b49-196">Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e são mapeados para as respectivas propriedades no `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="64b49-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="64b49-197">Substituição de cadeia de caracteres com segredos</span><span class="sxs-lookup"><span data-stu-id="64b49-197">String replacement with secrets</span></span>

<span data-ttu-id="64b49-198">O armazenamento de senhas em texto sem formatação não é seguro.</span><span class="sxs-lookup"><span data-stu-id="64b49-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="64b49-199">Por exemplo, uma cadeia de conexão de banco de dados armazenada em *appsettings.json* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="64b49-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="64b49-200">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="64b49-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="64b49-201">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64b49-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="64b49-202">Remova o `Password` par chave-valor da cadeia de conexão no *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="64b49-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="64b49-203">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64b49-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="64b49-204">O valor do segredo pode ser definido na propriedade de um <xref:System.Data.SqlClient.SqlConnectionStringBuilder> objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para concluir a cadeia de conexão:</span><span class="sxs-lookup"><span data-stu-id="64b49-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="64b49-205">Listar os segredos</span><span class="sxs-lookup"><span data-stu-id="64b49-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="64b49-206">Execute o seguinte comando do diretório no qual o arquivo de projeto existe:</span><span class="sxs-lookup"><span data-stu-id="64b49-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="64b49-207">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="64b49-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="64b49-208">No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *secrets.jsem*.</span><span class="sxs-lookup"><span data-stu-id="64b49-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="64b49-209">Remover um único segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="64b49-210">Execute o seguinte comando do diretório no qual o arquivo de projeto existe:</span><span class="sxs-lookup"><span data-stu-id="64b49-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="64b49-211">Osecrets.jsdo aplicativo *no* arquivo foi modificado para remover o par chave-valor associado à `MoviesConnectionString` chave:</span><span class="sxs-lookup"><span data-stu-id="64b49-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="64b49-212">`dotnet user-secrets list` exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="64b49-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="64b49-213">Remover todos os segredos</span><span class="sxs-lookup"><span data-stu-id="64b49-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="64b49-214">Execute o seguinte comando do diretório no qual o arquivo de projeto existe:</span><span class="sxs-lookup"><span data-stu-id="64b49-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="64b49-215">Todos os segredos do usuário para o aplicativo foram excluídos do *secrets.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="64b49-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="64b49-216">`dotnet user-secrets list`A execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="64b49-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="64b49-217">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="64b49-217">Additional resources</span></span>

* <span data-ttu-id="64b49-218">Consulte [esse problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar segredos do usuário do IIS.</span><span class="sxs-lookup"><span data-stu-id="64b49-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="64b49-219">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="64b49-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="64b49-220">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="64b49-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="64b49-221">Este documento explica como gerenciar dados confidenciais para um aplicativo ASP.NET Core em um computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="64b49-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="64b49-222">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="64b49-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="64b49-223">Os segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="64b49-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="64b49-224">Os segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="64b49-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="64b49-225">Em vez disso, os segredos de produção devem ser acessados por meio de um meio controlado, como variáveis de ambiente ou Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="64b49-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="64b49-226">Você pode armazenar e proteger os segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="64b49-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="64b49-227">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="64b49-227">Environment variables</span></span>

<span data-ttu-id="64b49-228">As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="64b49-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="64b49-229">Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="64b49-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="64b49-230">Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada.</span><span class="sxs-lookup"><span data-stu-id="64b49-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="64b49-231">Uma cadeia de conexão de banco de dados padrão é incluída no arquivo do projeto *appsettings.json* com a chave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="64b49-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="64b49-232">A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="64b49-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="64b49-233">Durante a implantação do aplicativo, o `DefaultConnection` valor da chave pode ser substituído por um valor de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="64b49-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="64b49-234">A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="64b49-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="64b49-235">Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="64b49-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="64b49-236">Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="64b49-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="64b49-237">Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="64b49-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="64b49-238">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-238">Secret Manager</span></span>

<span data-ttu-id="64b49-239">A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="64b49-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="64b49-240">Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="64b49-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="64b49-241">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="64b49-242">Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos.</span><span class="sxs-lookup"><span data-stu-id="64b49-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="64b49-243">Os segredos do aplicativo não são verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="64b49-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="64b49-244">A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável.</span><span class="sxs-lookup"><span data-stu-id="64b49-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="64b49-245">É apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="64b49-245">It's for development purposes only.</span></span> <span data-ttu-id="64b49-246">As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="64b49-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="64b49-247">Como funciona a ferramenta Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="64b49-248">A ferramenta Gerenciador de segredo oculta os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="64b49-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="64b49-249">Você pode usar a ferramenta sem conhecer esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="64b49-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="64b49-250">Os valores são armazenados em um arquivo JSON na pasta de perfil de usuário do computador local:</span><span class="sxs-lookup"><span data-stu-id="64b49-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="64b49-251">Windows</span><span class="sxs-lookup"><span data-stu-id="64b49-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="64b49-252">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="64b49-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="64b49-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="64b49-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="64b49-254">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="64b49-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="64b49-255">Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo `UserSecretsId` valor especificado no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="64b49-256">Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="64b49-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="64b49-257">Esses detalhes de implementação podem ser alterados.</span><span class="sxs-lookup"><span data-stu-id="64b49-257">These implementation details may change.</span></span> <span data-ttu-id="64b49-258">Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.</span><span class="sxs-lookup"><span data-stu-id="64b49-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="64b49-259">Habilitar o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="64b49-259">Enable secret storage</span></span>

<span data-ttu-id="64b49-260">A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="64b49-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="64b49-261">Para usar os segredos do usuário, defina um `UserSecretsId` elemento dentro `PropertyGroup` de um do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="64b49-262">O texto interno de `UserSecretsId` é arbitrário, mas é exclusivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="64b49-263">Os desenvolvedores normalmente geram um GUID para o `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="64b49-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="64b49-264">No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="64b49-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="64b49-265">Esse gesto adiciona um `UserSecretsId` elemento, populado com um GUID, ao arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="64b49-266">Definir um segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-266">Set a secret</span></span>

<span data-ttu-id="64b49-267">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="64b49-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="64b49-268">O segredo é associado ao valor do projeto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="64b49-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="64b49-269">Por exemplo, execute o seguinte comando do diretório no qual o arquivo de projeto existe:</span><span class="sxs-lookup"><span data-stu-id="64b49-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="64b49-270">No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma `ServiceApiKey` propriedade.</span><span class="sxs-lookup"><span data-stu-id="64b49-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="64b49-271">A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="64b49-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="64b49-272">Use a `--project` opção para fornecer o caminho do sistema de arquivos no qual o arquivo de projeto existe.</span><span class="sxs-lookup"><span data-stu-id="64b49-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="64b49-273">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64b49-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="64b49-274">Nivelamento de estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64b49-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="64b49-275">O gesto **gerenciar segredos do usuário** do Visual Studio abre uma *secrets.jsno* arquivo no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="64b49-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="64b49-276">Substitua o conteúdo de *secrets.js* com os pares chave-valor a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="64b49-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="64b49-277">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64b49-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="64b49-278">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="64b49-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="64b49-279">Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` a execução recolhe o `Movies` literal do objeto.</span><span class="sxs-lookup"><span data-stu-id="64b49-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="64b49-280">O arquivo modificado é semelhante ao JSON a seguir:</span><span class="sxs-lookup"><span data-stu-id="64b49-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="64b49-281">Definir vários segredos</span><span class="sxs-lookup"><span data-stu-id="64b49-281">Set multiple secrets</span></span>

<span data-ttu-id="64b49-282">Um lote de segredos pode ser definido por meio de tubulação JSON para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="64b49-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="64b49-283">No exemplo a seguir, o *input.jsno* conteúdo do arquivo é canalizado para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="64b49-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="64b49-284">Windows</span><span class="sxs-lookup"><span data-stu-id="64b49-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="64b49-285">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64b49-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="64b49-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="64b49-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="64b49-287">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64b49-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="64b49-288">Acessar um segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-288">Access a secret</span></span>

<span data-ttu-id="64b49-289">A [API de configuração](xref:fundamentals/configuration/index) fornece acesso aos segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="64b49-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="64b49-290">Se o projeto tiver como alvo .NET Framework, instale o [Microsoft.Extensions.Configuração. Pacote NuGet usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="64b49-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="64b49-291">No ASP.NET Core 2,0 ou posterior, a fonte de configuração de segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="64b49-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="64b49-292">`CreateDefaultBuilder` chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> é <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="64b49-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="64b49-293">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> no `Startup` Construtor.</span><span class="sxs-lookup"><span data-stu-id="64b49-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="64b49-294">Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="64b49-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="64b49-295">Os segredos do usuário podem ser recuperados por meio da API de configuração do .NET:</span><span class="sxs-lookup"><span data-stu-id="64b49-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="64b49-296">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="64b49-296">Map secrets to a POCO</span></span>

<span data-ttu-id="64b49-297">O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="64b49-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="64b49-298">Para mapear os segredos anteriores para um POCO, use o recurso Associação de [gráfico de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API de configuração do .net.</span><span class="sxs-lookup"><span data-stu-id="64b49-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="64b49-299">O código a seguir é associado a um `MovieSettings` poco personalizado e acessa o `ServiceApiKey` valor da propriedade:</span><span class="sxs-lookup"><span data-stu-id="64b49-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="64b49-300">Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e são mapeados para as respectivas propriedades no `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="64b49-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="64b49-301">Substituição de cadeia de caracteres com segredos</span><span class="sxs-lookup"><span data-stu-id="64b49-301">String replacement with secrets</span></span>

<span data-ttu-id="64b49-302">O armazenamento de senhas em texto sem formatação não é seguro.</span><span class="sxs-lookup"><span data-stu-id="64b49-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="64b49-303">Por exemplo, uma cadeia de conexão de banco de dados armazenada em *appsettings.json* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="64b49-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="64b49-304">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="64b49-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="64b49-305">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64b49-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="64b49-306">Remova o `Password` par chave-valor da cadeia de conexão no *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="64b49-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="64b49-307">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64b49-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="64b49-308">O valor do segredo pode ser definido na propriedade de um <xref:System.Data.SqlClient.SqlConnectionStringBuilder> objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para concluir a cadeia de conexão:</span><span class="sxs-lookup"><span data-stu-id="64b49-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="64b49-309">Listar os segredos</span><span class="sxs-lookup"><span data-stu-id="64b49-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="64b49-310">Execute o seguinte comando do diretório no qual o arquivo de projeto existe:</span><span class="sxs-lookup"><span data-stu-id="64b49-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="64b49-311">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="64b49-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="64b49-312">No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *secrets.jsem*.</span><span class="sxs-lookup"><span data-stu-id="64b49-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="64b49-313">Remover um único segredo</span><span class="sxs-lookup"><span data-stu-id="64b49-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="64b49-314">Execute o seguinte comando do diretório no qual o arquivo de projeto existe:</span><span class="sxs-lookup"><span data-stu-id="64b49-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="64b49-315">Osecrets.jsdo aplicativo *no* arquivo foi modificado para remover o par chave-valor associado à `MoviesConnectionString` chave:</span><span class="sxs-lookup"><span data-stu-id="64b49-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="64b49-316">`dotnet user-secrets list`A execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="64b49-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="64b49-317">Remover todos os segredos</span><span class="sxs-lookup"><span data-stu-id="64b49-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="64b49-318">Execute o seguinte comando do diretório no qual o arquivo de projeto existe:</span><span class="sxs-lookup"><span data-stu-id="64b49-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="64b49-319">Todos os segredos do usuário para o aplicativo foram excluídos do *secrets.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="64b49-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="64b49-320">`dotnet user-secrets list`A execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="64b49-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="64b49-321">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="64b49-321">Additional resources</span></span>

* <span data-ttu-id="64b49-322">Consulte [esse problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar segredos do usuário do IIS.</span><span class="sxs-lookup"><span data-stu-id="64b49-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end