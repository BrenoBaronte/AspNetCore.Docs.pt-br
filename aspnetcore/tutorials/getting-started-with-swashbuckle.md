---
title: Introdução ao Swashbuckle e ao ASP.NET Core
author: zuckerthoben
description: Saiba como adicionar o Swashbuckle ao seu projeto de API Web ASP.NET Core para integrar a interface do usuário do Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
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
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: df9c78770f3494b1ae9e0d3604926d77cb1e67bb
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99578315"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Introdução ao Swashbuckle e ao ASP.NET Core

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([como baixar](xref:index#how-to-download-a-sample))

O Swashbuckle tem três componentes principais:

* [Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): um modelo de objeto e um middleware do Swagger para expor objetos `SwaggerDocument` como pontos de extremidade JSON.

* [Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): um gerador do Swagger cria objetos `SwaggerDocument` diretamente de modelos, controladores e rotas. Normalmente, ela é combinada com o middleware de ponto de extremidade de Swagger para expor automaticamente o JSON do Swagger.

* [Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): uma versão incorporada da ferramenta de interface do usuário do Swagger. Ele interpreta o JSON do Swagger a fim de criar uma experiência rica e personalizável para descrever a funcionalidade da API Web. Ele inclui os agentes de teste internos para os métodos públicos.

## <a name="package-installation"></a>Instalação do pacote

O Swashbuckle pode ser adicionado com as seguintes abordagens:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Da janela **Console do Gerenciador de Pacotes**:
  * Ir para **Exibir**  >  **outro**  >  **console do Gerenciador de pacotes** do Windows
  * Navegue para o diretório no qual o arquivo *TodoApi.csproj* está localizado
  * Execute o seguinte comando:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.6.3
    ```

* Da caixa de diálogo **Gerenciar Pacotes NuGet**:
  * Clique com o botão direito do mouse no projeto no **Gerenciador de soluções**  >  **gerenciar pacotes NuGet**
  * Defina a **Origem do pacote** para "nuget.org"
  * Verifique se a opção "Incluir pré-lançamento" está habilitada
  * Insira "Swashbuckle.AspNetCore" na caixa de pesquisa
  * Selecione o pacote "Swashbuckle.AspNetCore" mais recente na guia **Procurar** e clique em **Instalar**

### <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta *pacotes* em **painel de soluções**  >  **adicionar pacotes...**
* Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"
* Verifique se a opção "Mostrar pacotes de pré-lançamento" está habilitada
* Insira "Swashbuckle.AspNetCore" na caixa de pesquisa
* Selecione o pacote "Swashbuckle.AspNetCore" mais recente no painel de resultados e clique em **Adicionar Pacote**

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o comando a seguir do **Terminal Integrado**:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

### <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Execute o comando a seguir:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

---

## <a name="add-and-configure-swagger-middleware"></a>Adicionar e configurar o middleware do Swagger

Adicione o gerador do Swagger à coleção de serviços no método `Startup.ConfigureServices`:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

No método `Startup.Configure`, habilite o middleware para atender ao documento JSON gerado e à interface do usuário do Swagger:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> O Swashbuckle se baseia no MVC do <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> para descobrir as rotas e os pontos de extremidade. Se o projeto chamar <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> , as rotas e os pontos de extremidade serão descobertos automaticamente. Ao chamar <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> , o <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> método deve ser chamado explicitamente. Para obter mais informações, consulte [Swashbuckle, ApiExplorer e Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).

A chamada do método `UseSwaggerUI` precedente habilita o [middleware de arquivos estáticos](xref:fundamentals/static-files). Se estiver direcionando .NET Framework ou o .NET Core 1. x, adicione o pacote NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.

Inicie o aplicativo e navegue até `http://localhost:<port>/swagger/v1/swagger.json`. O documento gerado descrevendo os pontos de extremidade aparece como mostrado na [especificação de openapi (openapi.jsem)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).

A interface do usuário do Swagger pode ser encontrada em `http://localhost:<port>/swagger`. Explore a API por meio da interface do usuário do Swagger e incorpore-a em outros programas.

> [!TIP]
> Para atender à interface do usuário do Swagger na raiz do aplicativo (`http://localhost:<port>/`), defina a propriedade `RoutePrefix` como uma cadeia de caracteres vazia:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

Se estiver usando diretórios com o IIS ou um proxy reverso, defina o ponto de extremidade do Swagger como um caminho relativo usando o prefixo `./`. Por exemplo, `./swagger/v1/swagger.json`. Usar o `/swagger/v1/swagger.json` instrui o aplicativo a procurar o arquivo JSON na raiz verdadeira da URL (mais o prefixo da rota, se usado). Por exemplo, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` ao invés de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

> [!NOTE]
> Por padrão, swashbuckle gera e expõe o JSON do Swagger na versão 3,0 da especificação &mdash; oficialmente chamada de especificação openapi. Para oferecer suporte à compatibilidade com versões anteriores, você pode optar por expor o JSON no formato 2,0 em vez disso. Esse formato 2,0 é importante para integrações como o Microsoft Power apps e Microsoft Flow que atualmente dão suporte à versão 2,0 do OpenAPI. Para aceitar o formato 2,0, defina a `SerializeAsV2` propriedade em `Startup.Configure` :
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a>Personalizar e estender

O Swagger fornece opções para documentar o modelo de objeto e personalizar a interface do usuário para corresponder ao seu tema.

Na classe `Startup`, adicione os seguintes namespaces:

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Descrição e informações da API

A ação de configuração passada para o método `AddSwaggerGen` adiciona informações como o autor, a licença e a descrição:

Na classe `Startup`, importe o namespace a seguir para usar a classe `OpenApiInfo`:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Usando a `OpenApiInfo` classe, modifique as informações exibidas na interface do usuário:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

A interface do usuário do Swagger exibe as informações da versão:

![A interface do usuário do Swagger com informações de versão: descrição, autor e link veja mais](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>Comentários XML

Comentários XML podem ser habilitados com as seguintes abordagens:

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.
* Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Clique com o botão direito do mouse no projeto em **Gerenciador de soluções** e selecione **Propriedades**.
* Marque a caixa **arquivo de documentação XML** na seção **saída** da guia **Compilar** .

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* No *Painel de Soluções*, pressione **control** e clique no nome do projeto. Navegue até **ferramentas**  >  **Editar arquivo**.
* Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Abra a caixa de diálogo **Opções do Projeto** > **Compilar** > **Compilador**
* Marque a caixa **Gerar documentação XML** na seção **Opções Gerais**

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

A habilitação de comentários XML fornece informações de depuração para os membros e os tipos públicos não documentados. Os membros e tipos não documentados são indicados por mensagem de aviso. Por exemplo, a seguinte mensagem indica uma violação do código de aviso 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Para suprimir os avisos de todo o projeto, defina uma lista separada por ponto e vírgula dos códigos de aviso a serem ignorados no arquivo do projeto. Acrescentar os códigos de aviso ao `$(NoWarn);` também aplica os [valores padrão C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Para suprimir avisos somente para membros específicos, coloque o código nas diretivas de pré-processador [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning). Essa abordagem é útil para o código que não deve ser exposto por meio dos documentos da API. No exemplo a seguir, o código de aviso CS1591 é ignorado para toda a `Program` classe. A imposição do código de aviso é restaurada no fechamento da definição de classe. Especifique vários códigos de aviso com uma lista delimitada por vírgulas.

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

Configure o Swagger para usar o arquivo XML gerado com as instruções anteriores. Para sistemas operacionais Linux ou que não sejam Windows, os caminhos e nomes de arquivo podem diferenciar maiúsculas de minúsculas. Por exemplo, um arquivo *TodoApi.XML* é válido no Windows, mas não no CentOS.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

No código anterior, a [reflexão](/dotnet/csharp/programming-guide/concepts/reflection) é usada para criar um nome de arquivo XML correspondente ao do projeto da API Web. A propriedade [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) é usada para construir um caminho para o arquivo XML. Alguns recursos do Swagger (como os esquemas de parâmetros de entrada ou dos métodos HTTP e os códigos de resposta dos respectivos atributos) funcionam sem o uso de um arquivo de documentação XML. Para a maioria dos recursos, ou seja, resumos de método e descrições dos parâmetros e códigos de resposta, é obrigatório o uso de um arquivo XML.

Adicionar comentários de barra tripla a uma ação aprimora a interface do usuário do Swagger, adicionando a descrição ao cabeçalho da seção. Adicione um [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) elemento acima da `Delete` ação:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

A interface do usuário do Swagger exibe o texto interno do elemento `<summary>` do código anterior:

![A interface do usuário do Swagger, mostrando o comentário XML 'Exclui um TodoItem específico'. para o método DELETE](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

A interface do usuário é controlada pelo esquema JSON gerado:

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

Adicione um [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) elemento à `Create` documentação do método de ação. Ele complementa as informações especificadas no elemento `<summary>` e fornece uma interface de usuário do Swagger mais robusta. O conteúdo do elemento `<remarks>` pode consistir em texto, JSON ou XML.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Observe os aprimoramentos da interface do usuário com esses comentários adicionais:

![Interface do usuário do Swagger com comentários adicionais mostrados](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Anotações de dados

Marque o modelo com atributos, encontrado no namespace [System. ComponentModel. Annotations](/dotnet/api/system.componentmodel.dataannotations) , para ajudar a orientar os componentes da interface do usuário do Swagger.

Adicione o atributo `[Required]` à propriedade `Name` da classe `TodoItem`:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

A presença desse atributo altera o comportamento da interface do usuário e altera o esquema JSON subjacente:

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

Adicione o atributo `[Produces("application/json")]` ao controlador da API. Sua finalidade é declarar que as ações do controlador permitem o tipo de conteúdo de resposta *application/json*:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

A lista suspensa **tipo de conteúdo de resposta** seleciona esse tipo de conteúdo como o padrão para as ações Get do controlador:

![Interface do usuário do Swagger com o tipo de conteúdo de resposta padrão](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

À medida que aumenta o uso de anotações de dados na API Web, a interface do usuário e as páginas de ajuda da API se tornam mais descritivas e úteis.

### <a name="describe-response-types"></a>Descrever os tipos de resposta

Os desenvolvedores que usam uma API Web estão mais preocupados com o que é retornado&mdash;, especificamente, os tipos de resposta e os códigos de erro (se eles não forem padrão). Os tipos de resposta e os códigos de erro são indicados nos comentários XML e nas anotações de dados.

A ação `Create` retorna um código de status HTTP 201 em caso de sucesso. Um código de status HTTP 400 é retornado quando o corpo da solicitação postada é nulo. Sem a documentação adequada na interface do usuário do Swagger, o consumidor não tem conhecimento desses resultados esperados. Corrija esse problema adicionando as linhas realçadas no exemplo a seguir:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

A interface do usuário do Swagger agora documenta claramente os códigos de resposta HTTP esperados:

![A interface do usuário do Swagger mostra a descrição da classe de resposta POST, 'Retorna o item de tarefa pendente recém-criado' e '400 – se o item for nulo' para o código de status e o motivo em Mensagens de Resposta](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

No ASP.NET Core 2.2 ou posterior, as convenções podem ser usadas como uma alternativa para decorar explicitamente as ações individuais com `[ProducesResponseType]`. Para obter mais informações, consulte <xref:web-api/advanced/conventions>.

Para dar suporte à `[ProducesResponseType]` decoração, o pacote [swashbuckle. AspNetCore. Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) oferece extensões para habilitar e enriquecer os metadados de resposta, esquema e parâmetro.

::: moniker-end

### <a name="customize-the-ui"></a>Personalizar a interface do usuário

A interface do usuário padrão é funcional e apresentável. No entanto, as páginas de documentação da API devem representar a sua marca ou o seu tema. Para inserir sua marca nos componentes do Swashbuckle é necessário adicionar recursos para atender aos arquivos estáticos e criar a estrutura de pasta para hospedar esses arquivos.

Se você estiver direcionando ao .NET Framework ou ao .NET Core 1.x, adicione o pacote do NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) ao projeto:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

O pacote do NuGet anterior já estará instalado se você estiver direcionando ao .NET Core 2.x e usando o [metapackage](xref:fundamentals/metapackage).

Habilitar o middleware de arquivos estáticos:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

Para injetar folhas de estilo CSS adicionais, adicione-as à pasta *wwwroot* do projeto e especifique o caminho relativo nas opções de middleware:

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```

## <a name="additional-resources"></a>Recursos adicionais

* [Microsoft Learn: melhorar a experiência do desenvolvedor de uma API com a documentação do Swagger](/learn/modules/improve-api-developer-experience-with-swagger/)
