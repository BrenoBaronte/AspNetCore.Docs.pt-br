---
title: 'IU reutilizável Razor em bibliotecas de classes com ASP.NET Core'
author: Rick-Anderson
description: 'Explica como criar Razor uma interface do usuário reutilizável usando exibições parciais em uma biblioteca de classes no ASP.NET Core.'
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: razor-pages/ui-class
ms.openlocfilehash: 0bfdb1932d829ec00c9de1bd38b7920cb1f40c51
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570166"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="ea0d1-103">Criar interface do usuário reutilizável usando o Razor projeto de biblioteca de classes no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea0d1-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="ea0d1-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea0d1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ea0d1-105">Razorexibições, páginas, controladores, modelos de página, [ Razor componentes](xref:blazor/components/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma Razor biblioteca de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="ea0d1-106">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="ea0d1-107">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="ea0d1-108">Quando uma exibição, exibição parcial ou Razor página é encontrada no aplicativo Web e no RCL, a Razor marcação (arquivo *. cshtml* ) no aplicativo Web tem precedência.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup ( *.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="ea0d1-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea0d1-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="ea0d1-110">Criar uma biblioteca de classes que contém a Razor interface do usuário</span><span class="sxs-lookup"><span data-stu-id="ea0d1-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea0d1-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea0d1-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ea0d1-112">No Visual Studio, selecione **criar novo um novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="ea0d1-113">Selecione **Razor biblioteca de classes** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="ea0d1-114">Nomeie a biblioteca (por exemplo, " Razor ClassLib"), > **criar**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="ea0d1-115">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="ea0d1-116">Selecione **páginas de suporte e exibições** se você precisar dar suporte a exibições.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="ea0d1-117">Por padrão, há Razor suporte apenas para páginas.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="ea0d1-118">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-118">Select **Create**.</span></span>

<span data-ttu-id="ea0d1-119">O Razor modelo de biblioteca de classes (RCL) usa como padrão o Razor desenvolvimento de componentes por padrão.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="ea0d1-120">A opção **páginas e exibições de suporte** oferece suporte a páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ea0d1-121">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ea0d1-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ea0d1-122">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="ea0d1-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="ea0d1-124">O Razor modelo de biblioteca de classes (RCL) usa como padrão o Razor desenvolvimento de componentes por padrão.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="ea0d1-125">Passe a `--support-pages-and-views` opção ( `dotnet new razorclasslib --support-pages-and-views` ) para fornecer suporte para páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="ea0d1-126">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="ea0d1-127">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="ea0d1-128">Adicione Razor arquivos ao RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="ea0d1-129">Os modelos de ASP.NET Core assumem que o conteúdo RCL está na pasta *áreas* .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="ea0d1-130">Consulte [layout de páginas RCL](#rcl-pages-layout) para criar um RCL que expõe o conteúdo em `~/Pages` em vez de `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="ea0d1-131">Conteúdo de RCL de referência</span><span class="sxs-lookup"><span data-stu-id="ea0d1-131">Reference RCL content</span></span>

<span data-ttu-id="ea0d1-132">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="ea0d1-133">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-133">NuGet package.</span></span> <span data-ttu-id="ea0d1-134">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="ea0d1-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="ea0d1-136">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="ea0d1-137">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="ea0d1-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="ea0d1-138">Quando uma exibição, exibição parcial ou Razor página é encontrada no aplicativo Web e no RCL, a Razor marcação (arquivo *. cshtml* ) no aplicativo Web tem precedência.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup ( *.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="ea0d1-139">Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="ea0d1-140">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="ea0d1-141">Copie a exibição parcial *Razor UIClassLib/areas/MyFeature/Pages/shared/_Message. cshtml* para *WebApp1/areas/MyFeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="ea0d1-142">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="ea0d1-143">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="ea0d1-144">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="ea0d1-144">RCL Pages layout</span></span>

<span data-ttu-id="ea0d1-145">Para fazer referência ao conteúdo do RCL como se ele fosse parte da pasta *páginas* do aplicativo Web, crie o projeto RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="ea0d1-146">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="ea0d1-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="ea0d1-147">*RazorUIClassLib/páginas/compartilhado*</span><span class="sxs-lookup"><span data-stu-id="ea0d1-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="ea0d1-148">Suponha que *Razor UIClassLib/Pages/Shared* contenham dois arquivos parciais: *_Header. cshtml* e *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="ea0d1-149">As `<partial>` marcas podem ser adicionadas ao arquivo *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ea0d1-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="ea0d1-150">Criar um RCL com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="ea0d1-150">Create an RCL with static assets</span></span>

<span data-ttu-id="ea0d1-151">Um RCL pode exigir ativos estáticos complementares que podem ser referenciados pelo RCL ou pelo aplicativo de consumo do RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="ea0d1-152">ASP.NET Core permite criar RCLs que incluem ativos estáticos disponíveis para um aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="ea0d1-153">Para incluir ativos complementares como parte de um RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua todos os arquivos necessários nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="ea0d1-154">Ao empacotar um RCL, todos os ativos complementares na pasta *wwwroot* são incluídos automaticamente no pacote.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="ea0d1-155">Use o `dotnet pack` comando em vez da versão NuGet.exe `nuget pack` .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="ea0d1-156">Excluir ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="ea0d1-156">Exclude static assets</span></span>

<span data-ttu-id="ea0d1-157">Para excluir ativos estáticos, adicione o caminho de exclusão desejado ao `$(DefaultItemExcludes)` grupo de propriedades no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="ea0d1-158">Separe as entradas com um ponto e vírgula ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="ea0d1-159">No exemplo a seguir, a folha de estilo *lib. css* na pasta *wwwroot* não é considerada um ativo estático e não está incluída no RCL publicado:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="ea0d1-160">Integração do typescript</span><span class="sxs-lookup"><span data-stu-id="ea0d1-160">Typescript integration</span></span>

<span data-ttu-id="ea0d1-161">Para incluir arquivos TypeScript em um RCL:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="ea0d1-162">Coloque os arquivos TypeScript ( *. TS* ) fora da pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-162">Place the TypeScript files ( *.ts* ) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="ea0d1-163">Por exemplo, coloque os arquivos em uma pasta de *cliente* .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="ea0d1-164">Configure a saída de compilação do TypeScript para a pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="ea0d1-165">Defina a `TypescriptOutDir` propriedade dentro de um `PropertyGroup` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="ea0d1-166">Inclua o destino TypeScript como uma dependência do `ResolveCurrentProjectStaticWebAssets` destino adicionando o seguinte destino dentro de um `PropertyGroup` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="ea0d1-167">Consumir conteúdo de um RCL referenciado</span><span class="sxs-lookup"><span data-stu-id="ea0d1-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="ea0d1-168">Os arquivos incluídos na pasta *wwwroot* do RCL são expostos para o aplicativo RCL ou consumidor sob o prefixo `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="ea0d1-169">Por exemplo, uma biblioteca chamada *Razor . Class. lib* resulta em um caminho para o conteúdo estático em `_content/Razor.Class.Lib/` .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="ea0d1-170">Ao produzir um pacote NuGet e o nome do assembly não for igual à ID do pacote, use a ID do pacote para `{LIBRARY NAME}` .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="ea0d1-171">O aplicativo de consumo faz referência a ativos estáticos fornecidos pela biblioteca com `<script>` , `<style>` , `<img>` e outras marcas HTML.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="ea0d1-172">O aplicativo de consumo deve ter o [suporte a arquivo estático](xref:fundamentals/static-files) habilitado em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="ea0d1-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="ea0d1-173">Ao executar o aplicativo de consumo da saída da compilação ( `dotnet run` ), os ativos da Web estáticos são habilitados por padrão no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="ea0d1-174">Para dar suporte a ativos em outros ambientes ao executar a partir da saída da compilação, chame `UseStaticWebAssets` no construtor de hosts em *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea0d1-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="ea0d1-175">`UseStaticWebAssets`A chamada não é necessária ao executar um aplicativo da saída publicada ( `dotnet publish` ).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="ea0d1-176">Fluxo de desenvolvimento de vários projetos</span><span class="sxs-lookup"><span data-stu-id="ea0d1-176">Multi-project development flow</span></span>

<span data-ttu-id="ea0d1-177">Quando o aplicativo de consumo for executado:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-177">When the consuming app runs:</span></span>

* <span data-ttu-id="ea0d1-178">Os ativos no RCL permanecem em suas pastas originais.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="ea0d1-179">Os ativos não são movidos para o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="ea0d1-180">Qualquer alteração na pasta *wwwroot* do RCL é refletida no aplicativo de consumo depois que o RCL é recriado e sem recriar o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="ea0d1-181">Quando o RCL é criado, é produzido um manifesto que descreve os locais estáticos de ativos da Web.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="ea0d1-182">O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="ea0d1-183">Quando um novo ativo é adicionado a um RCL, o RCL deve ser recriado para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="ea0d1-184">Publicar</span><span class="sxs-lookup"><span data-stu-id="ea0d1-184">Publish</span></span>

<span data-ttu-id="ea0d1-185">Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes referenciados são copiados para a pasta *wwwroot* do aplicativo publicado em `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ea0d1-186">Razorexibições, páginas, controladores, modelos de página, [ Razor componentes](xref:blazor/components/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma Razor biblioteca de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-186">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="ea0d1-187">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="ea0d1-188">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="ea0d1-189">Quando uma exibição, exibição parcial ou Razor página é encontrada no aplicativo Web e no RCL, a Razor marcação (arquivo *. cshtml* ) no aplicativo Web tem precedência.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup ( *.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="ea0d1-190">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea0d1-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="ea0d1-191">Criar uma biblioteca de classes que contém a Razor interface do usuário</span><span class="sxs-lookup"><span data-stu-id="ea0d1-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea0d1-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea0d1-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ea0d1-193">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ea0d1-194">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ea0d1-195">Nomeie a biblioteca (por exemplo, " Razor ClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-195">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="ea0d1-196">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="ea0d1-197">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="ea0d1-198">Selecione **Razor biblioteca de classes** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-198">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="ea0d1-199">Um RCL tem o seguinte arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="ea0d1-200">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ea0d1-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ea0d1-201">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="ea0d1-202">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="ea0d1-203">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="ea0d1-204">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="ea0d1-205">Adicione Razor arquivos ao RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="ea0d1-206">Os modelos de ASP.NET Core assumem que o conteúdo RCL está na pasta *áreas* .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="ea0d1-207">Consulte [layout de páginas RCL](#rcl-pages-layout) para criar um RCL que expõe o conteúdo em `~/Pages` em vez de `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="ea0d1-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="ea0d1-208">Conteúdo de RCL de referência</span><span class="sxs-lookup"><span data-stu-id="ea0d1-208">Reference RCL content</span></span>

<span data-ttu-id="ea0d1-209">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="ea0d1-210">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-210">NuGet package.</span></span> <span data-ttu-id="ea0d1-211">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="ea0d1-212">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="ea0d1-213">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="ea0d1-214">Walkthrough: criar um projeto RCL e usar de um Razor projeto de páginas</span><span class="sxs-lookup"><span data-stu-id="ea0d1-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="ea0d1-215">Você pode baixar o [projeto completo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="ea0d1-216">O download de exemplo contém um código adicional e links que facilitam o teste do projeto.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="ea0d1-217">Você pode deixar comentários [nesse problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="ea0d1-218">Testar o aplicativo de download</span><span class="sxs-lookup"><span data-stu-id="ea0d1-218">Test the download app</span></span>

<span data-ttu-id="ea0d1-219">Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea0d1-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea0d1-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea0d1-221">Abra o arquivo *.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="ea0d1-222">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ea0d1-223">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ea0d1-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ea0d1-224">Em um prompt de comando no diretório *cli* , crie a RCL e o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="ea0d1-225">Acesse o diretório *WebApp1* e execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="ea0d1-226">Siga as instruções em [Testar WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="ea0d1-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="ea0d1-227">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="ea0d1-227">Create an RCL</span></span>

<span data-ttu-id="ea0d1-228">Nesta seção, um RCL é criado.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-228">In this section, an RCL is created.</span></span> <span data-ttu-id="ea0d1-229">Razor os arquivos são adicionados ao RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-229">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea0d1-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea0d1-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea0d1-231">Crie o projeto da RCL:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-231">Create the RCL project:</span></span>

* <span data-ttu-id="ea0d1-232">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ea0d1-233">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ea0d1-234">Nomeie o aplicativo **Razor UIClassLib** como > **OK**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-234">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="ea0d1-235">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="ea0d1-236">Selecione **Razor biblioteca de classes** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-236">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="ea0d1-237">Adicione um Razor arquivo de exibição parcial chamado *Razor UIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ea0d1-238">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ea0d1-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ea0d1-239">Na linha de comando, execute o seguinte:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="ea0d1-240">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-240">The preceding commands:</span></span>

* <span data-ttu-id="ea0d1-241">Cria o `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="ea0d1-242">Cria uma Razor página _Message e a adiciona ao RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="ea0d1-243">O parâmetro `-np` cria a página sem um `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="ea0d1-244">Cria um arquivo [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) e o adiciona ao RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="ea0d1-245">O arquivo *_ViewStart. cshtml* é necessário para usar o layout do Razor projeto de páginas (que é adicionado na próxima seção).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="ea0d1-246">Adicionar Razor arquivos e pastas ao projeto</span><span class="sxs-lookup"><span data-stu-id="ea0d1-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="ea0d1-247">Substitua a marcação em *Razor UIClassLib/areas/MyFeature/páginas/Shared/_Message. cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="ea0d1-248">Substitua a marcação em *Razor UIClassLib/areas/MyFeature/Pages/página1. cshtml* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="ea0d1-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="ea0d1-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="ea0d1-250">Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="ea0d1-251">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="ea0d1-252">Para obter mais informações sobre o *_ViewImports. cshtml* , consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="ea0d1-252">For more information on *_ViewImports.cshtml* , see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="ea0d1-253">Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="ea0d1-254">A saída da compilação contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="ea0d1-255">*RazorUIClassLib.Views.dll* contém o Razor conteúdo compilado.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="ea0d1-256">Usar a Razor biblioteca de interface do usuário de um Razor projeto de páginas</span><span class="sxs-lookup"><span data-stu-id="ea0d1-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea0d1-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea0d1-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea0d1-258">Criar o Razor aplicativo Web de páginas:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="ea0d1-259">No **Gerenciador de Soluções** , clique com o botão direito do mouse na solução > **Adicionar** >  **Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-259">From **Solution Explorer** , right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="ea0d1-260">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ea0d1-261">Nomeie o aplicativo como **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="ea0d1-262">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="ea0d1-263">Selecione **Aplicativo Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="ea0d1-264">No **Gerenciador de Soluções** , clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-264">From **Solution Explorer** , right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="ea0d1-265">No **Gerenciador de Soluções** , clique com o botão direito do mouse em **WebApp1** e selecione **Dependências de Build** > **Dependências do Projeto**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-265">From **Solution Explorer** , right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="ea0d1-266">Verifique **Razor UIClassLib** como uma dependência de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-266">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="ea0d1-267">No **Gerenciador de Soluções** , clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** > **Referência**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-267">From **Solution Explorer** , right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="ea0d1-268">Na caixa de diálogo **Gerenciador de referências** , marque **Razor UIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="ea0d1-269">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ea0d1-270">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="ea0d1-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ea0d1-271">Crie um Razor aplicativo Web de páginas e um arquivo de solução contendo o Razor aplicativo Pages e o RCL:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="ea0d1-272">Crie e execute o aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="ea0d1-273">Testar o WebApp1</span><span class="sxs-lookup"><span data-stu-id="ea0d1-273">Test WebApp1</span></span>

<span data-ttu-id="ea0d1-274">Navegue até `/MyFeature/Page1` para verificar se a Razor biblioteca de classes da interface do usuário está em uso.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="ea0d1-275">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="ea0d1-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="ea0d1-276">Quando uma exibição, exibição parcial ou Razor página é encontrada no aplicativo Web e no RCL, a Razor marcação (arquivo *. cshtml* ) no aplicativo Web tem precedência.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup ( *.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="ea0d1-277">Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="ea0d1-278">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="ea0d1-279">Copie a exibição parcial *Razor UIClassLib/areas/MyFeature/Pages/shared/_Message. cshtml* para *WebApp1/areas/MyFeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="ea0d1-280">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="ea0d1-281">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="ea0d1-282">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="ea0d1-282">RCL Pages layout</span></span>

<span data-ttu-id="ea0d1-283">Para fazer referência ao conteúdo do RCL como se ele fosse parte da pasta *páginas* do aplicativo Web, crie o projeto RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="ea0d1-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="ea0d1-284">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="ea0d1-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="ea0d1-285">*RazorUIClassLib/páginas/compartilhado*</span><span class="sxs-lookup"><span data-stu-id="ea0d1-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="ea0d1-286">Suponha que *Razor UIClassLib/Pages/Shared* contenham dois arquivos parciais: *_Header. cshtml* e *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ea0d1-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="ea0d1-287">As `<partial>` marcas podem ser adicionadas ao arquivo *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ea0d1-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ea0d1-288">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ea0d1-288">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
