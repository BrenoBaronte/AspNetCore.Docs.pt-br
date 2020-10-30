---
title: Usar a CLI do LibMan com ASP.NET Core
author: scottaddie
description: Saiba como usar a CLI do LibMan em um projeto ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: client-side/libman/libman-cli
ms.openlocfilehash: dad9136439b61ad98523061d181fe44d3bf1273d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054743"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="6de99-103">Usar a CLI do LibMan com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6de99-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="6de99-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6de99-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6de99-105">A CLI do [LibMan](xref:client-side/libman/index) é uma ferramenta de plataforma cruzada com suporte em todo o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6de99-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6de99-106">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="6de99-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="6de99-107">Instalação</span><span class="sxs-lookup"><span data-stu-id="6de99-107">Installation</span></span>

<span data-ttu-id="6de99-108">Para instalar a CLI do LibMan:</span><span class="sxs-lookup"><span data-stu-id="6de99-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="6de99-109">Uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) é instalada do pacote NuGet [Microsoft. Web. LibraryManager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .</span><span class="sxs-lookup"><span data-stu-id="6de99-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="6de99-110">Para instalar a CLI do LibMan de uma origem específica do pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="6de99-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="6de99-111">No exemplo anterior, uma ferramenta global do .NET Core é instalada a partir do arquivo *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* do computador Windows local.</span><span class="sxs-lookup"><span data-stu-id="6de99-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="6de99-112">Uso</span><span class="sxs-lookup"><span data-stu-id="6de99-112">Usage</span></span>

<span data-ttu-id="6de99-113">Após a instalação bem-sucedida da CLI, o comando a seguir pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="6de99-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="6de99-114">Para exibir a versão instalada da CLI:</span><span class="sxs-lookup"><span data-stu-id="6de99-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="6de99-115">Para exibir os comandos da CLI disponíveis:</span><span class="sxs-lookup"><span data-stu-id="6de99-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="6de99-116">O comando anterior exibe uma saída semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="6de99-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="6de99-117">As seções a seguir descrevem os comandos da CLI disponíveis.</span><span class="sxs-lookup"><span data-stu-id="6de99-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="6de99-118">Inicializar LibMan no projeto</span><span class="sxs-lookup"><span data-stu-id="6de99-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="6de99-119">O `libman init` comando criará um *libman.jsno* arquivo se ele não existir.</span><span class="sxs-lookup"><span data-stu-id="6de99-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="6de99-120">O arquivo é criado com o conteúdo do modelo de item padrão.</span><span class="sxs-lookup"><span data-stu-id="6de99-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6de99-121">Sinopse</span><span class="sxs-lookup"><span data-stu-id="6de99-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6de99-122">Opções</span><span class="sxs-lookup"><span data-stu-id="6de99-122">Options</span></span>

<span data-ttu-id="6de99-123">As opções a seguir estão disponíveis para o comando `libman init`:</span><span class="sxs-lookup"><span data-stu-id="6de99-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="6de99-124">Um caminho relativo à pasta atual.</span><span class="sxs-lookup"><span data-stu-id="6de99-124">A path relative to the current folder.</span></span> <span data-ttu-id="6de99-125">Os arquivos de biblioteca serão instalados nesse local se nenhuma `destination` propriedade for definida para uma biblioteca no *libman.jsem* .</span><span class="sxs-lookup"><span data-stu-id="6de99-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json* .</span></span> <span data-ttu-id="6de99-126">O `<PATH>` valor é gravado na `defaultDestination` propriedade de *libman.jsem* .</span><span class="sxs-lookup"><span data-stu-id="6de99-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json* .</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="6de99-127">O provedor a ser usado se nenhum provedor for definido para uma determinada biblioteca.</span><span class="sxs-lookup"><span data-stu-id="6de99-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="6de99-128">O `<PROVIDER>` valor é gravado na `defaultProvider` propriedade de *libman.jsem* .</span><span class="sxs-lookup"><span data-stu-id="6de99-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json* .</span></span> <span data-ttu-id="6de99-129">Substitua `<PROVIDER>` por um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="6de99-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6de99-130">Exemplos</span><span class="sxs-lookup"><span data-stu-id="6de99-130">Examples</span></span>

<span data-ttu-id="6de99-131">Para criar um *libman.jsno* arquivo em um projeto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6de99-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="6de99-132">Navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="6de99-132">Navigate to the project root.</span></span>
* <span data-ttu-id="6de99-133">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6de99-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="6de99-134">Digite o nome do provedor padrão ou pressione `Enter` para usar o provedor CDNJS padrão.</span><span class="sxs-lookup"><span data-stu-id="6de99-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="6de99-135">Os valores válidos incluem:</span><span class="sxs-lookup"><span data-stu-id="6de99-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![comando init Libman – provedor padrão](_static/libman-init-provider.png)

<span data-ttu-id="6de99-137">Um *libman.jsno* arquivo é adicionado à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="6de99-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="6de99-138">Adicionar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="6de99-138">Add library files</span></span>

<span data-ttu-id="6de99-139">O `libman install` comando baixa e instala arquivos de biblioteca no projeto.</span><span class="sxs-lookup"><span data-stu-id="6de99-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="6de99-140">Um *libman.jsno* arquivo será adicionado se um não existir.</span><span class="sxs-lookup"><span data-stu-id="6de99-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="6de99-141">O *libman.jsno* arquivo é modificado para armazenar detalhes de configuração dos arquivos de biblioteca.</span><span class="sxs-lookup"><span data-stu-id="6de99-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6de99-142">Sinopse</span><span class="sxs-lookup"><span data-stu-id="6de99-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6de99-143">Argumentos</span><span class="sxs-lookup"><span data-stu-id="6de99-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6de99-144">O nome da biblioteca a ser instalada.</span><span class="sxs-lookup"><span data-stu-id="6de99-144">The name of the library to install.</span></span> <span data-ttu-id="6de99-145">Esse nome pode incluir a notação de número de versão (por exemplo, `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="6de99-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="6de99-146">Opções</span><span class="sxs-lookup"><span data-stu-id="6de99-146">Options</span></span>

<span data-ttu-id="6de99-147">As opções a seguir estão disponíveis para o comando `libman install`:</span><span class="sxs-lookup"><span data-stu-id="6de99-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="6de99-148">O local para instalar a biblioteca.</span><span class="sxs-lookup"><span data-stu-id="6de99-148">The location to install the library.</span></span> <span data-ttu-id="6de99-149">Se não for especificado, o local padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="6de99-149">If not specified, the default location is used.</span></span> <span data-ttu-id="6de99-150">Se nenhuma `defaultDestination` propriedade for especificada no *libman.jsem* , essa opção será necessária.</span><span class="sxs-lookup"><span data-stu-id="6de99-150">If no `defaultDestination` property is specified in *libman.json* , this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="6de99-151">Especifique o nome do arquivo a ser instalado da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="6de99-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="6de99-152">Se não for especificado, todos os arquivos da biblioteca serão instalados.</span><span class="sxs-lookup"><span data-stu-id="6de99-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="6de99-153">Forneça uma `--files` opção por arquivo a ser instalada.</span><span class="sxs-lookup"><span data-stu-id="6de99-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="6de99-154">Caminhos relativos também têm suporte.</span><span class="sxs-lookup"><span data-stu-id="6de99-154">Relative paths are supported too.</span></span> <span data-ttu-id="6de99-155">Por exemplo: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="6de99-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="6de99-156">O nome do provedor a ser usado para a aquisição da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="6de99-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="6de99-157">Substitua `<PROVIDER>` por um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="6de99-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="6de99-158">Se não for especificado, a `defaultProvider` propriedade no *libman.jsem* será usada.</span><span class="sxs-lookup"><span data-stu-id="6de99-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="6de99-159">Se nenhuma `defaultProvider` propriedade for especificada no *libman.jsem* , essa opção será necessária.</span><span class="sxs-lookup"><span data-stu-id="6de99-159">If no `defaultProvider` property is specified in *libman.json* , this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6de99-160">Exemplos</span><span class="sxs-lookup"><span data-stu-id="6de99-160">Examples</span></span>

<span data-ttu-id="6de99-161">Considere o seguinte *libman.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="6de99-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="6de99-162">Para instalar o arquivo jQuery versão 3.2.1 *jquery.min.js* na pasta *wwwroot/scripts/jQuery* usando o provedor CDNJS:</span><span class="sxs-lookup"><span data-stu-id="6de99-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="6de99-163">O *libman.jsno* arquivo é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="6de99-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="6de99-164">Para instalar os arquivos *calendar.js* e *Calendar. css* de *C: \\ temp \\ contosoCalendar \\* usando o provedor do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="6de99-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="6de99-165">O prompt a seguir aparece por dois motivos:</span><span class="sxs-lookup"><span data-stu-id="6de99-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="6de99-166">O *libman.jsno* arquivo não contém uma `defaultDestination` propriedade.</span><span class="sxs-lookup"><span data-stu-id="6de99-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="6de99-167">O `libman install` comando não contém a `-d|--destination` opção.</span><span class="sxs-lookup"><span data-stu-id="6de99-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![comando de instalação do Libman-destino](_static/libman-install-destination.png)

<span data-ttu-id="6de99-169">Depois de aceitar o destino padrão, o *libman.jsno* arquivo é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="6de99-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="6de99-170">Restaurar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="6de99-170">Restore library files</span></span>

<span data-ttu-id="6de99-171">O `libman restore` comando instala os arquivos de biblioteca definidos no *libman.jsem* .</span><span class="sxs-lookup"><span data-stu-id="6de99-171">The `libman restore` command installs library files defined in *libman.json* .</span></span> <span data-ttu-id="6de99-172">As seguintes regras se aplicam:</span><span class="sxs-lookup"><span data-stu-id="6de99-172">The following rules apply:</span></span>

* <span data-ttu-id="6de99-173">Se nenhum *libman.jsno* arquivo existir na raiz do projeto, um erro será retornado.</span><span class="sxs-lookup"><span data-stu-id="6de99-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="6de99-174">Se uma biblioteca especificar um provedor, a `defaultProvider` propriedade no *libman.jsem* será ignorada.</span><span class="sxs-lookup"><span data-stu-id="6de99-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="6de99-175">Se uma biblioteca especificar um destino, a `defaultDestination` propriedade no *libman.jsem* será ignorada.</span><span class="sxs-lookup"><span data-stu-id="6de99-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6de99-176">Sinopse</span><span class="sxs-lookup"><span data-stu-id="6de99-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6de99-177">Opções</span><span class="sxs-lookup"><span data-stu-id="6de99-177">Options</span></span>

<span data-ttu-id="6de99-178">As opções a seguir estão disponíveis para o comando `libman restore`:</span><span class="sxs-lookup"><span data-stu-id="6de99-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6de99-179">Exemplos</span><span class="sxs-lookup"><span data-stu-id="6de99-179">Examples</span></span>

<span data-ttu-id="6de99-180">Para restaurar os arquivos de biblioteca definidos no *libman.jsem* :</span><span class="sxs-lookup"><span data-stu-id="6de99-180">To restore the library files defined in *libman.json* :</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="6de99-181">Excluir arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="6de99-181">Delete library files</span></span>

<span data-ttu-id="6de99-182">O `libman clean` comando exclui os arquivos de biblioteca restaurados anteriormente por meio de LibMan.</span><span class="sxs-lookup"><span data-stu-id="6de99-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="6de99-183">Pastas que se tornam vazias depois que essa operação é excluída.</span><span class="sxs-lookup"><span data-stu-id="6de99-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="6de99-184">As configurações associadas dos arquivos de biblioteca na `libraries` propriedade de *libman.json* não são removidas.</span><span class="sxs-lookup"><span data-stu-id="6de99-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6de99-185">Sinopse</span><span class="sxs-lookup"><span data-stu-id="6de99-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6de99-186">Opções</span><span class="sxs-lookup"><span data-stu-id="6de99-186">Options</span></span>

<span data-ttu-id="6de99-187">As opções a seguir estão disponíveis para o comando `libman clean`:</span><span class="sxs-lookup"><span data-stu-id="6de99-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6de99-188">Exemplos</span><span class="sxs-lookup"><span data-stu-id="6de99-188">Examples</span></span>

<span data-ttu-id="6de99-189">Para excluir arquivos de biblioteca instalados por meio do LibMan:</span><span class="sxs-lookup"><span data-stu-id="6de99-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="6de99-190">Desinstalar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="6de99-190">Uninstall library files</span></span>

<span data-ttu-id="6de99-191">O comando `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="6de99-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="6de99-192">Exclui todos os arquivos associados à biblioteca especificada do destino no *libman.jsem* .</span><span class="sxs-lookup"><span data-stu-id="6de99-192">Deletes all files associated with the specified library from the destination in *libman.json* .</span></span>
* <span data-ttu-id="6de99-193">Remove a configuração da biblioteca associada do *libman.jsem* .</span><span class="sxs-lookup"><span data-stu-id="6de99-193">Removes the associated library configuration from *libman.json* .</span></span>

<span data-ttu-id="6de99-194">Ocorrerá um erro quando:</span><span class="sxs-lookup"><span data-stu-id="6de99-194">An error occurs when:</span></span>

* <span data-ttu-id="6de99-195">Não existe *libman.jsno* arquivo na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="6de99-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="6de99-196">A biblioteca especificada não existe.</span><span class="sxs-lookup"><span data-stu-id="6de99-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="6de99-197">Se mais de uma biblioteca com o mesmo nome estiver instalada, você será solicitado a escolher uma.</span><span class="sxs-lookup"><span data-stu-id="6de99-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6de99-198">Sinopse</span><span class="sxs-lookup"><span data-stu-id="6de99-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6de99-199">Argumentos</span><span class="sxs-lookup"><span data-stu-id="6de99-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6de99-200">O nome da biblioteca a ser desinstalada.</span><span class="sxs-lookup"><span data-stu-id="6de99-200">The name of the library to uninstall.</span></span> <span data-ttu-id="6de99-201">Esse nome pode incluir a notação de número de versão (por exemplo, `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="6de99-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="6de99-202">Opções</span><span class="sxs-lookup"><span data-stu-id="6de99-202">Options</span></span>

<span data-ttu-id="6de99-203">As opções a seguir estão disponíveis para o comando `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="6de99-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6de99-204">Exemplos</span><span class="sxs-lookup"><span data-stu-id="6de99-204">Examples</span></span>

<span data-ttu-id="6de99-205">Considere o seguinte *libman.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="6de99-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="6de99-206">Para desinstalar o jQuery, qualquer um dos seguintes comandos é executado com sucesso:</span><span class="sxs-lookup"><span data-stu-id="6de99-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="6de99-207">Para desinstalar os arquivos do Lodash instalados por meio do `filesystem` provedor:</span><span class="sxs-lookup"><span data-stu-id="6de99-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="6de99-208">Atualizar versão da biblioteca</span><span class="sxs-lookup"><span data-stu-id="6de99-208">Update library version</span></span>

<span data-ttu-id="6de99-209">O `libman update` comando atualiza uma biblioteca instalada por meio de LibMan para a versão especificada.</span><span class="sxs-lookup"><span data-stu-id="6de99-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="6de99-210">Ocorrerá um erro quando:</span><span class="sxs-lookup"><span data-stu-id="6de99-210">An error occurs when:</span></span>

* <span data-ttu-id="6de99-211">Não existe *libman.jsno* arquivo na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="6de99-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="6de99-212">A biblioteca especificada não existe.</span><span class="sxs-lookup"><span data-stu-id="6de99-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="6de99-213">Se mais de uma biblioteca com o mesmo nome estiver instalada, você será solicitado a escolher uma.</span><span class="sxs-lookup"><span data-stu-id="6de99-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6de99-214">Sinopse</span><span class="sxs-lookup"><span data-stu-id="6de99-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6de99-215">Argumentos</span><span class="sxs-lookup"><span data-stu-id="6de99-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6de99-216">O nome da biblioteca a ser atualizada.</span><span class="sxs-lookup"><span data-stu-id="6de99-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="6de99-217">Opções</span><span class="sxs-lookup"><span data-stu-id="6de99-217">Options</span></span>

<span data-ttu-id="6de99-218">As opções a seguir estão disponíveis para o comando `libman update`:</span><span class="sxs-lookup"><span data-stu-id="6de99-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="6de99-219">Obtenha a versão de pré-lançamento mais recente da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="6de99-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="6de99-220">Obtenha uma versão específica da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="6de99-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6de99-221">Exemplos</span><span class="sxs-lookup"><span data-stu-id="6de99-221">Examples</span></span>

* <span data-ttu-id="6de99-222">Para atualizar o jQuery para a versão mais recente:</span><span class="sxs-lookup"><span data-stu-id="6de99-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="6de99-223">Para atualizar o jQuery para a versão 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="6de99-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="6de99-224">Para atualizar o jQuery para a versão de pré-lançamento mais recente:</span><span class="sxs-lookup"><span data-stu-id="6de99-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="6de99-225">Gerenciar cache de biblioteca</span><span class="sxs-lookup"><span data-stu-id="6de99-225">Manage library cache</span></span>

<span data-ttu-id="6de99-226">O `libman cache` comando gerencia o cache da biblioteca LibMan.</span><span class="sxs-lookup"><span data-stu-id="6de99-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="6de99-227">O `filesystem` provedor não usa o cache de biblioteca.</span><span class="sxs-lookup"><span data-stu-id="6de99-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6de99-228">Sinopse</span><span class="sxs-lookup"><span data-stu-id="6de99-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6de99-229">Argumentos</span><span class="sxs-lookup"><span data-stu-id="6de99-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="6de99-230">Usado somente com o `clean` comando.</span><span class="sxs-lookup"><span data-stu-id="6de99-230">Only used with the `clean` command.</span></span> <span data-ttu-id="6de99-231">Especifica o cache do provedor a ser limpo.</span><span class="sxs-lookup"><span data-stu-id="6de99-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="6de99-232">Os valores válidos incluem:</span><span class="sxs-lookup"><span data-stu-id="6de99-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="6de99-233">Opções</span><span class="sxs-lookup"><span data-stu-id="6de99-233">Options</span></span>

<span data-ttu-id="6de99-234">As opções a seguir estão disponíveis para o comando `libman cache`:</span><span class="sxs-lookup"><span data-stu-id="6de99-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="6de99-235">Lista os nomes de arquivos que são armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="6de99-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="6de99-236">Liste os nomes das bibliotecas armazenadas em cache.</span><span class="sxs-lookup"><span data-stu-id="6de99-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6de99-237">Exemplos</span><span class="sxs-lookup"><span data-stu-id="6de99-237">Examples</span></span>

* <span data-ttu-id="6de99-238">Para exibir os nomes das bibliotecas armazenadas em cache por provedor, use um dos seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6de99-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="6de99-239">Uma saída semelhante à apresentada a seguir será exibida:</span><span class="sxs-lookup"><span data-stu-id="6de99-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="6de99-240">Para exibir os nomes dos arquivos de biblioteca armazenados em cache por provedor:</span><span class="sxs-lookup"><span data-stu-id="6de99-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="6de99-241">Uma saída semelhante à apresentada a seguir será exibida:</span><span class="sxs-lookup"><span data-stu-id="6de99-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="6de99-242">Observe que a saída anterior mostra que as versões 3.2.1 e 3.3.1 do jQuery são armazenadas em cache no provedor CDNJS.</span><span class="sxs-lookup"><span data-stu-id="6de99-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="6de99-243">Para esvaziar o cache de biblioteca para o provedor CDNJS:</span><span class="sxs-lookup"><span data-stu-id="6de99-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="6de99-244">Depois de esvaziar o cache do provedor de CDNJS, o `libman cache list` comando exibe o seguinte:</span><span class="sxs-lookup"><span data-stu-id="6de99-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="6de99-245">Para esvaziar o cache para todos os provedores com suporte:</span><span class="sxs-lookup"><span data-stu-id="6de99-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="6de99-246">Depois de esvaziar todos os caches do provedor, o `libman cache list` comando exibe o seguinte:</span><span class="sxs-lookup"><span data-stu-id="6de99-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="6de99-247">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6de99-247">Additional resources</span></span>

* [<span data-ttu-id="6de99-248">Instalar uma Ferramenta Global</span><span class="sxs-lookup"><span data-stu-id="6de99-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="6de99-249">Repositório do GitHub do LibMan</span><span class="sxs-lookup"><span data-stu-id="6de99-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
