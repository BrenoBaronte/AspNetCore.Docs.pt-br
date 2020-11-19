---
title: comando dotnet aspnet-codegenerator
author: rick-anderson
description: O comando dotnet aspnet-codegenerator aplica scaffold em projetos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: d29535ea0b4197443fd884aaa6e5b4b763d04fc7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920697"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="71783-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="71783-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="71783-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71783-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71783-105">`dotnet aspnet-codegenerator` – executa o mecanismo de scaffolding do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="71783-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="71783-106">`dotnet aspnet-codegenerator` é necessário somente para fazer scaffold da linha de comando, não é preciso usar o scaffolding com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="71783-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="71783-107">Instalar e atualizar ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="71783-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="71783-108">Instale o [SDK do .net](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="71783-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="71783-109">`dotnet-aspnet-codegenerator` é uma [ferramenta global](/dotnet/core/tools/global-tools) que deve ser instalada.</span><span class="sxs-lookup"><span data-stu-id="71783-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="71783-110">O comando a seguir instala a versão estável mais recente da ferramenta `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="71783-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="71783-111">O comando a seguir atualiza `dotnet-aspnet-codegenerator` para a versão estável mais recente disponível dos SDKs do .NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="71783-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="71783-112">Desinstalar ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="71783-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="71783-113">Pode ser necessário desinstalar o `aspnet-codegenerator` para resolver problemas.</span><span class="sxs-lookup"><span data-stu-id="71783-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="71783-114">Por exemplo, se você instalou uma versão de visualização do `aspnet-codegenerator` , desinstale-a antes de instalar a versão de lançamento.</span><span class="sxs-lookup"><span data-stu-id="71783-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="71783-115">Os seguintes comandos desinstalem a `dotnet-aspnet-codegenerator` ferramenta e instalam a versão estável mais recente:</span><span class="sxs-lookup"><span data-stu-id="71783-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="71783-116">Sinopse</span><span class="sxs-lookup"><span data-stu-id="71783-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="71783-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="71783-117">Description</span></span>

<span data-ttu-id="71783-118">O comando global `dotnet aspnet-codegenerator` executa o mecanismo de scaffolding e o gerador de código do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="71783-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="71783-119">Argumentos</span><span class="sxs-lookup"><span data-stu-id="71783-119">Arguments</span></span>

`generator`

<span data-ttu-id="71783-120">O gerador de código para ser executado.</span><span class="sxs-lookup"><span data-stu-id="71783-120">The code generator to run.</span></span> <span data-ttu-id="71783-121">Os geradores a seguir estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="71783-121">The following generators are available:</span></span>

| <span data-ttu-id="71783-122">Gerador</span><span class="sxs-lookup"><span data-stu-id="71783-122">Generator</span></span>  | <span data-ttu-id="71783-123">Operação</span><span class="sxs-lookup"><span data-stu-id="71783-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="71783-124">área</span><span class="sxs-lookup"><span data-stu-id="71783-124">area</span></span>       | [<span data-ttu-id="71783-125">Faz scaffold de uma área</span><span class="sxs-lookup"><span data-stu-id="71783-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="71783-126">controlador</span><span class="sxs-lookup"><span data-stu-id="71783-126">controller</span></span> | [<span data-ttu-id="71783-127">Faz scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="71783-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="71783-128">identidade</span><span class="sxs-lookup"><span data-stu-id="71783-128">identity</span></span>   | [<span data-ttu-id="71783-129">Aplica Scaffold Identity</span><span class="sxs-lookup"><span data-stu-id="71783-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="71783-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="71783-130">razorpage</span></span>  | [<span data-ttu-id="71783-131">Páginas do aplica Scaffold Razor</span><span class="sxs-lookup"><span data-stu-id="71783-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="71783-132">exibição</span><span class="sxs-lookup"><span data-stu-id="71783-132">view</span></span>       | [<span data-ttu-id="71783-133">Faz scaffolds de um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="71783-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="71783-134">Opções</span><span class="sxs-lookup"><span data-stu-id="71783-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="71783-135">Especifica o diretório de pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="71783-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="71783-136">Define a configuração da compilação.</span><span class="sxs-lookup"><span data-stu-id="71783-136">Defines the build configuration.</span></span> <span data-ttu-id="71783-137">O valor padrão é `Debug`.</span><span class="sxs-lookup"><span data-stu-id="71783-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="71783-138">O [Framework](/dotnet/standard/frameworks) destino para usar.</span><span class="sxs-lookup"><span data-stu-id="71783-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="71783-139">Por exemplo, `net46`.</span><span class="sxs-lookup"><span data-stu-id="71783-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="71783-140">O caminho base da compilação.</span><span class="sxs-lookup"><span data-stu-id="71783-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="71783-141">Imprime uma ajuda breve para o comando.</span><span class="sxs-lookup"><span data-stu-id="71783-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="71783-142">Não compila o projeto antes da execução.</span><span class="sxs-lookup"><span data-stu-id="71783-142">Doesn't build the project before running.</span></span> <span data-ttu-id="71783-143">Também define o sinalizador `--no-restore` implicitamente.</span><span class="sxs-lookup"><span data-stu-id="71783-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="71783-144">Especifica o caminho do arquivo de projeto a ser executado (nome da pasta ou caminho completo).</span><span class="sxs-lookup"><span data-stu-id="71783-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="71783-145">Se não é especificado, ele usa como padrão o diretório atual.</span><span class="sxs-lookup"><span data-stu-id="71783-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="71783-146">Opções de gerador</span><span class="sxs-lookup"><span data-stu-id="71783-146">Generator options</span></span>

<span data-ttu-id="71783-147">As seções a seguir detalham as opções disponíveis para os geradores com suporte:</span><span class="sxs-lookup"><span data-stu-id="71783-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="71783-148">Área</span><span class="sxs-lookup"><span data-stu-id="71783-148">Area</span></span>
* <span data-ttu-id="71783-149">Controller</span><span class="sxs-lookup"><span data-stu-id="71783-149">Controller</span></span>
* Identity  
* <span data-ttu-id="71783-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="71783-150">Razorpage</span></span>
* <span data-ttu-id="71783-151">Exibir</span><span class="sxs-lookup"><span data-stu-id="71783-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="71783-152">Opções de área</span><span class="sxs-lookup"><span data-stu-id="71783-152">Area options</span></span>

<span data-ttu-id="71783-153">Essa ferramenta destina-se aos projetos Web do ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="71783-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="71783-154">Ele não se destina a Razor aplicativos de páginas.</span><span class="sxs-lookup"><span data-stu-id="71783-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="71783-155">Uso: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="71783-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="71783-156">O comando anterior gera as seguintes pastas:</span><span class="sxs-lookup"><span data-stu-id="71783-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="71783-157">*Áreas*</span><span class="sxs-lookup"><span data-stu-id="71783-157">*Areas*</span></span>
  * <span data-ttu-id="71783-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="71783-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="71783-159">*Controladores*</span><span class="sxs-lookup"><span data-stu-id="71783-159">*Controllers*</span></span>
    * <span data-ttu-id="71783-160">*Dados*</span><span class="sxs-lookup"><span data-stu-id="71783-160">*Data*</span></span>
    * <span data-ttu-id="71783-161">*Modelos*</span><span class="sxs-lookup"><span data-stu-id="71783-161">*Models*</span></span>
    * <span data-ttu-id="71783-162">*Exibições*</span><span class="sxs-lookup"><span data-stu-id="71783-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="71783-163">Opções de controlador</span><span class="sxs-lookup"><span data-stu-id="71783-163">Controller options</span></span>

<span data-ttu-id="71783-164">A tabela a seguir lista as opções para o  `aspnet-codegenerator` `controller` e o `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="71783-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="71783-165">A tabela a seguir lista as opções exclusivas para `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="71783-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="71783-166">Opção</span><span class="sxs-lookup"><span data-stu-id="71783-166">Option</span></span>                         | <span data-ttu-id="71783-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="71783-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="71783-168">--controllerName ou -name</span><span class="sxs-lookup"><span data-stu-id="71783-168">--controllerName or -name</span></span>      | <span data-ttu-id="71783-169">O nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="71783-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="71783-170">--useAsyncActions ou -async</span><span class="sxs-lookup"><span data-stu-id="71783-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="71783-171">Gera ações do controlador assíncrono.</span><span class="sxs-lookup"><span data-stu-id="71783-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="71783-172">--noViews ou -nv</span><span class="sxs-lookup"><span data-stu-id="71783-172">--noViews or -nv</span></span>               | <span data-ttu-id="71783-173">**Não** gera modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="71783-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="71783-174">--restWithNoViews ou -api</span><span class="sxs-lookup"><span data-stu-id="71783-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="71783-175">Gera um controlador com API estilo REST.</span><span class="sxs-lookup"><span data-stu-id="71783-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="71783-176">É assumido `noViews` e todas as opções relacionadas à visualização são ignoradas.</span><span class="sxs-lookup"><span data-stu-id="71783-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="71783-177">--readWriteActions ou -actions</span><span class="sxs-lookup"><span data-stu-id="71783-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="71783-178">Gere o controlador com ações de leitura/gravação sem um modelo.</span><span class="sxs-lookup"><span data-stu-id="71783-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="71783-179">Use o switch `-h` para obter ajuda sobre o comando `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="71783-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="71783-180">Confira [Fazer scaffold do modelo de filme](xref:tutorials/first-mvc-app/adding-model) para obter um exemplo de `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="71783-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="71783-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="71783-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="71783-182">Razor As páginas podem ser com Scaffold individualmente especificando o nome da nova página e o modelo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="71783-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="71783-183">Os modelos com suporte são:</span><span class="sxs-lookup"><span data-stu-id="71783-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="71783-184">Por exemplo, o comando a seguir usa o modelo Editar para gerar *MyEdit.cshtml* e *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="71783-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="71783-185">Normalmente, o modelo e o nome de arquivo gerado não são especificados e os seguintes modelos são criados:</span><span class="sxs-lookup"><span data-stu-id="71783-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="71783-186">A tabela a seguir lista as opções para o  `aspnet-codegenerator` `razorpage` e o `controller` :</span><span class="sxs-lookup"><span data-stu-id="71783-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="71783-187">A tabela a seguir lista as opções exclusivas para `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="71783-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="71783-188">Opção</span><span class="sxs-lookup"><span data-stu-id="71783-188">Option</span></span>                        | <span data-ttu-id="71783-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="71783-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="71783-190">--namespaceName ou -namespace</span><span class="sxs-lookup"><span data-stu-id="71783-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="71783-191">O nome do namespace a ser usado no PageModel gerado</span><span class="sxs-lookup"><span data-stu-id="71783-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="71783-192">--partialView ou -partial</span><span class="sxs-lookup"><span data-stu-id="71783-192">--partialView or -partial</span></span>     | <span data-ttu-id="71783-193">Gere uma exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="71783-193">Generate a partial view.</span></span> <span data-ttu-id="71783-194">As opções de layout -l e - udl são ignoradas, se isso for especificado.</span><span class="sxs-lookup"><span data-stu-id="71783-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="71783-195">--noPageModel ou -npm</span><span class="sxs-lookup"><span data-stu-id="71783-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="71783-196">Alterne para não gerar uma classe PageModel para o modelo Vazio</span><span class="sxs-lookup"><span data-stu-id="71783-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="71783-197">Use o switch `-h` para obter ajuda sobre o comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="71783-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="71783-198">Confira [Fazer scaffold do modelo de filme](xref:tutorials/razor-pages/model) para obter um exemplo de `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="71783-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="71783-199">Consulte [Scaffold Identity ](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="71783-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
