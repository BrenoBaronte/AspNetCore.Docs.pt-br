---
title: Introdução ao Swashbuckle e ao ASP.NET Core
author: zuckerthoben
description: Saiba como adicionar o Swashbuckle ao seu projeto de API Web ASP.NET Core para integrar a interface do usuário do Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/05/2018
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 945a2ebe138ba6a1f6029f9e867887b1ce8d628f
ms.sourcegitcommit: 09affee3d234cb27ea6fe33bc113b79e68900d22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51191276"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="2fb7b-103">Introdução ao Swashbuckle e ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fb7b-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="2fb7b-104">Por [Shayne Boyer](https://twitter.com/spboyer) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="2fb7b-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="2fb7b-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fb7b-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2fb7b-106">Há três componentes principais bo Swashbuckle:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="2fb7b-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): um modelo de objeto e um middleware do Swagger para expor objetos `SwaggerDocument` como pontos de extremidade JSON.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="2fb7b-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): um gerador do Swagger cria objetos `SwaggerDocument` diretamente de modelos, controladores e rotas.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="2fb7b-109">Normalmente, ele é combinado com o middleware de ponto de extremidade do Swagger para expor automaticamente o JSON do Swagger.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="2fb7b-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): uma versão incorporada da ferramenta de interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="2fb7b-111">Ele interpreta o JSON do Swagger para criar uma experiência rica e personalizável para descrever a funcionalidade da API da Web.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-111">It interprets Swagger JSON to build a rich, customizable experience for describing the Web API functionality.</span></span> <span data-ttu-id="2fb7b-112">Ela inclui o agente de teste interno para os métodos públicos.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="2fb7b-113">Instalação do pacote</span><span class="sxs-lookup"><span data-stu-id="2fb7b-113">Package installation</span></span>

<span data-ttu-id="2fb7b-114">O Swashbuckle pode ser adicionado com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fb7b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fb7b-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2fb7b-116">Da janela **Console do Gerenciador de Pacotes**:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="2fb7b-117">Acesse **Exibição** > **Outras Janelas** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="2fb7b-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="2fb7b-118">Navegue para o diretório no qual o arquivo *TodoApi.csproj* está localizado</span><span class="sxs-lookup"><span data-stu-id="2fb7b-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="2fb7b-119">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* <span data-ttu-id="2fb7b-120">Da caixa de diálogo **Gerenciar Pacotes NuGet**:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="2fb7b-121">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="2fb7b-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="2fb7b-122">Defina a **Origem do pacote** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="2fb7b-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="2fb7b-123">Insira "Swashbuckle.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="2fb7b-123">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="2fb7b-124">Selecione o pacote "Swashbuckle.AspNetCore" na guia **Procurar** e clique em **Instalar**</span><span class="sxs-lookup"><span data-stu-id="2fb7b-124">Select the "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2fb7b-125">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2fb7b-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2fb7b-126">Clique com o botão direito do mouse na pasta *Pacotes* em **Painel de Soluções** > **Adicionar Pacotes...**</span><span class="sxs-lookup"><span data-stu-id="2fb7b-126">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="2fb7b-127">Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="2fb7b-127">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="2fb7b-128">Insira "Swashbuckle.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="2fb7b-128">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="2fb7b-129">Selecione o pacote "Swashbuckle.AspNetCore" no painel de resultados e clique em **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="2fb7b-129">Select the "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2fb7b-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fb7b-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2fb7b-131">Execute o comando a seguir do **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-131">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2fb7b-132">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2fb7b-132">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2fb7b-133">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-133">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="2fb7b-134">Adicionar e configurar o middleware do Swagger</span><span class="sxs-lookup"><span data-stu-id="2fb7b-134">Add and configure Swagger middleware</span></span>

<span data-ttu-id="2fb7b-135">Adicione o gerador do Swagger à coleção de serviços no método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-135">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

<span data-ttu-id="2fb7b-136">Importe o namespace a seguir para usar a classe `Info`:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-136">Import the following namespace to use the `Info` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="2fb7b-137">No método `Startup.Configure`, habilite o middleware para atender ao documento JSON gerado e à interface do usuário do Swagger:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-137">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

<span data-ttu-id="2fb7b-138">A chamada de método `UseSwaggerUI` anterior habilita o [Middleware de arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="2fb7b-138">The preceding `UseSwaggerUI` method call enables the [Static Files Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="2fb7b-139">Se você estiver direcionando ao .NET Framework ou ao .NET Core 1.x, adicione o pacote NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-139">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="2fb7b-140">Inicie o aplicativo e navegue até `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-140">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="2fb7b-141">O documento gerado que descreve os pontos de extremidade é exibido conforme é mostrado na [Especificação do Swagger (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="2fb7b-141">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="2fb7b-142">A interface do usuário do Swagger pode ser encontrada em `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-142">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="2fb7b-143">Explore a API por meio da interface do usuário do Swagger e incorpore-a em outros programas.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-143">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="2fb7b-144">Para atender à interface do usuário do Swagger na raiz do aplicativo (`http://localhost:<port>/`), defina a propriedade `RoutePrefix` como uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-144">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

## <a name="customize-and-extend"></a><span data-ttu-id="2fb7b-145">Personalizar e estender</span><span class="sxs-lookup"><span data-stu-id="2fb7b-145">Customize and extend</span></span>

<span data-ttu-id="2fb7b-146">O Swagger fornece opções para documentar o modelo de objeto e personalizar a interface do usuário para corresponder ao seu tema.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-146">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="2fb7b-147">Descrição e informações da API</span><span class="sxs-lookup"><span data-stu-id="2fb7b-147">API info and description</span></span>

<span data-ttu-id="2fb7b-148">A ação de configuração passada para o método `AddSwaggerGen` adiciona informações como o autor, a licença e a descrição:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-148">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="2fb7b-149">A interface do usuário do Swagger exibe as informações da versão:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-149">The Swagger UI displays the version's information:</span></span>

![A interface do usuário do Swagger com informações de versão: descrição, autor e link veja mais](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="2fb7b-151">comentários XML</span><span class="sxs-lookup"><span data-stu-id="2fb7b-151">XML comments</span></span>

<span data-ttu-id="2fb7b-152">Comentários XML podem ser habilitados com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-152">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fb7b-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fb7b-153">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="2fb7b-154">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-154">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="2fb7b-155">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-155">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="2fb7b-156">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-156">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="2fb7b-157">Marque a caixa **Arquivo de documentação XML** na seção **Saída** da guia **Build**.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-157">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2fb7b-158">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2fb7b-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="2fb7b-159">No *Painel de Soluções*, pressione **control** e clique no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-159">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="2fb7b-160">Navegue até **Ferramentas** > **Editar arquivo**.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-160">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="2fb7b-161">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-161">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="2fb7b-162">Abra a caixa de diálogo **Opções do Projeto** > **Compilar**>**Compilador**</span><span class="sxs-lookup"><span data-stu-id="2fb7b-162">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="2fb7b-163">Marque a caixa **Gerar documentação XML** na seção **Opções Gerais**</span><span class="sxs-lookup"><span data-stu-id="2fb7b-163">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2fb7b-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fb7b-164">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2fb7b-165">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-165">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2fb7b-166">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="2fb7b-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2fb7b-167">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-167">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="2fb7b-168">A habilitação de comentários XML fornece informações de depuração para os membros e os tipos públicos não documentados.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-168">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="2fb7b-169">Os membros e tipos não documentados são indicados por mensagem de aviso.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-169">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="2fb7b-170">Por exemplo, a seguinte mensagem indica uma violação do código de aviso 1591:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-170">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="2fb7b-171">Para suprimir os avisos de todo o projeto, defina uma lista separada por ponto e vírgula dos códigos de aviso a serem ignorados no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-171">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="2fb7b-172">Acrescentar os códigos de aviso ao `$(NoWarn);` também aplica os valores padrão C#.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-172">Appending the warning codes to `$(NoWarn);` applies the C# default values too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="2fb7b-173">Para suprimir avisos somente para membros específicos, coloque o código nas diretivas de pré-processador [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="2fb7b-173">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="2fb7b-174">Essa abordagem é útil para o código que não deve ser exposto por meio dos documentos da API. No exemplo a seguir, o código de aviso CS1591 é ignorado para toda a classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-174">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="2fb7b-175">A imposição do código de aviso é restaurada no fechamento da definição de classe.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-175">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="2fb7b-176">Especifique vários códigos de aviso com uma lista delimitada por vírgulas.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-176">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="2fb7b-177">Configure o Swagger para usar o arquivo XML gerado.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-177">Configure Swagger to use the generated XML file.</span></span> <span data-ttu-id="2fb7b-178">Para sistemas operacionais Linux ou que não sejam Windows, os caminhos e nomes de arquivo podem diferenciar maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-178">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="2fb7b-179">Por exemplo, um arquivo *TodoApi.XML* é válido no Windows, mas não no CentOS.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-179">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="2fb7b-180">No código anterior, a [Reflexão](/dotnet/csharp/programming-guide/concepts/reflection) é usada para criar um nome de arquivo XML correspondente ao do projeto de API da Web.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-180">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the Web API project.</span></span> <span data-ttu-id="2fb7b-181">A propriedade [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory#System_AppContext_BaseDirectory) é usada para construir um caminho para o arquivo XML.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-181">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory#System_AppContext_BaseDirectory) property is used to construct a path to the XML file.</span></span>

<span data-ttu-id="2fb7b-182">Adicionar comentários de barra tripla a uma ação aprimora a interface do usuário do Swagger adicionando a descrição ao cabeçalho da seção.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-182">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="2fb7b-183">Adicione um elemento [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) acima da ação `Delete`:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-183">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="2fb7b-184">A interface do usuário do Swagger exibe o texto interno do elemento `<summary>` do código anterior:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-184">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![A interface do usuário do Swagger, mostrando o comentário XML 'Exclui um TodoItem específico'.](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="2fb7b-187">A interface do usuário é controlada pelo esquema JSON gerado:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-187">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="2fb7b-188">Adicione um elemento [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) na documentação do método da ação `Create`.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-188">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="2fb7b-189">Ele complementa as informações especificadas no elemento `<summary>` e fornece uma interface de usuário do Swagger mais robusta.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-189">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="2fb7b-190">O conteúdo do elemento `<remarks>` pode consistir em texto, JSON ou XML.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-190">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="2fb7b-191">Observe os aprimoramentos da interface do usuário com esses comentários adicionais:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-191">Notice the UI enhancements with these additional comments:</span></span>

![Interface do usuário do Swagger com comentários adicionais mostrados](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="2fb7b-193">Anotações de dados</span><span class="sxs-lookup"><span data-stu-id="2fb7b-193">Data annotations</span></span>

<span data-ttu-id="2fb7b-194">Decore o modelo com atributos, encontrados no namespace [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations), para ajudar a gerar os componentes da interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-194">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="2fb7b-195">Adicione o atributo `[Required]` à propriedade `Name` da classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-195">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="2fb7b-196">A presença desse atributo altera o comportamento da interface do usuário e altera o esquema JSON subjacente:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-196">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="2fb7b-197">Adicione o atributo `[Produces("application/json")]` ao controlador da API.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-197">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="2fb7b-198">Sua finalidade é declarar que as ações do controlador permitem o tipo de conteúdo de resposta *application/json*:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-198">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="2fb7b-199">A lista suspensa **Tipo de Conteúdo de Resposta** seleciona esse tipo de conteúdo como o padrão para ações GET do controlador:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-199">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interface do usuário do Swagger com o tipo de conteúdo de resposta padrão](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="2fb7b-201">À medida que aumenta o uso de anotações de dados na API Web, a interface do usuário e as páginas de ajuda da API se tornam mais descritivas e úteis.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-201">As the usage of data annotations in the Web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="2fb7b-202">Descrever os tipos de resposta</span><span class="sxs-lookup"><span data-stu-id="2fb7b-202">Describe response types</span></span>

<span data-ttu-id="2fb7b-203">Os desenvolvedores de consumo estão mais preocupados com o que é retornado, especificamente, o tipos de resposta e o códigos de erro (se eles não forem padrão).</span><span class="sxs-lookup"><span data-stu-id="2fb7b-203">Consuming developers are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="2fb7b-204">Os tipos de resposta e os códigos de erro são indicados nos comentários XML e nas anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-204">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="2fb7b-205">A ação `Create` retorna um código de status HTTP 201 em caso de sucesso.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-205">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="2fb7b-206">Um código de status HTTP 400 é retornado quando o corpo da solicitação postada é nulo.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-206">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="2fb7b-207">Sem a documentação adequada na interface do usuário do Swagger, o consumidor não tem conhecimento desses resultados esperados.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-207">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="2fb7b-208">Corrija esse problema adicionando as linhas realçadas no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-208">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="2fb7b-209">A interface do usuário do Swagger agora documenta claramente os códigos de resposta HTTP esperados:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-209">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![A interface do usuário do Swagger mostra a descrição da classe de resposta POST, 'Retorna o item de tarefa pendente recém-criado' e '400 – se o item for nulo' para o código de status e o motivo em Mensagens de Resposta](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customize-the-ui"></a><span data-ttu-id="2fb7b-211">Personalizar a interface do usuário</span><span class="sxs-lookup"><span data-stu-id="2fb7b-211">Customize the UI</span></span>

<span data-ttu-id="2fb7b-212">A interface do usuário de estoque é apresentável e funcional.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-212">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="2fb7b-213">No entanto, as páginas de documentação da API devem representar a sua marca ou o seu tema.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-213">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="2fb7b-214">Para inserir sua marca nos componentes do Swashbuckle é necessário adicionar recursos para atender aos arquivos estáticos e criar a estrutura de pasta para hospedar esses arquivos.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-214">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="2fb7b-215">Se você estiver direcionando ao .NET Framework ou ao .NET Core 1.x, adicione o pacote do NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) ao projeto:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-215">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="2fb7b-216">O pacote do NuGet anterior já estará instalado se você estiver direcionando ao .NET Core 2.x e usando o [metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="2fb7b-216">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="2fb7b-217">Habilite o middleware de arquivos estáticos:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-217">Enable the static files middleware:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

<span data-ttu-id="2fb7b-218">Adquira o conteúdo da pasta *dist* do [repositório GitHub da interface do usuário do Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span><span class="sxs-lookup"><span data-stu-id="2fb7b-218">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="2fb7b-219">Essa pasta contém os ativos necessários para a página da interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-219">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="2fb7b-220">Crie uma pasta *swagger/wwwroot/ui* e copie para ela o conteúdo da pasta *dist*.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-220">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="2fb7b-221">Crie um arquivo *custom.css* em *swagger/wwwroot/ui*, com o seguinte CSS para personalizar o cabeçalho da página:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-221">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="2fb7b-222">Referencie *custom.css* no arquivo *index.html* depois de todos os outros arquivos CSS:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-222">Reference *custom.css* in the *index.html* file, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="2fb7b-223">Navegue até a página *index.html* em `http://localhost:<port>/swagger/ui/index.html`.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-223">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="2fb7b-224">Digite `http://localhost:<port>/swagger/v1/swagger.json` na caixa de texto do cabeçalho e clique no botão **Explorar**.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-224">Enter `http://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="2fb7b-225">A página resultante será semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="2fb7b-225">The resulting page looks as follows:</span></span>

![Interface do usuário do Swagger com o título do cabeçalho personalizado](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="2fb7b-227">Há muito mais que você pode fazer com a página.</span><span class="sxs-lookup"><span data-stu-id="2fb7b-227">There's much more you can do with the page.</span></span> <span data-ttu-id="2fb7b-228">Consulte as funcionalidades completas para os recursos de interface do usuário no [repositório GitHub da interface do usuário do Swagger](https://github.com/swagger-api/swagger-ui).</span><span class="sxs-lookup"><span data-stu-id="2fb7b-228">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
