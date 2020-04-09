---
title: 'Tutorial: Crie uma API web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417654"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Tutorial: Crie uma API web com ASP.NET Core

Por [Rick Anderson,](https://twitter.com/RickAndMSFT) [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)

Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.

::: moniker range=">= aspnetcore-3.0"

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de API Web.
> * Adicione uma classe de modelo e um contexto de banco de dados.
> * Faça scaffold de um controlador com métodos CRUD.
> * Configure o roteamento, os caminhos de URL e os valores retornados.
> * Chamar a API Web com o Postman.

No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.

## <a name="overview"></a>Visão geral

Este tutorial cria a seguinte API:

|API | Descrição | Corpo da solicitação | Corpo da resposta |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Obter todos os itens de tarefas pendentes | Nenhum | Matriz de itens de tarefas pendentes|
|GET /api/TodoItems/{id} | Obter um item por ID | Nenhum | Item de tarefas pendentes|
|POST /api/TodoItems | Adicionar um novo item | Item de tarefas pendentes | Item de tarefas pendentes |
|PUT /api/TodoItems/{id} | Atualizar um item &nbsp; existente | Item de tarefas pendentes | Nenhum |
|EXCLUSão /api/TodoItems/{id} &nbsp;&nbsp; | Exclua um &nbsp; item&nbsp; | Nenhum | Nenhum|

O diagrama a seguir mostra o design do aplicativo.

![O cliente é representado por uma caixa à esquerda. Ele envia uma solicitação e recebe uma resposta do aplicativo, uma caixa desenhada à direita. Dentro da caixa do aplicativo, três caixas representam o controlador, o modelo e a camada de acesso a dados. A solicitação é recebida no controlador do aplicativo e as operações de leitura/gravação ocorrem entre o controlador e a camada de acesso a dados. O modelo é serializado e retornado para o cliente na resposta.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Criar um projeto Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo**, selecione **Novo** > **Projeto**.
* Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.
* Nomeie o projeto como *TodoApi* e clique em **Criar**.
* No **Criar uma nova caixa de diálogo ASP.NET Do Aplicativo Da Web,** confirme se **o .NET Core** e **o ASP.NET Core 3.1** estão selecionados. Selecione o modelo **API** e clique em **Criar**.

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.
* Execute os seguintes comandos:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.

  Os comandos anteriores:

  * Cria um novo projeto de API Web e o abre no Visual Studio Code.
  * Adiciona os pacotes do NuGet que são exigidos na próxima seção.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* Na **configuração de sua nova caixa de diálogo API da Web ASP.NET,** selecione **'Estrutura** de*destino' *.NET Core 3.1*.

* Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testar a API

O modelo de projeto cria uma API `WeatherForecast`. Chame o método `Get` em um navegador para testar o aplicativo.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pressione CTRL+F5 para executar o aplicativo. O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.

Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**. Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pressione CTRL+F5 para executar o aplicativo. Em um navegador, acesse a seguinte URL: `https://localhost:5001/WeatherForecast`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Selecione **Executar** > **iniciar depuração** para iniciar o aplicativo. O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente. Um erro HTTP 404 (Não Encontrado) será retornado. Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).

---

Um JSON semelhante ao seguinte será retornado:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Adicionar uma classe de modelo

Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo. O modelo para esse aplicativo é uma única classe `TodoItem`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto. Selecione **Adicionar** > **nova pasta**. Nomeie a pasta como *Modelos*.

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**. Dê à classe o nome *TodoItem* e selecione **Adicionar**.

* Substitua o código do modelo pelo seguinte código:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Adicione uma pasta denominada *Modelos*.

* Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse no projeto. Selecione **Adicionar** > **nova pasta**. Nomeie a pasta como *Modelos*.

  ![nova pasta](first-web-api-mac/_static/folder.png)

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.

* Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.

* Substitua o código do modelo pelo seguinte código:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.

As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.

## <a name="add-a-database-context"></a>Adicionar um contexto de banco de dados

O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados. Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Adicione Microsoft.EntityFrameworkCore.SqlServer

* No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.
* Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.
* Selecione **Microsoft.EntityFrameworkCore.SqlServer** no painel esquerdo.
* Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.
* Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Adicione o contexto de banco de dados TodoContext

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**. Nomeie a classe como *TodoContext* e clique em **Adicionar**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Adicione uma classe denominada `TodoContext` à pasta *Modelos*.

---

* Insira o seguinte código:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrar o contexto do banco de dados

No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection). O contêiner fornece o serviço aos controladores.

Atualize *Startup.cs* com o seguinte código realçado:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

O código anterior:

* Remove as declarações `using` não utilizadas.
* Adiciona o contexto de banco de dados ao contêiner de DI.
* Especifica que o contexto de banco de dados usará um banco de dados em memória.

## <a name="scaffold-a-controller"></a>Faça scaffold de um controlador

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Clique com o botão direito do mouse na pasta *Controllers*.
* Selecione **Adicionar** > **Novo item com scaffold**.
* Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.
* Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:

  * Selecione **TodoItem (TodoApi.Models)** na **classe Modelo**.
  * Selecione **TodoContext (TodoApi.Models)** na **classe de contexto Data**.
  * Selecione **Adicionar**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Execute os seguintes comandos:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Os comandos anteriores:

* Adicionam os pacotes do NuGet para scaffolding.
* Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).
* Fazem scaffold de `TodoItemsController`.

---

O código gerado:

* Marca a classe [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) com o atributo. Esse atributo indica se o controlador responde às solicitações da API Web. Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.
* Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador. O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.

Os modelos ASP.NET Core para:

* Controladores com `[action]` visualizações incluem no modelo de rota.
* Os controladores de API não incluem `[action]` no modelo de rota.

Quando `[action]` o token não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota. Ou seja, o nome do método associado da ação não é usado na rota correspondente.

## <a name="examine-the-posttodoitem-create-method"></a>Examine o método criar do PostTodoItem

Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

O código anterior é um método HTTP [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) POST, conforme indicado pelo atributo. O método obtém o valor do item pendente no corpo da solicitação HTTP.

O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:

* retorna um código de status HTTP 201 em caso de êxito. HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.
* Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta. O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado. Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho. A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.

### <a name="install-postman"></a>Instale o Postman

Este tutorial usa o Postman para testar a API Web.

* Instalar [carteiro](https://www.getpostman.com/downloads/)
* Inicie o aplicativo Web.
* Inicie o Postman.
* Desabilite a **Verificação do certificado SSL**
  * Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.
    > [!WARNING]
    > Habilite novamente a verificação do certificado SSL depois de testar o controlador.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Teste o PostTodoItem com o Postman

* Crie uma solicitação.
* Defina o método HTTP como `POST`.
* Selecione a guia **Corpo**.
* Selecione o botão de opção **bruto**.
* Defina o tipo como **JSON (aplicativo/json)**.
* No corpo da solicitação, insira JSON para um item pendente:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Selecione **Enviar**.

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Testar o URI do cabeçalho de local

* Selecione a guia **Cabeçalhos** no painel **Resposta**.
* Copie o valor do cabeçalho **Local**:

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* Defina o método como GET.
* Cole o URI (por `https://localhost:5001/api/TodoItems/1`exemplo, ).
* Selecione **Enviar**.

## <a name="examine-the-get-methods"></a>Examine os métodos GET

Esses métodos implementam dois pontos de extremidade GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman. Por exemplo:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Teste o GET com o Postman

* Crie uma solicitação.
* Defina o método HTTP como **GET**.
* Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`. Por exemplo, `https://localhost:5001/api/TodoItems`.
* Defina **Exibição de dois painéis** no Postman.
* Selecione **Enviar**.

Este aplicativo usa um banco de dados em memória. Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado. Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.

## <a name="routing-and-url-paths"></a>Roteamento e caminhos de URL

O [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atributo denota um método que responde a uma solicitação HTTP GET. O caminho da URL de cada método é construído da seguinte maneira:

* Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção. Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems". O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.
* Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho. Esta amostra não usa um modelo. Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente. Quando `GetTodoItem` é invocado, `"{id}"` o valor da URL é fornecido `id` ao método em seu parâmetro.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valores retornados

O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta. O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento. As exceções sem tratamento são convertidas em erros 5xx.

Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP. Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:

* Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).
* Caso contrário, o método retornará 200 com um corpo de resposta JSON. Retornar `item` resulta em uma resposta HTTP 200.

## <a name="the-puttodoitem-method"></a>O método PutTodoItem

Examine o método `PutTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT. A resposta é [204 (Sem Conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações. Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.

### <a name="test-the-puttodoitem-method"></a>Testar o método PutTodoItem

Esta amostra usa um banco de dados na memória que deve ser inicializado cada vez que o aplicativo é iniciado. Deverá haver um item no banco de dados antes de você fazer uma chamada PUT. Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.

Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

A seguinte imagem mostra a atualização do Postman:

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>O método DeleteTodoItem

Examine o método `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Testar o método DeleteTodoItem

Use o Postman para excluir um item pendente:

* Defina o método como `DELETE`.
* Defina o URI do objeto `https://localhost:5001/api/TodoItems/1`para excluir (por exemplo ).
* Selecione **Enviar**.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Evitar o excesso de postagem

Atualmente, o aplicativo de `TodoItem` amostra expõe todo o objeto. Os aplicativos de produções normalmente limitam os dados que são inseridos e devolvidos usando um subconjunto do modelo. Há várias razões por trás disso e a segurança é uma das principais. O subconjunto de um modelo é geralmente referido como um Objeto de Transferência de Dados (DTO), modelo de entrada ou modelo de exibição. **DTO** é usado neste artigo.

Um DTO pode ser usado para:

* Evite o excesso de postagem.
* Ocultar propriedades que os clientes não devem visualizar.
* Omita algumas propriedades para reduzir o tamanho da carga.
* Achatar gráficos de objetos que contenham objetos aninhados. Gráficos de objetos achatados podem ser mais convenientes para os clientes.

Para demonstrar a abordagem `TodoItem` DTO, atualize a classe para incluir um campo secreto:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

O campo secreto precisa ser escondido a partir deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.

Verifique se você pode postar e obter o campo secreto.

Criar um modelo DTO:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Atualize `TodoItemsController` o `TodoItemDTO`a ser usado:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Verifique se você não pode postar ou obter o campo secreto.

## <a name="call-the-web-api-with-javascript"></a>Chamar a API Web com o JavaScript

Veja [tutorial: Chame uma API web ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de API Web.
> * Adicione uma classe de modelo e um contexto de banco de dados.
> * Adicionar um controlador.
> * Adicionar métodos CRUD.
> * Configurar o roteamento e caminhos de URL.
> * Especificar os valores retornados.
> * Chamar a API Web com o Postman.
> * Chamar a API Web com o JavaScript.

No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.

## <a name="overview"></a>Visão geral

Este tutorial cria a seguinte API:

|API | Descrição | Corpo da solicitação | Corpo da resposta |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Obter todos os itens de tarefas pendentes | Nenhum | Matriz de itens de tarefas pendentes|
|GET /api/TodoItems/{id} | Obter um item por ID | Nenhum | Item de tarefas pendentes|
|POST /api/TodoItems | Adicionar um novo item | Item de tarefas pendentes | Item de tarefas pendentes |
|PUT /api/TodoItems/{id} | Atualizar um item &nbsp; existente | Item de tarefas pendentes | Nenhum |
|EXCLUSão /api/TodoItems/{id} &nbsp;&nbsp; | Exclua um &nbsp; item&nbsp; | Nenhum | Nenhum|

O diagrama a seguir mostra o design do aplicativo.

![O cliente é representado por uma caixa à esquerda. Ele envia uma solicitação e recebe uma resposta do aplicativo, uma caixa desenhada à direita. Dentro da caixa do aplicativo, três caixas representam o controlador, o modelo e a camada de acesso a dados. A solicitação é recebida no controlador do aplicativo e as operações de leitura/gravação ocorrem entre o controlador e a camada de acesso a dados. O modelo é serializado e retornado para o cliente na resposta.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Criar um projeto Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo**, selecione **Novo** > **Projeto**.
* Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.
* Nomeie o projeto como *TodoApi* e clique em **Criar**.
* Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados. Selecione o modelo **API** e clique em **Criar**. **Não** selecione **Habilitar Suporte ao Docker**.

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.
* Execute os seguintes comandos:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.

* Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão **.NET Core 2.2*.

* Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testar a API

O modelo de projeto cria uma API `values`. Chame o método `Get` em um navegador para testar o aplicativo.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pressione CTRL+F5 para executar o aplicativo. O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.

Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**. Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pressione CTRL+F5 para executar o aplicativo. Em um navegador, acesse a seguinte URL: `https://localhost:5001/api/values`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Selecione **Executar** > **iniciar depuração** para iniciar o aplicativo. O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente. Um erro HTTP 404 (Não Encontrado) será retornado. Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).

---

O seguinte JSON é retornado:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Adicionar uma classe de modelo

Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo. O modelo para esse aplicativo é uma única classe `TodoItem`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto. Selecione **Adicionar** > **nova pasta**. Nomeie a pasta como *Modelos*.

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**. Dê à classe o nome *TodoItem* e selecione **Adicionar**.

* Substitua o código do modelo pelo seguinte código:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Adicione uma pasta denominada *Modelos*.

* Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse no projeto. Selecione **Adicionar** > **nova pasta**. Nomeie a pasta como *Modelos*.

  ![nova pasta](first-web-api-mac/_static/folder.png)

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.

* Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.

* Substitua o código do modelo pelo seguinte código:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.

As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.

## <a name="add-a-database-context"></a>Adicionar um contexto de banco de dados

O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados. Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**. Nomeie a classe como *TodoContext* e clique em **Adicionar**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Adicione uma classe denominada `TodoContext` à pasta *Modelos*.

---

* Substitua o código do modelo pelo seguinte código:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrar o contexto do banco de dados

No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection). O contêiner fornece o serviço aos controladores.

Atualize *Startup.cs* com o seguinte código realçado:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

O código anterior:

* Remove as declarações `using` não utilizadas.
* Adiciona o contexto de banco de dados ao contêiner de DI.
* Especifica que o contexto de banco de dados usará um banco de dados em memória.

## <a name="add-a-controller"></a>Adicionar um controlador

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Clique com o botão direito do mouse na pasta *Controllers*.
* Selecione **Adicionar** > **Novo Item**.
* Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.
* Dê à classe o nome *TodoController* e selecione **Adicionar**.

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Na pasta *Controllers*, crie uma classe chamada `TodoController`.

---

* Substitua o código do modelo pelo seguinte código:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

O código anterior:

* Define uma classe de controlador de API sem métodos.
* Marca a classe [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) com o atributo. Esse atributo indica se o controlador responde às solicitações da API Web. Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.
* Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador. O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.
* Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio. Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP. Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado. Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.

## <a name="add-get-methods"></a>Adicionar métodos Get

Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Esses métodos implementam dois pontos de extremidade GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Interrompa o aplicativo se ele ainda estiver em execução. Em seguida, execute-o novamente para incluir as alterações mais recentes.

Teste o aplicativo chamando os dois pontos de extremidade em um navegador. Por exemplo:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Roteamento e caminhos de URL

O [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atributo denota um método que responde a uma solicitação HTTP GET. O caminho da URL de cada método é construído da seguinte maneira:

* Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção. Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo". O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.
* Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho. Esta amostra não usa um modelo. Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente. Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valores retornados

O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta. O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento. As exceções sem tratamento são convertidas em erros 5xx.

Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP. Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:

* Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).
* Caso contrário, o método retornará 200 com um corpo de resposta JSON. Retornar `item` resulta em uma resposta HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Testar o método GetTodoItems

Este tutorial usa o Postman para testar a API Web.

* Instalar [carteiro](https://www.getpostman.com/downloads/).
* Inicie o aplicativo Web.
* Inicie o Postman.
* Desativar a **verificação do certificado SSL**.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* A partir de**Preferências** **de Carteiro** > **(guia Geral),** desabilite a **verificação do certificado SSL**. Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.

---
  
> [!WARNING]
> Habilite novamente a verificação do certificado SSL depois de testar o controlador.

* Crie uma solicitação.
  * Defina o método HTTP como **GET**.
  * Defina a URL de solicitação como `https://localhost:<port>/api/todo`. Por exemplo, `https://localhost:5001/api/todo`.
* Defina **Exibição de dois painéis** no Postman.
* Selecione **Enviar**.

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Adicionar um método Create

Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

O código anterior é um método HTTP [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) POST, conforme indicado pelo atributo. O método obtém o valor do item pendente no corpo da solicitação HTTP.

O método `CreatedAtAction`:

* retorna um código de status HTTP 201 em caso de êxito. HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.
* Adiciona um cabeçalho `Location` à resposta. O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado. Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho. A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Testar o método PostTodoItem

* Compile o projeto.
* No Postman, defina o método HTTP como `POST`.
* Selecione a guia **Corpo**.
* Selecione o botão de opção **bruto**.
* Defina o tipo como **JSON (aplicativo/json)**.
* No corpo da solicitação, insira JSON para um item pendente:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Selecione **Enviar**.

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.

### <a name="test-the-location-header-uri"></a>Testar o URI do cabeçalho de local

* Selecione a guia **Cabeçalhos** no painel **Resposta**.
* Copie o valor do cabeçalho **Local**:

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* Defina o método como GET.
* Cole o URI (por `https://localhost:5001/api/Todo/2`exemplo, ).
* Selecione **Enviar**.

## <a name="add-a-puttodoitem-method"></a>Adicionar um método PutTodoItem

Em seguida, adicione o método `PutTodoItem` a seguir:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT. A resposta é [204 (Sem Conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações. Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.

### <a name="test-the-puttodoitem-method"></a>Testar o método PutTodoItem

Esta amostra usa um banco de dados na memória que deve ser inicializado cada vez que o aplicativo é iniciado. Deverá haver um item no banco de dados antes de você fazer uma chamada PUT. Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.

Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

A seguinte imagem mostra a atualização do Postman:

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Adicionar um método DeleteTodoItem

Em seguida, adicione o método `DeleteTodoItem` a seguir:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Testar o método DeleteTodoItem

Use o Postman para excluir um item pendente:

* Defina o método como `DELETE`.
* Defina o URI do objeto para `https://localhost:5001/api/todo/1`excluir (por exemplo, ).
* Selecione **Enviar**.

O aplicativo de exemplo permite que você exclua todos os itens. No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.

## <a name="call-the-web-api-with-javascript"></a>Chamar a API Web com o JavaScript

Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web. jQuery inicia a solicitação. O JavaScript atualiza a página com os detalhes da resposta da API Web.

Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Crie uma pasta *wwwroot* no diretório do projeto.

Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*. Substitua seu conteúdo pela seguinte marcação:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*. Substitua o conteúdo pelo código a seguir:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:

* Abra *Properties\launchSettings.json*.
* Remova `launchUrl` a propriedade para forçar o aplicativo a abrir no *index.html*&mdash;o arquivo padrão do projeto.

Esta amostra chama todos os métodos CRUD da API Web. Veja a seguir explicações das chamadas à API.

### <a name="get-a-list-of-to-do-items"></a>Obter uma lista de itens pendentes

o jQuery envia uma solicitação HTTP GET para a API web, que retorna o JSON representando uma matriz de itens a fazer. A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida. No retorno de chamada, o DOM é atualizado com as informações do item pendente.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Adicionar um item pendente

jQuery envia uma solicitação HTTP POST com o item a fazer no órgão de solicitação. As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado. O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Atualizar um item pendente

A atualização de um item pendente é semelhante à adição de um. A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Excluir um item pendente

A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Adicionar suporte à autenticação a uma API web

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Recursos adicionais

[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Consulte [como baixar](xref:index#how-to-download-a-sample).

Para saber mais, consulte os recursos a seguir:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=TTkhEyGBfAk)
