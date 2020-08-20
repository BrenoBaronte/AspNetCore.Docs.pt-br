---
title: Usar o LibMan com ASP.NET Core no Visual Studio
author: scottaddie
description: Saiba como usar o LibMan em um projeto ASP.NET Core com o Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
no-loc:
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: 77cfced18edd9db5be21265469b42f32e3de274d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625668"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="fe005-103">Usar o LibMan com ASP.NET Core no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe005-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="fe005-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="fe005-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="fe005-105">O Visual Studio tem suporte interno para [LibMan](xref:client-side/libman/index) em projetos ASP.NET Core, incluindo:</span><span class="sxs-lookup"><span data-stu-id="fe005-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="fe005-106">Suporte para configurar e executar operações de restauração do LibMan no Build.</span><span class="sxs-lookup"><span data-stu-id="fe005-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="fe005-107">Itens de menu para disparar operações de restauração LibMan e limpeza.</span><span class="sxs-lookup"><span data-stu-id="fe005-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="fe005-108">Caixa de diálogo de pesquisa para localizar bibliotecas e adicionar os arquivos a um projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="fe005-109">Suporte de edição para *libman.jsno* &mdash; arquivo de manifesto LibMan.</span><span class="sxs-lookup"><span data-stu-id="fe005-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="fe005-110">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="fe005-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe005-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="fe005-111">Prerequisites</span></span>

* <span data-ttu-id="fe005-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web</span><span class="sxs-lookup"><span data-stu-id="fe005-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="fe005-113">Adicionar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="fe005-113">Add library files</span></span>

<span data-ttu-id="fe005-114">Os arquivos de biblioteca podem ser adicionados a um projeto ASP.NET Core de duas maneiras diferentes:</span><span class="sxs-lookup"><span data-stu-id="fe005-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="fe005-115">Usar a caixa de diálogo Adicionar biblioteca do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="fe005-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="fe005-116">Configurar manualmente as entradas do arquivo de manifesto LibMan</span><span class="sxs-lookup"><span data-stu-id="fe005-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="fe005-117">Usar a caixa de diálogo Adicionar biblioteca do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="fe005-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="fe005-118">Siga estas etapas para instalar uma biblioteca do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="fe005-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="fe005-119">Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta do projeto na qual os arquivos devem ser adicionados.</span><span class="sxs-lookup"><span data-stu-id="fe005-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="fe005-120">Escolha **Adicionar**  >  **biblioteca do lado do cliente**.</span><span class="sxs-lookup"><span data-stu-id="fe005-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="fe005-121">A caixa de diálogo **Adicionar biblioteca do lado do cliente** é exibida:</span><span class="sxs-lookup"><span data-stu-id="fe005-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Caixa de diálogo Adicionar biblioteca do lado do cliente](_static/add-library-dialog.png)

* <span data-ttu-id="fe005-123">Selecione o provedor de biblioteca na lista suspensa **provedor** .</span><span class="sxs-lookup"><span data-stu-id="fe005-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="fe005-124">CDNJS é o provedor padrão.</span><span class="sxs-lookup"><span data-stu-id="fe005-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="fe005-125">Digite o nome da biblioteca a ser buscada na caixa de texto **biblioteca** .</span><span class="sxs-lookup"><span data-stu-id="fe005-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="fe005-126">O IntelliSense fornece uma lista de bibliotecas que começam com o texto fornecido.</span><span class="sxs-lookup"><span data-stu-id="fe005-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="fe005-127">Selecione a biblioteca na lista do IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="fe005-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="fe005-128">Observe que o nome da biblioteca é sufixado com o `@` símbolo e a versão estável mais recente conhecida pelo provedor selecionado.</span><span class="sxs-lookup"><span data-stu-id="fe005-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="fe005-129">Decida quais arquivos incluir:</span><span class="sxs-lookup"><span data-stu-id="fe005-129">Decide which files to include:</span></span>
  * <span data-ttu-id="fe005-130">Selecione o botão de opção **incluir todos os arquivos de biblioteca** para incluir todos os arquivos da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="fe005-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="fe005-131">Selecione o botão de opção **escolher arquivos específicos** para incluir um subconjunto dos arquivos da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="fe005-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="fe005-132">Quando o botão de opção é selecionado, a árvore seletor de arquivo é habilitada.</span><span class="sxs-lookup"><span data-stu-id="fe005-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="fe005-133">Marque as caixas à esquerda dos nomes de arquivo para download.</span><span class="sxs-lookup"><span data-stu-id="fe005-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="fe005-134">Especifique a pasta do projeto para armazenar os arquivos na caixa de texto **local de destino** .</span><span class="sxs-lookup"><span data-stu-id="fe005-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="fe005-135">Como recomendação, armazene cada biblioteca em uma pasta separada.</span><span class="sxs-lookup"><span data-stu-id="fe005-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="fe005-136">A pasta de **local de destino** sugerida é baseada no local do qual a caixa de diálogo é iniciada:</span><span class="sxs-lookup"><span data-stu-id="fe005-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="fe005-137">Se for iniciado da raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="fe005-137">If launched from the project root:</span></span>
    * <span data-ttu-id="fe005-138">*wwwroot/lib* será usado se *wwwroot* existir.</span><span class="sxs-lookup"><span data-stu-id="fe005-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="fe005-139">*lib* será usado se *wwwroot* não existir.</span><span class="sxs-lookup"><span data-stu-id="fe005-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="fe005-140">Se for iniciado a partir de uma pasta de projeto, o nome de pasta correspondente será usado.</span><span class="sxs-lookup"><span data-stu-id="fe005-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="fe005-141">A sugestão de pasta é sufixada com o nome da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="fe005-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="fe005-142">A tabela a seguir ilustra as sugestões de pasta ao instalar o jQuery em um Razor projeto de páginas.</span><span class="sxs-lookup"><span data-stu-id="fe005-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="fe005-143">Local de inicialização</span><span class="sxs-lookup"><span data-stu-id="fe005-143">Launch location</span></span>                           |<span data-ttu-id="fe005-144">Pasta sugerida</span><span class="sxs-lookup"><span data-stu-id="fe005-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="fe005-145">raiz do projeto (se *wwwroot* existir)</span><span class="sxs-lookup"><span data-stu-id="fe005-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="fe005-146">*wwwroot/lib/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="fe005-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="fe005-147">raiz do projeto (se *wwwroot* não existir)</span><span class="sxs-lookup"><span data-stu-id="fe005-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="fe005-148">*lib/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="fe005-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="fe005-149">Pasta *páginas* no projeto</span><span class="sxs-lookup"><span data-stu-id="fe005-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="fe005-150">*Páginas/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="fe005-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="fe005-151">Clique no botão **instalar** para baixar os arquivos, de acordo com a configuração no *libman.jsem*.</span><span class="sxs-lookup"><span data-stu-id="fe005-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="fe005-152">Examine o feed do **Gerenciador de biblioteca** da janela **saída** para obter detalhes da instalação.</span><span class="sxs-lookup"><span data-stu-id="fe005-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="fe005-153">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fe005-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="fe005-154">Configurar manualmente as entradas do arquivo de manifesto LibMan</span><span class="sxs-lookup"><span data-stu-id="fe005-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="fe005-155">Todas as operações de LibMan no Visual Studio se baseiam no conteúdo do manifesto LibMan da raiz do projeto (*libman.jsem*).</span><span class="sxs-lookup"><span data-stu-id="fe005-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="fe005-156">Você pode editar manualmente *libman.jsem* para configurar os arquivos de biblioteca para o projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="fe005-157">O Visual Studio restaura todos os arquivos de biblioteca quando *libman.jsno* é salvo.</span><span class="sxs-lookup"><span data-stu-id="fe005-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="fe005-158">Para abrir *libman.js* para edição, existem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="fe005-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="fe005-159">Clique duas vezes na *libman.jsno* arquivo **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="fe005-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="fe005-160">Clique com o botão direito do mouse no projeto no **Gerenciador de soluções** e selecione **gerenciar bibliotecas do lado do cliente**.</span><span class="sxs-lookup"><span data-stu-id="fe005-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="fe005-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="fe005-161">**&#8224;**</span></span>
* <span data-ttu-id="fe005-162">Selecione **gerenciar bibliotecas do lado do cliente** no menu do **projeto** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fe005-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="fe005-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="fe005-163">**&#8224;**</span></span>

<span data-ttu-id="fe005-164">**&#8224;** Se o *libman.jsno* arquivo ainda não existir na raiz do projeto, ele será criado com o conteúdo do modelo de item padrão.</span><span class="sxs-lookup"><span data-stu-id="fe005-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="fe005-165">O Visual Studio oferece suporte avançado para edição de JSON, como colorização, formatação, IntelliSense e validação de esquema.</span><span class="sxs-lookup"><span data-stu-id="fe005-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="fe005-166">O esquema JSON do manifesto LibMan é encontrado em [https://json.schemastore.org/libman](https://json.schemastore.org/libman) .</span><span class="sxs-lookup"><span data-stu-id="fe005-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="fe005-167">Com o arquivo de manifesto a seguir, LibMan recupera arquivos de acordo com a configuração definida na `libraries` propriedade.</span><span class="sxs-lookup"><span data-stu-id="fe005-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="fe005-168">Uma explicação dos literais de objeto definidos na `libraries` seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="fe005-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="fe005-169">Um subconjunto do [jQuery](https://jquery.com/) versão 3.3.1 é recuperado do provedor CDNJS.</span><span class="sxs-lookup"><span data-stu-id="fe005-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="fe005-170">O subconjunto é definido na `files` propriedade &mdash; *jquery.min.js*, *jquery.js*e *jQuery. min. map*.</span><span class="sxs-lookup"><span data-stu-id="fe005-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="fe005-171">Os arquivos são colocados na pasta *wwwroot/lib/jQuery* do projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="fe005-172">A totalidade da versão de [Bootstrap](https://getbootstrap.com/) 4.1.3 é recuperada e colocada em uma pasta *wwwroot/lib/Bootstrap* .</span><span class="sxs-lookup"><span data-stu-id="fe005-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="fe005-173">A propriedade do literal do objeto `provider` substitui o `defaultProvider` valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="fe005-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="fe005-174">LibMan recupera os arquivos de inicialização do provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="fe005-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="fe005-175">Um subconjunto de [Lodash](https://lodash.com/) foi aprovado por um corpo governado dentro da organização.</span><span class="sxs-lookup"><span data-stu-id="fe005-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="fe005-176">Os arquivos de *lodash.js* e *lodash.min.js* são recuperados do sistema de arquivos local em \*C: \\ temp \\ lodash \\ \*.</span><span class="sxs-lookup"><span data-stu-id="fe005-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="fe005-177">Os arquivos são copiados para a pasta *wwwroot/lib/lodash* do projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="fe005-178">O LibMan dá suporte apenas a uma versão de cada provedor de cada fornecedor.</span><span class="sxs-lookup"><span data-stu-id="fe005-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="fe005-179">O *libman.jsno* arquivo falha na validação do esquema se ele contiver duas bibliotecas com o mesmo nome de biblioteca para um determinado provedor.</span><span class="sxs-lookup"><span data-stu-id="fe005-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="fe005-180">Restaurar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="fe005-180">Restore library files</span></span>

<span data-ttu-id="fe005-181">Para restaurar arquivos de biblioteca de dentro do Visual Studio, deve haver um *libman.jsválido no* arquivo na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="fe005-182">Os arquivos restaurados são colocados no projeto no local especificado para cada biblioteca.</span><span class="sxs-lookup"><span data-stu-id="fe005-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="fe005-183">Os arquivos de biblioteca podem ser restaurados em um projeto ASP.NET Core de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="fe005-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="fe005-184">Restaurar arquivos durante a compilação</span><span class="sxs-lookup"><span data-stu-id="fe005-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="fe005-185">Restaurar arquivos manualmente</span><span class="sxs-lookup"><span data-stu-id="fe005-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="fe005-186">Restaurar arquivos durante a compilação</span><span class="sxs-lookup"><span data-stu-id="fe005-186">Restore files during build</span></span>

<span data-ttu-id="fe005-187">LibMan pode restaurar os arquivos de biblioteca definidos como parte do processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="fe005-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="fe005-188">Por padrão, o comportamento de *restauração no Build* é desabilitado.</span><span class="sxs-lookup"><span data-stu-id="fe005-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="fe005-189">Para habilitar e testar o comportamento de Restore-on-Build:</span><span class="sxs-lookup"><span data-stu-id="fe005-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="fe005-190">Clique com o botão direito do mouse em *libman.js* em **Gerenciador de soluções** e selecione **habilitar a restauração de bibliotecas do lado do cliente no Build** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="fe005-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="fe005-191">Clique no botão **Sim** quando for solicitado a instalar um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="fe005-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="fe005-192">O pacote NuGet [Microsoft. Web. LibraryManager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) é adicionado ao projeto:</span><span class="sxs-lookup"><span data-stu-id="fe005-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="fe005-193">Compile o projeto para confirmar se a restauração do arquivo LibMan ocorre.</span><span class="sxs-lookup"><span data-stu-id="fe005-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="fe005-194">O `Microsoft.Web.LibraryManager.Build` pacote injeta um destino do MSBuild que executa o LibMan durante a operação de compilação do projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="fe005-195">Examine o feed de **Build** da janela de **saída** para um log de atividades do LibMan:</span><span class="sxs-lookup"><span data-stu-id="fe005-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="fe005-196">Quando o comportamento de Restore-on-Build é habilitado, o *libman.jsno menu de* contexto exibe uma opção **Disable restaurar bibliotecas do lado do cliente no Build** .</span><span class="sxs-lookup"><span data-stu-id="fe005-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="fe005-197">A seleção dessa opção remove a `Microsoft.Web.LibraryManager.Build` referência de pacote do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="fe005-198">Consequentemente, as bibliotecas do lado do cliente não são mais restauradas em cada compilação.</span><span class="sxs-lookup"><span data-stu-id="fe005-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="fe005-199">Independentemente da configuração de Restore-on-Build, você pode restaurar manualmente a qualquer momento do *libman.jsno* menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="fe005-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="fe005-200">Para obter mais informações, consulte [restaurar arquivos manualmente](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="fe005-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="fe005-201">Restaurar arquivos manualmente</span><span class="sxs-lookup"><span data-stu-id="fe005-201">Restore files manually</span></span>

<span data-ttu-id="fe005-202">Para restaurar manualmente os arquivos de biblioteca:</span><span class="sxs-lookup"><span data-stu-id="fe005-202">To manually restore library files:</span></span>

* <span data-ttu-id="fe005-203">Para todos os projetos na solução:</span><span class="sxs-lookup"><span data-stu-id="fe005-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="fe005-204">Clique com o botão direito do mouse no nome da solução em **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="fe005-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="fe005-205">Selecione a opção **restaurar bibliotecas do lado do cliente** .</span><span class="sxs-lookup"><span data-stu-id="fe005-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="fe005-206">Para um projeto específico:</span><span class="sxs-lookup"><span data-stu-id="fe005-206">For a specific project:</span></span>
  * <span data-ttu-id="fe005-207">Clique com o botão direito do mouse no arquivo *libman.js* no **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="fe005-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="fe005-208">Selecione a opção **restaurar bibliotecas do lado do cliente** .</span><span class="sxs-lookup"><span data-stu-id="fe005-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="fe005-209">Enquanto a operação de restauração está em execução:</span><span class="sxs-lookup"><span data-stu-id="fe005-209">While the restore operation is running:</span></span>

* <span data-ttu-id="fe005-210">O ícone de Central de Status de Tarefas (TSC) na barra de status do Visual Studio será animado e a operação de restauração de leitura será *iniciada*.</span><span class="sxs-lookup"><span data-stu-id="fe005-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="fe005-211">Clicar no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidas.</span><span class="sxs-lookup"><span data-stu-id="fe005-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="fe005-212">As mensagens serão enviadas à barra de status e ao feed do **Gerenciador de bibliotecas** da janela de **saída** .</span><span class="sxs-lookup"><span data-stu-id="fe005-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="fe005-213">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fe005-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="fe005-214">Excluir arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="fe005-214">Delete library files</span></span>

<span data-ttu-id="fe005-215">Para executar a operação de *limpeza* , que exclui os arquivos de biblioteca restaurados anteriormente no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fe005-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="fe005-216">Clique com o botão direito do mouse no arquivo *libman.js* no **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="fe005-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="fe005-217">Selecione a opção **limpar bibliotecas do lado do cliente** .</span><span class="sxs-lookup"><span data-stu-id="fe005-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="fe005-218">Para evitar a remoção não intencional de arquivos que não são da biblioteca, a operação de limpeza não exclui diretórios inteiros.</span><span class="sxs-lookup"><span data-stu-id="fe005-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="fe005-219">Ele remove apenas os arquivos que foram incluídos na restauração anterior.</span><span class="sxs-lookup"><span data-stu-id="fe005-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="fe005-220">Enquanto a operação de limpeza está em execução:</span><span class="sxs-lookup"><span data-stu-id="fe005-220">While the clean operation is running:</span></span>

* <span data-ttu-id="fe005-221">O ícone de TSC na barra de status do Visual Studio será animado e a operação de leitura de *bibliotecas do cliente será iniciada*.</span><span class="sxs-lookup"><span data-stu-id="fe005-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="fe005-222">Clicar no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidas.</span><span class="sxs-lookup"><span data-stu-id="fe005-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="fe005-223">As mensagens são enviadas à barra de status e ao feed do **Gerenciador de bibliotecas** da janela de **saída** .</span><span class="sxs-lookup"><span data-stu-id="fe005-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="fe005-224">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fe005-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="fe005-225">A operação de limpeza somente exclui arquivos do projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="fe005-226">Os arquivos de biblioteca permanecem no cache para uma recuperação mais rápida em operações futuras de restauração.</span><span class="sxs-lookup"><span data-stu-id="fe005-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="fe005-227">Para gerenciar arquivos de biblioteca armazenados no cache do computador local, use a [CLI do LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="fe005-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="fe005-228">Desinstalar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="fe005-228">Uninstall library files</span></span>

<span data-ttu-id="fe005-229">Para desinstalar os arquivos de biblioteca:</span><span class="sxs-lookup"><span data-stu-id="fe005-229">To uninstall library files:</span></span>

* <span data-ttu-id="fe005-230">Abra *libman.jsem*.</span><span class="sxs-lookup"><span data-stu-id="fe005-230">Open *libman.json*.</span></span>
* <span data-ttu-id="fe005-231">Posicione o cursor dentro do `libraries` literal de objeto correspondente.</span><span class="sxs-lookup"><span data-stu-id="fe005-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="fe005-232">Clique no ícone de lâmpada que aparece na margem esquerda e selecione \*\*desinstalar \<library_name> @ \<library_version> \*\*:</span><span class="sxs-lookup"><span data-stu-id="fe005-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Opção de menu de contexto desinstalar biblioteca](_static/uninstall-menu-option.png)

<span data-ttu-id="fe005-234">Como alternativa, você pode editar e salvar manualmente o manifesto LibMan (*libman.jsem*).</span><span class="sxs-lookup"><span data-stu-id="fe005-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="fe005-235">A [operação de restauração](#restore-library-files) é executada quando o arquivo é salvo.</span><span class="sxs-lookup"><span data-stu-id="fe005-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="fe005-236">Os arquivos de biblioteca que não estão mais definidos no *libman.jsem* são removidos do projeto.</span><span class="sxs-lookup"><span data-stu-id="fe005-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="fe005-237">Atualizar versão da biblioteca</span><span class="sxs-lookup"><span data-stu-id="fe005-237">Update library version</span></span>

<span data-ttu-id="fe005-238">Para verificar se há uma versão atualizada da biblioteca:</span><span class="sxs-lookup"><span data-stu-id="fe005-238">To check for an updated library version:</span></span>

* <span data-ttu-id="fe005-239">Abra *libman.jsem*.</span><span class="sxs-lookup"><span data-stu-id="fe005-239">Open *libman.json*.</span></span>
* <span data-ttu-id="fe005-240">Posicione o cursor dentro do `libraries` literal de objeto correspondente.</span><span class="sxs-lookup"><span data-stu-id="fe005-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="fe005-241">Clique no ícone de lâmpada que aparece na margem esquerda.</span><span class="sxs-lookup"><span data-stu-id="fe005-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="fe005-242">Passe o mouse sobre **verificar se há atualizações**.</span><span class="sxs-lookup"><span data-stu-id="fe005-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="fe005-243">LibMan verifica se há uma versão de biblioteca mais recente do que a versão instalada.</span><span class="sxs-lookup"><span data-stu-id="fe005-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="fe005-244">Os seguintes resultados podem ocorrer:</span><span class="sxs-lookup"><span data-stu-id="fe005-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="fe005-245">Uma mensagem **nenhuma atualização encontrada** será exibida se a versão mais recente já estiver instalada.</span><span class="sxs-lookup"><span data-stu-id="fe005-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="fe005-246">A versão estável mais recente será exibida se ainda não estiver instalada.</span><span class="sxs-lookup"><span data-stu-id="fe005-246">The latest stable version is displayed if not already installed.</span></span>

  ![Opção de menu de contexto verificar se há atualizações](_static/update-menu-option.png)

* <span data-ttu-id="fe005-248">Se um pré-lançamento mais recente do que a versão instalada estiver disponível, o pré-lançamento será exibido.</span><span class="sxs-lookup"><span data-stu-id="fe005-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="fe005-249">Para fazer o downgrade para uma versão mais antiga da biblioteca, edite manualmente o *libman.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="fe005-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="fe005-250">Quando o arquivo for salvo, a [operação de restauração](#restore-library-files)do LibMan:</span><span class="sxs-lookup"><span data-stu-id="fe005-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="fe005-251">Remove arquivos redundantes da versão anterior.</span><span class="sxs-lookup"><span data-stu-id="fe005-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="fe005-252">Adiciona arquivos novos e atualizados da nova versão.</span><span class="sxs-lookup"><span data-stu-id="fe005-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe005-253">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fe005-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="fe005-254">Repositório do GitHub do LibMan</span><span class="sxs-lookup"><span data-stu-id="fe005-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
