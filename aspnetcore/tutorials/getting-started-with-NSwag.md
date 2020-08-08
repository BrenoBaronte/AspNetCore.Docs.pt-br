---
title: Introdução ao NSwag e ao ASP.NET Core
author: zuckerthoben
description: Saiba como usar o NSwag para gerar a documentação e as páginas de ajuda para uma API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 8fd42c7d31edd20c2aae7577c5a490b54ab8129c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022258"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Introdução ao NSwag e ao ASP.NET Core

Por [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) e [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag oferece os seguintes recursos:

* A capacidade de utilizar a interface do usuário do Swagger e o gerador do Swagger.
* Recursos flexíveis de geração de código.

Com o NSwag, você não precisa de uma API existente&mdash;, pois pode usar APIs de terceiros que incorporam o Swagger e geram uma implementação de cliente. O NSwag permite acelerar o ciclo de desenvolvimento e adaptar-se facilmente às alterações na API.

## <a name="register-the-nswag-middleware"></a>Registrar o middleware do NSwag

Registre o middleware do NSwag para:

* Gerar a especificação do Swagger para a API Web implementada.
* Oferecer a interface do usuário do Swagger para navegar e testar a API Web.

Para usar o middleware ASP.NET Core do [NSwag](https://github.com/RicoSuter/NSwag), instale o pacote do NuGet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/). Este pacote contém o middleware para gerar e oferecer a especificação do Swagger, interface do usuário do Swagger (v2 e v3) e a [interface do usuário do ReDoc](https://github.com/Rebilly/ReDoc).

Use uma das seguintes abordagens para instalar o pacote do NuGet do NSwag:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Da janela **Console do Gerenciador de Pacotes**:
  * Ir para **Exibir**  >  **outro**  >  **console do Gerenciador de pacotes** do Windows
  * Navegue para o diretório no qual o arquivo *TodoApi.csproj* está localizado
  * Execute o seguinte comando:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* Da caixa de diálogo **Gerenciar Pacotes NuGet**:
  * Clique com o botão direito do mouse no projeto no **Gerenciador de soluções**  >  **gerenciar pacotes NuGet**
  * Defina a **Origem do pacote** para "nuget.org"
  * Insira "NSwag.AspNetCore" na caixa de pesquisa
  * Selecione o pacote "NSwag.AspNetCore" na guia **Procurar** e clique em **Instalar**

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta *pacotes* em **painel de soluções**  >  **adicionar pacotes...**
* Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"
* Insira "NSwag.AspNetCore" na caixa de pesquisa
* Selecione o pacote "NSwag. AspNetCore" no painel de resultados e clique em **Adicionar pacote**

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Execute o seguinte comando:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Adicionar e configurar o middleware do Swagger

Realize estas etapas para adicionar e configurar o Swagger em seu aplicativo ASP.NET Core:

* No método `Startup.ConfigureServices`, registre os serviços obrigatórios do Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* No método `Startup.Configure`, habilite o middleware para atender à especificação do Swagger gerado e à interface do usuário do Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Iniciar o aplicativo. Navegue até:
  * `http://localhost:<port>/swagger` para exibir a interface do usuário do Swagger.
  * `http://localhost:<port>/swagger/v1/swagger.json` para exibir a especificação do Swagger.

## <a name="code-generation"></a>Geração de código

Você pode tirar proveito dos recursos de geração de código do NSwag, escolhendo uma das opções a seguir:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): um aplicativo de área de trabalho do Windows para gerar código de cliente de API em C# ou TypeScript.
* Pacotes NuGet [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) ou [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) para a geração de código dentro do seu projeto.
* NSwag na [linha de comando](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* O pacote NuGet [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild).
* O [serviço conectado openapi (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): um serviço conectado do Visual Studio para gerar código de cliente de API em C# ou TypeScript. Também gera controladores C# para serviços de OpenAPI com o NSwag.

### <a name="generate-code-with-nswagstudio"></a>Gerar o código com NSwagStudio

* Instale o NSwagStudio, seguindo as instruções no [repositório GitHub do NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). Na página de versão do NSwag, você pode baixar uma versão do XCOPY que pode ser iniciada sem privilégios de instalação e administrador.
* Inicie o NSwagStudio e insira a URL do arquivo *swagger.json* na caixa de texto **URL de Especificação do Swagger**. Por exemplo, *http://localhost:44354/swagger/v1/swagger.json*.
* Clique no botão **Criar Cópia local** para gerar uma representação JSON de sua especificação do Swagger.

  ![Criar uma cópia local da especificação do Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* Na área **Saídas**, marque a caixa de seleção **Cliente CSharp**. Dependendo do seu projeto, você também pode escolher **Cliente TypeScript** ou **Controlador da API Web CSharp**. Se você selecionar **Controlador da API Web do CSharp**, uma especificação de serviço recompilará o serviço, que servirá como uma geração inversa.
* Clique em **Gerar Saídas** para produzir uma implementação completa de cliente em C# do projeto *TodoApi.NSwag*. Para ver o código de cliente gerado, clique na guia **Cliente do CSharp**:

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
> O código do cliente C# é gerado com base nas seleções na guia **configurações** . modifique as configurações para executar tarefas como a renomeação do namespace padrão e a geração de método síncrono.

* Copie o código C# gerado em um arquivo no projeto do cliente que consumirá a API.
* Inicie o consumo da API Web:

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>Personalizar a documentação da API

O Swagger fornece opções para documentar o modelo de objeto para facilitar o consumo da API Web.

### <a name="api-info-and-description"></a>Descrição e informações da API

No método `Startup.ConfigureServices`, uma ação de configuração passada para o método `AddSwaggerDocument` adiciona informações como o autor, a licença e a descrição:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

A interface do usuário do Swagger exibe as informações da versão:

![Interface do usuário do Swagger com informações de versão](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>Comentários XML

Para habilitar os comentários XML, execute as seguintes etapas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.
* Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**
* Marque a caixa **Arquivo de documentação XML** na seção **Saída** da guia **Build**

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* No *Painel de Soluções*, pressione **control** e clique no nome do projeto. Navegue até **ferramentas**  >  **Editar arquivo**.
* Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Abra a caixa de diálogo **Opções do Projeto** > **Compilar** > **Compilador**
* Marque a caixa **Gerar documentação XML** na seção **Opções Gerais**

::: moniker-end

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Anotações de dados

::: moniker range="<= aspnetcore-2.0"

Como o NSwag usa [Reflexão](/dotnet/csharp/programming-guide/concepts/reflection), e o tipo recomendado de retorno para ações da API Web é [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), ele não consegue inferir o que sua ação está fazendo e o que ela retorna.

Considere o exemplo a seguir:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

A ação anterior retorna `IActionResult`, mas dentro da ação, ela está retornando [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) ou [BadRequest](xref:System.Web.Http.ApiController.BadRequest*). Use anotações de dados para informar aos clientes quais códigos de status HTTP esta ação costuma retornar. Marque a ação com os seguintes atributos:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Como NSwag usa [reflexão](/dotnet/csharp/programming-guide/concepts/reflection)e o tipo de retorno recomendado para ações da API Web [é \<T> ActionResult](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), ele só pode inferir o tipo de retorno definido por `T` . Não é possível inferir automaticamente outros tipos de retorno possíveis.

Considere o exemplo a seguir:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

A ação anterior retorna `ActionResult<T>`. Dentro da ação, ela está retornando [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Como o controlador tem o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo, uma resposta [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) também é possível. Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses). Use anotações de dados para informar aos clientes quais códigos de status HTTP esta ação costuma retornar. Marque a ação com os seguintes atributos:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

No ASP.NET Core 2.2 ou posterior, é possível usar as convenções em vez de decorar explicitamente as ações individuais com `[ProducesResponseType]`. Para obter mais informações, consulte <xref:web-api/advanced/conventions>.

::: moniker-end

O gerador do Swagger agora pode descrever essa ação precisamente e os clientes gerados conseguem saber o que recebem ao chamar o ponto de extremidade. Como recomendação, marque todas as ações com esses atributos.

Para obter diretrizes sobre quais respostas HTTP as ações da API devem retornar, confira a [Especificação RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
