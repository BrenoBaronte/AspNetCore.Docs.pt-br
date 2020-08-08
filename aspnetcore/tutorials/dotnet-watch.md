---
title: Desenvolver aplicativos ASP.NET Core usando um observador de arquivo
author: rick-anderson
description: Este tutorial demonstra como instalar e usar a ferramenta observador de arquivo (observação de dotnet) da CLI do .NET Core em um aplicativo do ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: f4987e7eef496f3ba4b8f9bb084816be3b17ada7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022440"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="789ff-103">Desenvolver aplicativos ASP.NET Core usando um observador de arquivo</span><span class="sxs-lookup"><span data-stu-id="789ff-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="789ff-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="789ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="789ff-105">`dotnet watch`é uma ferramenta que executa um comando [CLI do .NET Core](/dotnet/core/tools) quando os arquivos de origem são alterados.</span><span class="sxs-lookup"><span data-stu-id="789ff-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="789ff-106">Por exemplo, uma alteração de arquivo pode disparar uma compilação, execução de teste ou uma implantação.</span><span class="sxs-lookup"><span data-stu-id="789ff-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="789ff-107">Este tutorial usa um aplicativo de API Web existente com dois pontos de extremidade: um que retorna uma soma e outro que retorna um produto.</span><span class="sxs-lookup"><span data-stu-id="789ff-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="789ff-108">O método de produto tem um bug, que é corrigido neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="789ff-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="789ff-109">Baixe o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="789ff-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="789ff-110">Ele consiste em dois projetos: *WebApp* (uma API Web ASP.NET Core) e *WebAppTests* (testes de unidade para a API Web).</span><span class="sxs-lookup"><span data-stu-id="789ff-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="789ff-111">Em um shell de comando, navegue até a pasta *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="789ff-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="789ff-112">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="789ff-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="789ff-113">`dotnet run --project <PROJECT>` pode ser usado para especificar um projeto a ser executado.</span><span class="sxs-lookup"><span data-stu-id="789ff-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="789ff-114">Por exemplo, a execução de `dotnet run --project WebApp` da raiz do aplicativo de exemplo também executará o projeto *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="789ff-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="789ff-115">O resultado do console mostra mensagens semelhantes à seguinte (indicando que o aplicativo está em execução e aguarda solicitações):</span><span class="sxs-lookup"><span data-stu-id="789ff-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="789ff-116">Em um navegador web, navegue até `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="789ff-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="789ff-117">Você deve ver o resultado de `9`.</span><span class="sxs-lookup"><span data-stu-id="789ff-117">You should see the result of `9`.</span></span>

<span data-ttu-id="789ff-118">Navegue para o API do produto (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="789ff-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="789ff-119">Ele retorna `9`, não `20`, conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="789ff-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="789ff-120">Esse problema é corrigido mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="789ff-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="789ff-121">Adicionar `dotnet watch` a um projeto</span><span class="sxs-lookup"><span data-stu-id="789ff-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="789ff-122">A ferramenta de observador de arquivo `dotnet watch` está incluída com a versão 2.1.300 do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="789ff-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="789ff-123">As etapas a seguir são necessárias ao usar uma versão anterior do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="789ff-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="789ff-124">Adicionar uma referência ao pacote de `Microsoft.DotNet.Watcher.Tools` para o arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="789ff-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="789ff-125">Instale o pacote `Microsoft.DotNet.Watcher.Tools` executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="789ff-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="789ff-126">Executar os comandos da CLI do .NET Core usando `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="789ff-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="789ff-127">Qualquer [comando da CLI do .NET Core](/dotnet/core/tools#cli-commands) pode ser executado com `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="789ff-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="789ff-128">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="789ff-128">For example:</span></span>

| <span data-ttu-id="789ff-129">Comando</span><span class="sxs-lookup"><span data-stu-id="789ff-129">Command</span></span> | <span data-ttu-id="789ff-130">Comando com inspeção</span><span class="sxs-lookup"><span data-stu-id="789ff-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="789ff-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="789ff-131">dotnet run</span></span> | <span data-ttu-id="789ff-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="789ff-132">dotnet watch run</span></span> |
| <span data-ttu-id="789ff-133">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="789ff-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="789ff-134">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="789ff-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="789ff-135">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="789ff-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="789ff-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="789ff-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="789ff-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="789ff-137">dotnet test</span></span> | <span data-ttu-id="789ff-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="789ff-138">dotnet watch test</span></span> |

<span data-ttu-id="789ff-139">Executar `dotnet watch run` na pasta *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="789ff-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="789ff-140">O resultado do console indica que `watch` foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="789ff-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="789ff-141">`dotnet watch --project <PROJECT>` pode ser usado para especificar um projeto a ser observado.</span><span class="sxs-lookup"><span data-stu-id="789ff-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="789ff-142">Por exemplo, a execução de `dotnet watch --project WebApp run` da raiz do aplicativo de exemplo também executará e observará o projeto *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="789ff-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="789ff-143">Fazer alterações com `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="789ff-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="789ff-144">Verifique se `dotnet watch` está em execução.</span><span class="sxs-lookup"><span data-stu-id="789ff-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="789ff-145">Corrija o bug no método `Product` do *MathController.cs* para que ele retorne o produto e não a soma:</span><span class="sxs-lookup"><span data-stu-id="789ff-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="789ff-146">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="789ff-146">Save the file.</span></span> <span data-ttu-id="789ff-147">O resultado do console indica que o `dotnet watch` detectou uma alteração de arquivo e reiniciou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="789ff-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="789ff-148">Verifique se `http://localhost:<port number>/api/math/product?a=4&b=5` retorna o resultado correto.</span><span class="sxs-lookup"><span data-stu-id="789ff-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="789ff-149">Executar testes usando o `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="789ff-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="789ff-150">Altere o método `Product` de *MathController.cs* de volta para retornar a soma.</span><span class="sxs-lookup"><span data-stu-id="789ff-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="789ff-151">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="789ff-151">Save the file.</span></span>
1. <span data-ttu-id="789ff-152">Em um shell de comando, navegue até a pasta *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="789ff-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="789ff-153">Execute [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="789ff-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="789ff-154">Execute `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="789ff-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="789ff-155">Seu resultado indica que um teste falhou e que o observador está aguardando as alterações de arquivo:</span><span class="sxs-lookup"><span data-stu-id="789ff-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="789ff-156">Corrija o código do método `Product` para que ele retorne o produto.</span><span class="sxs-lookup"><span data-stu-id="789ff-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="789ff-157">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="789ff-157">Save the file.</span></span>

<span data-ttu-id="789ff-158">`dotnet watch` detecta a alteração de arquivo e executa os testes novamente.</span><span class="sxs-lookup"><span data-stu-id="789ff-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="789ff-159">O resultado do console indica a aprovação nos testes.</span><span class="sxs-lookup"><span data-stu-id="789ff-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="789ff-160">Personalizar lista de arquivos a observar</span><span class="sxs-lookup"><span data-stu-id="789ff-160">Customize files list to watch</span></span>

<span data-ttu-id="789ff-161">Por padrão, o `dotnet-watch` controla todos os arquivos que correspondem aos seguintes padrões glob:</span><span class="sxs-lookup"><span data-stu-id="789ff-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="789ff-162">Mais itens podem ser adicionados à lista de inspeção editando o arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="789ff-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="789ff-163">Os itens podem ser especificados individualmente ou usando padrões glob.</span><span class="sxs-lookup"><span data-stu-id="789ff-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="789ff-164">Recusa de arquivos a serem observados</span><span class="sxs-lookup"><span data-stu-id="789ff-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="789ff-165">`dotnet-watch` pode ser configurado para ignorar as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="789ff-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="789ff-166">Para ignorar arquivos específicos, adicione o atributo `Watch="false"` à definição de um item no arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="789ff-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="789ff-167">Projetos de inspeção personalizados</span><span class="sxs-lookup"><span data-stu-id="789ff-167">Custom watch projects</span></span>

<span data-ttu-id="789ff-168">`dotnet-watch` não é restrito a projetos C#.</span><span class="sxs-lookup"><span data-stu-id="789ff-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="789ff-169">Projetos de inspeção personalizados podem ser criados para lidar com cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="789ff-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="789ff-170">Considere o layout de projeto a seguir:</span><span class="sxs-lookup"><span data-stu-id="789ff-170">Consider the following project layout:</span></span>

* <span data-ttu-id="789ff-171">**testar**</span><span class="sxs-lookup"><span data-stu-id="789ff-171">**test/**</span></span>
  * <span data-ttu-id="789ff-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="789ff-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="789ff-173">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="789ff-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="789ff-174">Se a meta é observar ambos os projetos, crie um arquivo de projeto personalizados configurado para observar os dois projetos:</span><span class="sxs-lookup"><span data-stu-id="789ff-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="789ff-175">Para iniciar a observação de arquivo em ambos os projetos, mude para a pasta de *teste*.</span><span class="sxs-lookup"><span data-stu-id="789ff-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="789ff-176">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="789ff-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="789ff-177">O VSTest é executado quando há qualquer mudança de arquivo no projeto de teste.</span><span class="sxs-lookup"><span data-stu-id="789ff-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="789ff-178">`dotnet-watch` no GitHub</span><span class="sxs-lookup"><span data-stu-id="789ff-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="789ff-179">`dotnet-watch`faz parte do [repositório dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)do github.</span><span class="sxs-lookup"><span data-stu-id="789ff-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
