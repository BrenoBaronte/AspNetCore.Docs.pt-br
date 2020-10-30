---
title: Criar uma API Web com o ASP.NET Core e o MongoDB
author: prkhandelwal
description: Este tutorial demonstra como criar uma API Web com o ASP.NET Core usando um banco de dados MongoDB NoSQL.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060736"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Criar uma API Web com o ASP.NET Core e o MongoDB

Por [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o MongoDB
> * Criar um banco de dados do MongoDB
> * Definir uma coleção e um esquema do MongoDB
> * Executar operações CRUD do MongoDB a partir de uma API Web
> * Personalizar a serialização JSON

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [SDK do .NET Core 3.0 ou posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [SDK do .NET Core 3.0 ou posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [SDK do .NET Core 3.0 ou posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio para Mac versão 7.7 ou posterior](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Configurar o MongoDB

Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão. Adicione *C: \\ arquivos de \\ programas \\ do \\ \<version_number> \\ servidor MongoDB* à `Path` variável de ambiente. Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.

Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos. Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Escolha um diretório no seu computador de desenvolvimento para armazenar os dados. Por exemplo, *C:\\BooksData* no Windows. Crie o diretório se não houver um. O Shell do mongo não cria novos diretórios.
1. Abra um shell de comando. Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017. Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Abra outra instância do shell de comando. Conecte-se ao banco de dados de testes padrão executando o seguinte comando:

   ```console
   mongo
   ```

1. Execute o seguinte em um shell de comando:

   ```console
   use BookstoreDb
   ```

   Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado. Se o banco de dados existir, a conexão dele será aberta para transações.

1. Crie uma coleção `Books` usando o seguinte comando:

   ```console
   db.createCollection('Books')
   ```

   O seguinte resultado é exibido:

   ```console
   { "ok" : 1 }
   ```

1. Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   O seguinte resultado é exibido:

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > As IDs mostradas neste artigo não corresponderão às IDs de quando você executar esse exemplo.

1. Visualize os documentos no banco de dados usando o seguinte comando:

   ```console
   db.Books.find({}).pretty()
   ```

   O seguinte resultado é exibido:

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.

O banco de dados está pronto. Você pode começar a criar a API Web do ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Criar o projeto da API Web do ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vá para **arquivo** > **novo** > **projeto** .
1. Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar** .
1. Nomeie o projeto como *BooksApi* e selecione **criar** .
1. Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 3.0** . Selecione o modelo de projeto **API** e, em seguida, **Criar** .
1. Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB. Na janela **Console do Gerenciador de Pacotes** , navegue até a raiz do projeto. Execute o seguinte comando para instalar o driver .NET para MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.

1. Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' BooksApi '. Adicioná-los?** . Selecione **Sim** na barra superior.
1. Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB. Abra **Terminal Integrado** e navegue até a raiz do projeto. Execute o seguinte comando para instalar o driver .NET para MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Em Visual Studio para Mac anteriores à versão 8,6, selecione **arquivo**  >  **nova solução**  >  **.NET Core**  >  **aplicativo** na barra lateral. Na versão 8,6 ou posterior, selecione **arquivo**  >  **nova solução**  >  **Web e**  >  **aplicativo** de console na barra lateral.
1. Selecione o modelo de projeto **ASP.NET Core** > **API** C# e selecione **Avançar** .
1. Selecione **.NET Core 3,1** na lista suspensa **estrutura de destino** e selecione **Avançar** .
1. Insira *BooksApi* para o **Nome do Projeto** e selecione **Criar** .
1. No painel **Solução** , clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes** .
1. Insira *MongoDB.Driver* na caixa de pesquisa, escolha o pacote *MongoDB.Driver* e selecione **Adicionar Pacote** .
1. Selecione o botão **Aceitar** na caixa de diálogo **Aceitação da Licença** .

---

## <a name="add-an-entity-model"></a>Adicionar um modelo de entidade

1. Adicione um diretório *Modelos* à raiz do projeto.
1. Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   Na classe anterior, a propriedade `Id`:

   * É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.
   * É anotada com [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para designar essa propriedade como a chave primária do documento.
   * É anotado com [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como tipo `string` em vez de uma estrutura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . O Mongo processa a conversão de `string` para `ObjectId`.

   A `BookName` propriedade é anotada com o [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributo. O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.

## <a name="add-a-configuration-model"></a>Adicionar um modelo de configuração

1. Adicione os seguintes valores de configuração de banco de dados a *appsettings.json* :

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   A `BookstoreDatabaseSettings` classe anterior é usada para armazenar os *appsettings.json* valores de `BookstoreDatabaseSettings` Propriedade do arquivo. Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.

1. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   No código anterior:

   * A instância de configuração à qual a *appsettings.json* seção do arquivo `BookstoreDatabaseSettings` é associada é registrada no contêiner injeção de dependência (di). Por exemplo, a `BookstoreDatabaseSettings` propriedade de um objeto `ConnectionString` é populada com a `BookstoreDatabaseSettings:ConnectionString` propriedade em *appsettings.json* .
   * A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton. Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.

1. Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Adicionar um serviço de operações CRUD

1. Adicione um diretório *Serviços* à raiz do projeto.
1. Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor. Essa técnica fornece acesso aos *appsettings.json* valores de configuração que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model) .

1. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras. O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`. De acordo com as diretrizes oficiais de [reutilização do cliente mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` o deve ser registrado em di com um tempo de vida do serviço singleton.

1. Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lê a instância de servidor para executar operações de banco de dados. O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa o banco de dados Mongo para executar operações. Este tutorial usa o método [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica. Execute operações CRUD em relação à coleção depois que esse método for chamado. Na chamada de método `GetCollection<TDocument>(collection)`:

  * `collection` representa o nome da coleção.
  * `TDocument` representa o tipo de objeto CLR armazenado na coleção.

`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção. Neste tutorial, os seguintes métodos são invocados na coleção:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): exclui um único documento correspondente aos critérios de pesquisa fornecidos.
* [Localizar \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): insere o objeto fornecido como um novo documento na coleção.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): substitui o documento único que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.

## <a name="add-a-controller"></a>Adicionar um controlador

Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

O controlador da API Web anterior:

* Usa a classe `BookService` para executar operações CRUD.
* Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.
* Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor. `CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta. O cabeçalho `Location` especifica o URI do livro recém-criado.

## <a name="test-the-web-api"></a>Testar a API Web

1. Compile e execute o aplicativo.

1. Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador. A seguinte resposta JSON é exibida:

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. Navegue até `http://localhost:<port>/api/books/{id here}` para testar o método de ação `Get` sobrecarregado do controlador. A seguinte resposta JSON é exibida:

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Configurar opções de serialização JSON

Há dois detalhes alterar sobre as respostas JSON retornadas na seção [Testar a API Web](#test-the-web-api):

* O uso de maiúsculas e minúsculas padrão dos nomes da propriedade deve ser alterado para corresponder ao uso de maiúsculas e minúsculas Pascal dos nomes de propriedade do objeto CLR.
* A propriedade `bookName` deve ser retornada como `Name`.

Para cumprir os requisitos anteriores, faça as seguintes alterações:

1. O JSON.NET foi removido da estrutura compartilhada do ASP.NET. Adicione uma referência de pacote a [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .

1. Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddControllers`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR. Por exemplo, a propriedade `Author` da classe `Book` é serializada como `Author`.

1. Em *Models/book. cs* , anote a `BookName` propriedade com o seguinte [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributo:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.

1. Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api). Observe a diferença em nomes de propriedade JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o MongoDB
> * Criar um banco de dados do MongoDB
> * Definir uma coleção e um esquema do MongoDB
> * Executar operações CRUD do MongoDB a partir de uma API Web
> * Personalizar a serialização JSON

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [SDK do .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [SDK do .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [SDK do .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio para Mac versão 7.7 ou posterior](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Configurar o MongoDB

Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão. Adicione *C: \\ arquivos de \\ programas \\ do \\ \<version_number> \\ servidor MongoDB* à `Path` variável de ambiente. Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.

Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos. Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Escolha um diretório no seu computador de desenvolvimento para armazenar os dados. Por exemplo, *C:\\BooksData* no Windows. Crie o diretório se não houver um. O Shell do mongo não cria novos diretórios.
1. Abra um shell de comando. Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017. Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Abra outra instância do shell de comando. Conecte-se ao banco de dados de testes padrão executando o seguinte comando:

   ```console
   mongo
   ```

1. Execute o seguinte em um shell de comando:

   ```console
   use BookstoreDb
   ```

   Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado. Se o banco de dados existir, a conexão dele será aberta para transações.

1. Crie uma coleção `Books` usando o seguinte comando:

   ```console
   db.createCollection('Books')
   ```

   O seguinte resultado é exibido:

   ```console
   { "ok" : 1 }
   ```

1. Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   O seguinte resultado é exibido:

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > As IDs mostradas neste artigo não corresponderão às IDs de quando você executar esse exemplo.

1. Visualize os documentos no banco de dados usando o seguinte comando:

   ```console
   db.Books.find({}).pretty()
   ```

   O seguinte resultado é exibido:

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.

O banco de dados está pronto. Você pode começar a criar a API Web do ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Criar o projeto da API Web do ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vá para **arquivo** > **novo** > **projeto** .
1. Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar** .
1. Nomeie o projeto como *BooksApi* e selecione **criar** .
1. Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 2.2** . Selecione o modelo de projeto **API** e, em seguida, **Criar** .
1. Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB. Na janela **Console do Gerenciador de Pacotes** , navegue até a raiz do projeto. Execute o seguinte comando para instalar o driver .NET para MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.

1. Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' BooksApi '. Adicioná-los?** . Selecione **Sim** na barra superior.
1. Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB. Abra **Terminal Integrado** e navegue até a raiz do projeto. Execute o seguinte comando para instalar o driver .NET para MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Em Visual Studio para Mac anteriores à versão 8,6, selecione **arquivo**  >  **nova solução**  >  **.NET Core**  >  **aplicativo** na barra lateral. Na versão 8,6 ou posterior, selecione **arquivo**  >  **nova solução**  >  **Web e**  >  **aplicativo** de console na barra lateral.
1. Selecione o modelo de projeto C# da **API Web do ASP.NET Core** e, em seguida, **Avançar** .
1. Selecione **.NET Core 2.2** na lista suspensa **Estrutura de Destino** e, em seguida, **Avançar** .
1. Insira *BooksApi* para o **Nome do Projeto** e selecione **Criar** .
1. No painel **Solução** , clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes** .
1. Insira *MongoDB.Driver* na caixa de pesquisa, escolha o pacote *MongoDB.Driver* e selecione **Adicionar Pacote** .
1. Selecione o botão **Aceitar** na caixa de diálogo **Aceitação da Licença** .

---

## <a name="add-an-entity-model"></a>Adicionar um modelo de entidade

1. Adicione um diretório *Modelos* à raiz do projeto.
1. Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   Na classe anterior, a propriedade `Id`:

   * É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.
   * É anotada com [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para designar essa propriedade como a chave primária do documento.
   * É anotado com [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como tipo `string` em vez de uma estrutura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . O Mongo processa a conversão de `string` para `ObjectId`.

   A `BookName` propriedade é anotada com o [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributo. O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.

## <a name="add-a-configuration-model"></a>Adicionar um modelo de configuração

1. Adicione os seguintes valores de configuração de banco de dados a *appsettings.json* :

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   A `BookstoreDatabaseSettings` classe anterior é usada para armazenar os *appsettings.json* valores de `BookstoreDatabaseSettings` Propriedade do arquivo. Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.

1. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   No código anterior:

   * A instância de configuração à qual a *appsettings.json* seção do arquivo `BookstoreDatabaseSettings` é associada é registrada no contêiner injeção de dependência (di). Por exemplo, a `BookstoreDatabaseSettings` propriedade de um objeto `ConnectionString` é populada com a `BookstoreDatabaseSettings:ConnectionString` propriedade em *appsettings.json* .
   * A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton. Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.

1. Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Adicionar um serviço de operações CRUD

1. Adicione um diretório *Serviços* à raiz do projeto.
1. Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor. Essa técnica fornece acesso aos *appsettings.json* valores de configuração que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model) .

1. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras. O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`. De acordo com as diretrizes oficiais de [reutilização do cliente mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` o deve ser registrado em di com um tempo de vida do serviço singleton.

1. Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lê a instância de servidor para executar operações de banco de dados. O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa o banco de dados Mongo para executar operações. Este tutorial usa o método [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica. Execute operações CRUD em relação à coleção depois que esse método for chamado. Na chamada de método `GetCollection<TDocument>(collection)`:

  * `collection` representa o nome da coleção.
  * `TDocument` representa o tipo de objeto CLR armazenado na coleção.

`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção. Neste tutorial, os seguintes métodos são invocados na coleção:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): exclui um único documento correspondente aos critérios de pesquisa fornecidos.
* [Localizar \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): insere o objeto fornecido como um novo documento na coleção.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): substitui o documento único que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.

## <a name="add-a-controller"></a>Adicionar um controlador

Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

O controlador da API Web anterior:

* Usa a classe `BookService` para executar operações CRUD.
* Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.
* Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor. `CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta. O cabeçalho `Location` especifica o URI do livro recém-criado.

## <a name="test-the-web-api"></a>Testar a API Web

1. Compile e execute o aplicativo.

1. Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador. A seguinte resposta JSON é exibida:

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. Navegue até `http://localhost:<port>/api/books/{id here}` para testar o método de ação `Get` sobrecarregado do controlador. A seguinte resposta JSON é exibida:

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Configurar opções de serialização JSON

Há dois detalhes alterar sobre as respostas JSON retornadas na seção [Testar a API Web](#test-the-web-api):

* O uso de maiúsculas e minúsculas padrão dos nomes da propriedade deve ser alterado para corresponder ao uso de maiúsculas e minúsculas Pascal dos nomes de propriedade do objeto CLR.
* A propriedade `bookName` deve ser retornada como `Name`.

Para cumprir os requisitos anteriores, faça as seguintes alterações:

1. Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddMvc`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR. Por exemplo, a propriedade `Author` da classe `Book` é serializada como `Author`.

1. Em *Models/book. cs* , anote a `BookName` propriedade com o seguinte [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributo:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.

1. Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api). Observe a diferença em nomes de propriedade JSON.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Adicionar suporte de autenticação a uma API da Web

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:

* [Versão deste artigo no YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
