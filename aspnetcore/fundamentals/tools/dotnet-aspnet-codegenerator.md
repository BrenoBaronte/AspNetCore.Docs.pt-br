---
title: comando dotnet aspnet-codegenerator
author: rick-anderson
description: O comando dotnet aspnet-codegenerator aplica scaffold em projetos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 58f7aa30d3e916307437d56c61e80765ac0c21cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766466"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="344b1-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="344b1-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="344b1-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="344b1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="344b1-105">`dotnet aspnet-codegenerator` – executa o mecanismo de scaffolding do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="344b1-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="344b1-106">`dotnet aspnet-codegenerator` é necessário somente para fazer scaffold da linha de comando, não é preciso usar o scaffolding com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="344b1-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="344b1-107">Este artigo se aplica ao [SDK do .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) e posteriores.</span><span class="sxs-lookup"><span data-stu-id="344b1-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="344b1-108">Instalação do aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="344b1-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="344b1-109">`dotnet-aspnet-codegenerator` é uma [ferramenta global](/dotnet/core/tools/global-tools) que deve ser instalada.</span><span class="sxs-lookup"><span data-stu-id="344b1-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="344b1-110">O comando a seguir instala a versão estável mais recente da ferramenta `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="344b1-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="344b1-111">O comando a seguir atualiza `dotnet-aspnet-codegenerator` para a versão estável mais recente disponível dos SDKs do .NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="344b1-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="344b1-112">Sinopse</span><span class="sxs-lookup"><span data-stu-id="344b1-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="344b1-113">Descrição</span><span class="sxs-lookup"><span data-stu-id="344b1-113">Description</span></span>

<span data-ttu-id="344b1-114">O comando global `dotnet aspnet-codegenerator` executa o mecanismo de scaffolding e o gerador de código do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="344b1-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="344b1-115">Argumentos</span><span class="sxs-lookup"><span data-stu-id="344b1-115">Arguments</span></span>

`generator`

<span data-ttu-id="344b1-116">O gerador de código para ser executado.</span><span class="sxs-lookup"><span data-stu-id="344b1-116">The code generator to run.</span></span> <span data-ttu-id="344b1-117">Os geradores a seguir estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="344b1-117">The following generators are available:</span></span>

| <span data-ttu-id="344b1-118">Gerador</span><span class="sxs-lookup"><span data-stu-id="344b1-118">Generator</span></span> | <span data-ttu-id="344b1-119">Operação</span><span class="sxs-lookup"><span data-stu-id="344b1-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="344b1-120">área</span><span class="sxs-lookup"><span data-stu-id="344b1-120">area</span></span>      | [<span data-ttu-id="344b1-121">Faz scaffold de uma área</span><span class="sxs-lookup"><span data-stu-id="344b1-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="344b1-122">controlador</span><span class="sxs-lookup"><span data-stu-id="344b1-122">controller</span></span>| [<span data-ttu-id="344b1-123">Faz scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="344b1-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="344b1-124">identidade</span><span class="sxs-lookup"><span data-stu-id="344b1-124">identity</span></span>  | [<span data-ttu-id="344b1-125">Faz scaffold de uma identidade</span><span class="sxs-lookup"><span data-stu-id="344b1-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="344b1-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="344b1-126">razorpage</span></span> | [<span data-ttu-id="344b1-127">Faz scaffold de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="344b1-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="344b1-128">exibição</span><span class="sxs-lookup"><span data-stu-id="344b1-128">view</span></span>      | [<span data-ttu-id="344b1-129">Faz scaffolds de um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="344b1-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="344b1-130">Opções</span><span class="sxs-lookup"><span data-stu-id="344b1-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="344b1-131">Especifica o diretório de pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="344b1-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="344b1-132">Define a configuração da compilação.</span><span class="sxs-lookup"><span data-stu-id="344b1-132">Defines the build configuration.</span></span> <span data-ttu-id="344b1-133">O valor padrão é `Debug`.</span><span class="sxs-lookup"><span data-stu-id="344b1-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="344b1-134">O [Framework](/dotnet/standard/frameworks) destino para usar.</span><span class="sxs-lookup"><span data-stu-id="344b1-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="344b1-135">Por exemplo, `net46`.</span><span class="sxs-lookup"><span data-stu-id="344b1-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="344b1-136">O caminho base da compilação.</span><span class="sxs-lookup"><span data-stu-id="344b1-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="344b1-137">Imprime uma ajuda breve para o comando.</span><span class="sxs-lookup"><span data-stu-id="344b1-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="344b1-138">Não compila o projeto antes da execução.</span><span class="sxs-lookup"><span data-stu-id="344b1-138">Doesn't build the project before running.</span></span> <span data-ttu-id="344b1-139">Também define o sinalizador `--no-restore` implicitamente.</span><span class="sxs-lookup"><span data-stu-id="344b1-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="344b1-140">Especifica o caminho do arquivo de projeto a ser executado (nome da pasta ou caminho completo).</span><span class="sxs-lookup"><span data-stu-id="344b1-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="344b1-141">Se não é especificado, ele usa como padrão o diretório atual.</span><span class="sxs-lookup"><span data-stu-id="344b1-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="344b1-142">Opções de gerador</span><span class="sxs-lookup"><span data-stu-id="344b1-142">Generator options</span></span>

<span data-ttu-id="344b1-143">As seções a seguir detalham as opções disponíveis para os geradores com suporte:</span><span class="sxs-lookup"><span data-stu-id="344b1-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="344b1-144">Área</span><span class="sxs-lookup"><span data-stu-id="344b1-144">Area</span></span>
* <span data-ttu-id="344b1-145">Controller</span><span class="sxs-lookup"><span data-stu-id="344b1-145">Controller</span></span>
* <span data-ttu-id="344b1-146">Identidade</span><span class="sxs-lookup"><span data-stu-id="344b1-146">Identity</span></span>  
* <span data-ttu-id="344b1-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="344b1-147">Razorpage</span></span>
* <span data-ttu-id="344b1-148">Visualizar</span><span class="sxs-lookup"><span data-stu-id="344b1-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="344b1-149">Opções de área</span><span class="sxs-lookup"><span data-stu-id="344b1-149">Area options</span></span>

<span data-ttu-id="344b1-150">Essa ferramenta destina-se aos projetos Web do ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="344b1-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="344b1-151">Ele não é destinado a aplicativos de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="344b1-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="344b1-152">Uso: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="344b1-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="344b1-153">O comando anterior gera as seguintes pastas:</span><span class="sxs-lookup"><span data-stu-id="344b1-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="344b1-154">*Áreas*</span><span class="sxs-lookup"><span data-stu-id="344b1-154">*Areas*</span></span>
  * <span data-ttu-id="344b1-155">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="344b1-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="344b1-156">*Controladores*</span><span class="sxs-lookup"><span data-stu-id="344b1-156">*Controllers*</span></span>
    * <span data-ttu-id="344b1-157">*Dados*</span><span class="sxs-lookup"><span data-stu-id="344b1-157">*Data*</span></span>
    * <span data-ttu-id="344b1-158">*Modelos*</span><span class="sxs-lookup"><span data-stu-id="344b1-158">*Models*</span></span>
    * <span data-ttu-id="344b1-159">*Modos de exibição*</span><span class="sxs-lookup"><span data-stu-id="344b1-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="344b1-160">Opções de controlador</span><span class="sxs-lookup"><span data-stu-id="344b1-160">Controller options</span></span>

<span data-ttu-id="344b1-161">A tabela a seguir lista as `aspnet-codegenerator` `controller` opções `razorpage`para o e o:</span><span class="sxs-lookup"><span data-stu-id="344b1-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="344b1-162">A tabela a seguir lista as opções exclusivas para `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="344b1-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="344b1-163">Opção</span><span class="sxs-lookup"><span data-stu-id="344b1-163">Option</span></span>               | <span data-ttu-id="344b1-164">Descrição</span><span class="sxs-lookup"><span data-stu-id="344b1-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="344b1-165">--controllerName ou -name</span><span class="sxs-lookup"><span data-stu-id="344b1-165">--controllerName or -name</span></span> | <span data-ttu-id="344b1-166">O nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="344b1-166">Name of the controller.</span></span> |
| <span data-ttu-id="344b1-167">--useAsyncActions ou -async</span><span class="sxs-lookup"><span data-stu-id="344b1-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="344b1-168">Gera ações do controlador assíncrono.</span><span class="sxs-lookup"><span data-stu-id="344b1-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="344b1-169">--noViews ou -nv</span><span class="sxs-lookup"><span data-stu-id="344b1-169">--noViews or -nv</span></span> | <span data-ttu-id="344b1-170">**Não** gera modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="344b1-170">Generate **no** views.</span></span> |
| <span data-ttu-id="344b1-171">--restWithNoViews ou -api</span><span class="sxs-lookup"><span data-stu-id="344b1-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="344b1-172">Gera um controlador com API estilo REST.</span><span class="sxs-lookup"><span data-stu-id="344b1-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="344b1-173">É assumido `noViews` e todas as opções relacionadas à visualização são ignoradas.</span><span class="sxs-lookup"><span data-stu-id="344b1-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="344b1-174">--readWriteActions ou -actions</span><span class="sxs-lookup"><span data-stu-id="344b1-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="344b1-175">Gere o controlador com ações de leitura/gravação sem um modelo.</span><span class="sxs-lookup"><span data-stu-id="344b1-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="344b1-176">Use o switch `-h` para obter ajuda sobre o comando `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="344b1-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="344b1-177">Confira [Fazer scaffold do modelo de filme](/aspnet/core/tutorials/razor-pages/model) para obter um exemplo de `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="344b1-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="344b1-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="344b1-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="344b1-179">As Razor Pages podem ser geradas por scaffolding individualmente, especificando o nome da nova página e o modelo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="344b1-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="344b1-180">Os modelos com suporte são:</span><span class="sxs-lookup"><span data-stu-id="344b1-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="344b1-181">Por exemplo, o comando a seguir usa o modelo Editar para gerar *MyEdit.cshtml* e *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="344b1-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="344b1-182">Normalmente, o modelo e o nome de arquivo gerado não são especificados e os seguintes modelos são criados:</span><span class="sxs-lookup"><span data-stu-id="344b1-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="344b1-183">A tabela a seguir lista as `aspnet-codegenerator` `razorpage` opções `controller`para o e o:</span><span class="sxs-lookup"><span data-stu-id="344b1-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="344b1-184">A tabela a seguir lista as opções exclusivas para `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="344b1-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="344b1-185">Opção</span><span class="sxs-lookup"><span data-stu-id="344b1-185">Option</span></span>               | <span data-ttu-id="344b1-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="344b1-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="344b1-187">--namespaceName ou -namespace</span><span class="sxs-lookup"><span data-stu-id="344b1-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="344b1-188">O nome do namespace a ser usado no PageModel gerado</span><span class="sxs-lookup"><span data-stu-id="344b1-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="344b1-189">--partialView ou -partial</span><span class="sxs-lookup"><span data-stu-id="344b1-189">--partialView or -partial</span></span> | <span data-ttu-id="344b1-190">Gere uma exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="344b1-190">Generate a partial view.</span></span> <span data-ttu-id="344b1-191">As opções de layout -l e - udl são ignoradas, se isso for especificado.</span><span class="sxs-lookup"><span data-stu-id="344b1-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="344b1-192">--noPageModel ou -npm</span><span class="sxs-lookup"><span data-stu-id="344b1-192">--noPageModel or -npm</span></span> | <span data-ttu-id="344b1-193">Alterne para não gerar uma classe PageModel para o modelo Vazio</span><span class="sxs-lookup"><span data-stu-id="344b1-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="344b1-194">Use o switch `-h` para obter ajuda sobre o comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="344b1-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="344b1-195">Confira [Fazer scaffold do modelo de filme](/aspnet/core/tutorials/razor-pages/model) para obter um exemplo de `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="344b1-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="344b1-196">Consulte [Scaffold Identity ](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="344b1-196">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
