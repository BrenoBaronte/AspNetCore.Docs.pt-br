---
title: Introdução ao NSwag e ao ASP.NET Core
author: zuckerthoben
description: Saiba como usar o NSwag para gerar a documentação e as páginas de ajuda para uma API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 78d58d4d544c33862cf502ce63e83560e8009c65
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060567"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="76994-103">Introdução ao NSwag e ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76994-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="76994-104">Por [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) e [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="76994-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="76994-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76994-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="76994-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76994-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="76994-107">NSwag oferece os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="76994-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="76994-108">A capacidade de utilizar a interface do usuário do Swagger e o gerador do Swagger.</span><span class="sxs-lookup"><span data-stu-id="76994-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="76994-109">Recursos flexíveis de geração de código.</span><span class="sxs-lookup"><span data-stu-id="76994-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="76994-110">Com o NSwag, você não precisa de uma API existente&mdash;, pois pode usar APIs de terceiros que incorporam o Swagger e geram uma implementação de cliente.</span><span class="sxs-lookup"><span data-stu-id="76994-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="76994-111">O NSwag permite acelerar o ciclo de desenvolvimento e adaptar-se facilmente às alterações na API.</span><span class="sxs-lookup"><span data-stu-id="76994-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="76994-112">Registrar o middleware do NSwag</span><span class="sxs-lookup"><span data-stu-id="76994-112">Register the NSwag middleware</span></span>

<span data-ttu-id="76994-113">Registre o middleware do NSwag para:</span><span class="sxs-lookup"><span data-stu-id="76994-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="76994-114">Gerar a especificação do Swagger para a API Web implementada.</span><span class="sxs-lookup"><span data-stu-id="76994-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="76994-115">Oferecer a interface do usuário do Swagger para navegar e testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="76994-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="76994-116">Para usar o middleware ASP.NET Core do [NSwag](https://github.com/RicoSuter/NSwag), instale o pacote do NuGet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="76994-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="76994-117">Este pacote contém o middleware para gerar e oferecer a especificação do Swagger, interface do usuário do Swagger (v2 e v3) e a [interface do usuário do ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="76994-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="76994-118">Use uma das seguintes abordagens para instalar o pacote do NuGet do NSwag:</span><span class="sxs-lookup"><span data-stu-id="76994-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76994-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76994-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="76994-120">Da janela **Console do Gerenciador de Pacotes**:</span><span class="sxs-lookup"><span data-stu-id="76994-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="76994-121">Ir para **Exibir**  >  **outro**  >  **console do Gerenciador de pacotes** do Windows</span><span class="sxs-lookup"><span data-stu-id="76994-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="76994-122">Navegue para o diretório no qual o arquivo *TodoApi.csproj* está localizado</span><span class="sxs-lookup"><span data-stu-id="76994-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="76994-123">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="76994-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="76994-124">Da caixa de diálogo **Gerenciar Pacotes NuGet**:</span><span class="sxs-lookup"><span data-stu-id="76994-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="76994-125">Clique com o botão direito do mouse no projeto no **Gerenciador de soluções**  >  **gerenciar pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="76994-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="76994-126">Defina a **Origem do pacote** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="76994-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="76994-127">Insira "NSwag.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="76994-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="76994-128">Selecione o pacote "NSwag.AspNetCore" na guia **Procurar** e clique em **Instalar**</span><span class="sxs-lookup"><span data-stu-id="76994-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76994-129">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="76994-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="76994-130">Clique com o botão direito do mouse na pasta *pacotes* em **painel de soluções**  >  **adicionar pacotes...**</span><span class="sxs-lookup"><span data-stu-id="76994-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="76994-131">Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="76994-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="76994-132">Insira "NSwag.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="76994-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="76994-133">Selecione o pacote "NSwag. AspNetCore" no painel de resultados e clique em **Adicionar pacote**</span><span class="sxs-lookup"><span data-stu-id="76994-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76994-134">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="76994-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="76994-135">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="76994-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="76994-136">Adicionar e configurar o middleware do Swagger</span><span class="sxs-lookup"><span data-stu-id="76994-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="76994-137">Realize estas etapas para adicionar e configurar o Swagger em seu aplicativo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="76994-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="76994-138">No método `Startup.ConfigureServices`, registre os serviços obrigatórios do Swagger:</span><span class="sxs-lookup"><span data-stu-id="76994-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="76994-139">No método `Startup.Configure`, habilite o middleware para atender à especificação do Swagger gerado e à interface do usuário do Swagger:</span><span class="sxs-lookup"><span data-stu-id="76994-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="76994-140">Iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="76994-140">Launch the app.</span></span> <span data-ttu-id="76994-141">Navegue até:</span><span class="sxs-lookup"><span data-stu-id="76994-141">Navigate to:</span></span>
  * <span data-ttu-id="76994-142">`http://localhost:<port>/swagger` para exibir a interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="76994-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="76994-143">`http://localhost:<port>/swagger/v1/swagger.json` para exibir a especificação do Swagger.</span><span class="sxs-lookup"><span data-stu-id="76994-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="76994-144">Geração de código</span><span class="sxs-lookup"><span data-stu-id="76994-144">Code generation</span></span>

<span data-ttu-id="76994-145">Você pode tirar proveito dos recursos de geração de código do NSwag, escolhendo uma das opções a seguir:</span><span class="sxs-lookup"><span data-stu-id="76994-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="76994-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): um aplicativo de área de trabalho do Windows para gerar código de cliente de API em C# ou TypeScript.</span><span class="sxs-lookup"><span data-stu-id="76994-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): A Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="76994-147">Pacotes NuGet [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) ou [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) para a geração de código dentro do seu projeto.</span><span class="sxs-lookup"><span data-stu-id="76994-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="76994-148">NSwag na [linha de comando](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="76994-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="76994-149">O pacote NuGet [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild).</span><span class="sxs-lookup"><span data-stu-id="76994-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="76994-150">O [serviço conectado openapi (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): um serviço conectado do Visual Studio para gerar código de cliente de API em C# ou TypeScript.</span><span class="sxs-lookup"><span data-stu-id="76994-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): A Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="76994-151">Também gera controladores C# para serviços de OpenAPI com o NSwag.</span><span class="sxs-lookup"><span data-stu-id="76994-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="76994-152">Gerar o código com NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="76994-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="76994-153">Instale o NSwagStudio, seguindo as instruções no [repositório GitHub do NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="76994-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span> <span data-ttu-id="76994-154">Na página de versão do NSwag, você pode baixar uma versão do XCOPY que pode ser iniciada sem privilégios de instalação e administrador.</span><span class="sxs-lookup"><span data-stu-id="76994-154">On the NSwag release page you can download an xcopy version which can be started without installation and admin privileges.</span></span>
* <span data-ttu-id="76994-155">Inicie o NSwagStudio e insira a URL do arquivo *swagger.json* na caixa de texto **URL de Especificação do Swagger**.</span><span class="sxs-lookup"><span data-stu-id="76994-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="76994-156">Por exemplo, *http://localhost:44354/swagger/v1/swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="76994-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="76994-157">Clique no botão **Criar Cópia local** para gerar uma representação JSON de sua especificação do Swagger.</span><span class="sxs-lookup"><span data-stu-id="76994-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Criar uma cópia local da especificação do Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="76994-159">Na área **Saídas**, marque a caixa de seleção **Cliente CSharp**.</span><span class="sxs-lookup"><span data-stu-id="76994-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="76994-160">Dependendo do seu projeto, você também pode escolher **Cliente TypeScript** ou **Controlador da API Web CSharp**.</span><span class="sxs-lookup"><span data-stu-id="76994-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="76994-161">Se você selecionar **Controlador da API Web do CSharp**, uma especificação de serviço recompilará o serviço, que servirá como uma geração inversa.</span><span class="sxs-lookup"><span data-stu-id="76994-161">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="76994-162">Clique em **Gerar Saídas** para produzir uma implementação completa de cliente em C# do projeto *TodoApi.NSwag*.</span><span class="sxs-lookup"><span data-stu-id="76994-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="76994-163">Para ver o código de cliente gerado, clique na guia **Cliente do CSharp**:</span><span class="sxs-lookup"><span data-stu-id="76994-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="76994-164">O código do cliente C# é gerado com base nas seleções na guia **configurações** . Modifique as configurações para executar tarefas como, por exemplo, renomeação de namespace padrão e geração de método síncrono.</span><span class="sxs-lookup"><span data-stu-id="76994-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="76994-165">Copie o código C# gerado em um arquivo no projeto do cliente que consumirá a API.</span><span class="sxs-lookup"><span data-stu-id="76994-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="76994-166">Inicie o consumo da API Web:</span><span class="sxs-lookup"><span data-stu-id="76994-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="76994-167">Personalizar a documentação da API</span><span class="sxs-lookup"><span data-stu-id="76994-167">Customize API documentation</span></span>

<span data-ttu-id="76994-168">O Swagger fornece opções para documentar o modelo de objeto para facilitar o consumo da API Web.</span><span class="sxs-lookup"><span data-stu-id="76994-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="76994-169">Descrição e informações da API</span><span class="sxs-lookup"><span data-stu-id="76994-169">API info and description</span></span>

<span data-ttu-id="76994-170">No método `Startup.ConfigureServices`, uma ação de configuração passada para o método `AddSwaggerDocument` adiciona informações como o autor, a licença e a descrição:</span><span class="sxs-lookup"><span data-stu-id="76994-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="76994-171">A interface do usuário do Swagger exibe as informações da versão:</span><span class="sxs-lookup"><span data-stu-id="76994-171">The Swagger UI displays the version's information:</span></span>

![Interface do usuário do Swagger com informações de versão](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="76994-173">Comentários XML</span><span class="sxs-lookup"><span data-stu-id="76994-173">XML comments</span></span>

<span data-ttu-id="76994-174">Para habilitar os comentários XML, execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="76994-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76994-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76994-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="76994-176">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="76994-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="76994-177">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="76994-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="76994-178">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**</span><span class="sxs-lookup"><span data-stu-id="76994-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="76994-179">Marque a caixa **Arquivo de documentação XML** na seção **Saída** da guia **Build**</span><span class="sxs-lookup"><span data-stu-id="76994-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76994-180">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="76994-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="76994-181">No *Painel de Soluções*, pressione **control** e clique no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="76994-181">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="76994-182">Navegue até **ferramentas**  >  **Editar arquivo**.</span><span class="sxs-lookup"><span data-stu-id="76994-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="76994-183">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="76994-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="76994-184">Abra a caixa de diálogo **Opções do Projeto** > **Compilar** > **Compilador**</span><span class="sxs-lookup"><span data-stu-id="76994-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="76994-185">Marque a caixa **Gerar documentação XML** na seção **Opções Gerais**</span><span class="sxs-lookup"><span data-stu-id="76994-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-cli"></a>[<span data-ttu-id="76994-186">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="76994-186">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="76994-187">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="76994-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="76994-188">Anotações de dados</span><span class="sxs-lookup"><span data-stu-id="76994-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="76994-189">Como o NSwag usa [Reflexão](/dotnet/csharp/programming-guide/concepts/reflection), e o tipo recomendado de retorno para ações da API Web é [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), ele não consegue inferir o que sua ação está fazendo e o que ela retorna.</span><span class="sxs-lookup"><span data-stu-id="76994-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="76994-190">Considere o seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="76994-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="76994-191">A ação anterior retorna `IActionResult`, mas dentro da ação, ela está retornando [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) ou [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span><span class="sxs-lookup"><span data-stu-id="76994-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="76994-192">Use anotações de dados para informar aos clientes quais códigos de status HTTP esta ação costuma retornar.</span><span class="sxs-lookup"><span data-stu-id="76994-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="76994-193">Marque a ação com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="76994-193">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="76994-194">Como NSwag usa [reflexão](/dotnet/csharp/programming-guide/concepts/reflection)e o tipo de retorno recomendado para ações da API Web [é \<T> ActionResult](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), ele só pode inferir o tipo de retorno definido por `T` .</span><span class="sxs-lookup"><span data-stu-id="76994-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="76994-195">Não é possível inferir automaticamente outros tipos de retorno possíveis.</span><span class="sxs-lookup"><span data-stu-id="76994-195">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="76994-196">Considere o seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="76994-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="76994-197">A ação anterior retorna `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="76994-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="76994-198">Dentro da ação, ela está retornando [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="76994-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="76994-199">Como o controlador tem o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo, uma resposta [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) também é possível.</span><span class="sxs-lookup"><span data-stu-id="76994-199">Since the controller has the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="76994-200">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="76994-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="76994-201">Use anotações de dados para informar aos clientes quais códigos de status HTTP esta ação costuma retornar.</span><span class="sxs-lookup"><span data-stu-id="76994-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="76994-202">Marque a ação com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="76994-202">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="76994-203">No ASP.NET Core 2.2 ou posterior, é possível usar as convenções em vez de decorar explicitamente as ações individuais com `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="76994-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="76994-204">Para obter mais informações, consulte <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="76994-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="76994-205">O gerador do Swagger agora pode descrever essa ação precisamente e os clientes gerados conseguem saber o que recebem ao chamar o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="76994-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="76994-206">Como recomendação, marque todas as ações com esses atributos.</span><span class="sxs-lookup"><span data-stu-id="76994-206">As a recommendation, mark all actions with these attributes.</span></span>

<span data-ttu-id="76994-207">Para obter diretrizes sobre quais respostas HTTP as ações da API devem retornar, confira a [Especificação RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="76994-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
