---
title: Razor Páginas testes de unidade no ASP.NET Core
author: rick-anderson
description: Saiba como criar testes de unidade para Razor aplicativos de páginas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: bb376412daafd49b307c3c13ea7c88f34628f1c3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634833"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a>Razor Páginas testes de unidade no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core dá suporte a testes de unidade de Razor aplicativos de páginas. Os testes da DAL (camada de acesso a dados) e dos modelos de página ajudam a garantir:

* Partes de um Razor aplicativo de páginas funcionam de forma independente e juntas como uma unidade durante a construção do aplicativo.
* Classes e métodos têm escopos de responsabilidade limitados.
* Há documentação adicional sobre como o aplicativo deve se comportar.
* Regressões, que são erros trazidos por atualizações para o código, são encontradas durante a criação e implantação automatizadas.

Este tópico pressupõe que você tenha uma compreensão básica de Razor aplicativos de páginas e testes de unidade. Se você não estiver familiarizado com Razor as páginas aplicativos ou conceitos de teste, consulte os seguintes tópicos:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [C# de teste de unidade no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O projeto de exemplo é composto por dois aplicativos:

| Aplicativo         | Pasta do projeto                     | Descrição |
| ----------- | ---------------------------------- | ----------- |
| Aplicativo de mensagens | *src/ Razor PagesTestSample*         | Permite que um usuário adicione uma mensagem, exclua uma mensagem, exclua todas as mensagens e analise as mensagens (Localize o número médio de palavras por mensagem). |
| Aplicativo de teste    | *testes/ Razor PagesTestSample. Tests* | Usado para testar a unidade do modelo de página DAL e índice do aplicativo de mensagem. |

Os testes podem ser executados usando os recursos de teste internos de um IDE, como o [Visual Studio](/visualstudio/test/unit-test-your-code) ou o [Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Se estiver usando [Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando na pasta *tests/ Razor PagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organização do aplicativo de mensagens

O aplicativo de mensagens é um Razor sistema de mensagens de páginas com as seguintes características:

* A página de índice do aplicativo (*pages/index. cshtml* e *pages/index. cshtml. cs*) fornece uma interface do usuário e métodos de modelo de página para controlar a adição, a exclusão e a análise de mensagens (localizar o número médio de palavras por mensagem).
* Uma mensagem é descrita pela `Message` classe (*Data/mensagem. cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). A `Text` propriedade é necessária e limitada a 200 caracteres.
* As mensagens são armazenadas usando&#8224; [de banco de dados na memória Entity Framework](/ef/core/providers/in-memory/) .
* O aplicativo contém uma DAL em sua classe de contexto de banco de `AppDbContext` dados, (*Data/AppDbContext. cs*). Os métodos DAL são marcados `virtual` , o que permite simular os métodos para uso nos testes.
* Se o banco de dados estiver vazio na inicialização do aplicativo, o repositório de mensagens será inicializado com três mensagens. Essas *mensagens propagadas* também são usadas em testes.

&#8224;tópico do EF, [teste com inmemory](/ef/core/miscellaneous/testing/in-memory), explica como usar um banco de dados na memória para testes com MSTest. Este tópico usa a estrutura de teste do [xUnit](https://xunit.github.io/) . Conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.

Embora o aplicativo de exemplo não use o padrão de repositório e não seja um exemplo eficaz do [padrão de rede de trabalho (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor as páginas dão suporte a esses padrões de desenvolvimento. Para obter mais informações, consulte [projetando a camada de persistência de infraestrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e <xref:mvc/controllers/testing> (o exemplo implementa o padrão de repositório).

## <a name="test-app-organization"></a>Organização de aplicativos de teste

O aplicativo de teste é um aplicativo de console dentro da pasta *tests/ Razor PagesTestSample. Tests* .

| Pasta do aplicativo de teste | Descrição |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contém os testes de unidade para a Dal.</li><li>*IndexPageTests.cs* contém os testes de unidade para o modelo de página de índice.</li></ul> |
| *Utilitários*     | Contém o `TestDbContextOptions` método usado para criar novas opções de contexto de banco de dados para cada teste de unidade da Dal para que o banco de dados seja redefinido para sua condição de linha de base para cada teste. |

A estrutura de teste é [xUnit](https://xunit.github.io/). A estrutura de simulação de objeto é [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testes de unidade da camada de acesso a dados (DAL)

O aplicativo de mensagem tem uma DAL com quatro métodos contidos na `AppDbContext` classe (*src/ Razor PagesTestSample/data/AppDbContext. cs*). Cada método tem um ou dois testes de unidade no aplicativo de teste.

| Método DAL               | Função                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtém um `List<Message>` do banco de dados classificado pela `Text` propriedade. |
| `AddMessageAsync`        | Adiciona um `Message` ao banco de dados.                                          |
| `DeleteAllMessagesAsync` | Exclui todas as `Message` entradas do banco de dados.                           |
| `DeleteMessageAsync`     | Exclui um único `Message` do banco de dados pelo `Id` .                      |

Testes de unidade da DAL exigem <xref:Microsoft.EntityFrameworkCore.DbContextOptions> ao criar um novo `AppDbContext` para cada teste. Uma abordagem para criar o `DbContextOptions` para cada teste é usar um <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

O problema dessa abordagem é que cada teste recebe o banco de dados em qualquer estado em que o teste anterior o deixou. Isso pode ser problemático ao tentar escrever testes de unidade atômicas que não interferem uns com os outros. Para forçar o `AppDbContext` a usar um novo contexto de banco de dados para cada teste, forneça uma `DbContextOptions` instância baseada em um novo provedor de serviços. O aplicativo de teste mostra como fazer isso usando seu `Utilities` método de classe `TestDbContextOptions` (*tests/ Razor PagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Usar o `DbContextOptions` nos testes de unidade Dal permite que cada teste seja executado atomicamente com uma nova instância de banco de dados:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Cada método de teste na `DataAccessLayerTest` classe (*UnitTests/DataAccessLayerTest. cs*) segue um padrão de Arrange-Act-Assert semelhante:

1. Organizar: o banco de dados está configurado para o teste e/ou o resultado esperado é definido.
1. Act: o teste é executado.
1. Assert: as asserções são feitas para determinar se o resultado do teste é um êxito.

Por exemplo, o `DeleteMessageAsync` método é responsável por remover uma única mensagem identificada por seu `Id` (*src/ Razor PagesTestSample/data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Há dois testes para esse método. Um teste verifica se o método exclui uma mensagem quando a mensagem está presente no banco de dados. O outro método testa se o banco de dados não é alterado se a mensagem `Id` para exclusão não existe. O `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` método é mostrado abaixo:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Primeiro, o método executa a etapa organizar, em que a preparação para a etapa Act ocorre. As mensagens de propagação são obtidas e mantidas em `seedMessages` . As mensagens de propagação são salvas no banco de dados. A mensagem com um `Id` de `1` é definida para exclusão. Quando o `DeleteMessageAsync` método é executado, as mensagens esperadas devem ter todas as mensagens, exceto aquela com um `Id` de `1` . A `expectedMessages` variável representa esse resultado esperado.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

O método age: o `DeleteMessageAsync` método é executado passando o `recId` de `1` :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Por fim, o método obtém o `Messages` do contexto e o compara com a `expectedMessages` declaração de que os dois são iguais:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Para comparar que os dois `List<Message>` são os mesmos:

* As mensagens são ordenadas por `Id` .
* Os pares de mensagens são comparados na `Text` propriedade.

Um método de teste semelhante, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` verifica o resultado da tentativa de excluir uma mensagem que não existe. Nesse caso, as mensagens esperadas no banco de dados devem ser iguais às mensagens reais depois que o `DeleteMessageAsync` método é executado. Não deve haver nenhuma alteração no conteúdo do banco de dados:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testes de unidade dos métodos de modelo de página

Outro conjunto de testes de unidade é responsável por testes de métodos de modelo de página. No aplicativo de mensagens, os modelos de página de índice são encontrados na `IndexModel` classe em *src/ Razor PagesTestSample/pages/index. cshtml. cs*.

| Método de modelo de página | Função |
| ----------------- | -------- |
| `OnGetAsync` | Obtém as mensagens da DAL para a interface do usuário usando o `GetMessagesAsync` método. |
| `OnPostAddMessageAsync` | Se [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) for válido, o chamará `AddMessageAsync` para adicionar uma mensagem ao banco de dados. |
| `OnPostDeleteAllMessagesAsync` | Chama `DeleteAllMessagesAsync` para excluir todas as mensagens no banco de dados. |
| `OnPostDeleteMessageAsync` | Executa `DeleteMessageAsync` para excluir uma mensagem com o `Id` especificado. |
| `OnPostAnalyzeMessagesAsync` | Se uma ou mais mensagens estiverem no banco de dados, o calculará o número médio de palavras por mensagem. |

Os métodos de modelo de página são testados usando sete testes na `IndexPageTests` classe (*tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Os testes usam o conhecido padrão Arrange-Assert-Act. Esses testes se concentram em:

* Determinando se os métodos seguem o comportamento correto quando [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) é inválido.
* A confirmação dos métodos gera o correto <xref:Microsoft.AspNetCore.Mvc.IActionResult> .
* Verificando se as atribuições de valor de propriedade são feitas corretamente.

Esse grupo de testes muitas vezes simula os métodos da DAL para produzir dados esperados para a etapa de ação em que um método de modelo de página é executado. Por exemplo, o `GetMessagesAsync` método de `AppDbContext` é simulado para produzir saída. Quando um método de modelo de página executa esse método, a simulação retorna o resultado. Os dados não são provenientes do Database. Isso cria condições de teste previsíveis e confiáveis para usar a DAL nos testes de modelo de página.

O `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` teste mostra como o `GetMessagesAsync` método é simulado para o modelo de página:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Quando o `OnGetAsync` método é executado na etapa Act, ele chama o método do modelo de página `GetMessagesAsync` .

Etapa de Act de teste de unidade (*tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` método do modelo de página `OnGetAsync` (*src/ Razor PagesTestSample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

O `GetMessagesAsync` método na Dal não retorna o resultado para essa chamada de método. A versão fictícia do método retorna o resultado.

Na `Assert` etapa, as mensagens reais ( `actualMessages` ) são atribuídas da `Messages` Propriedade do modelo de página. Uma verificação de tipo também é executada quando as mensagens são atribuídas. As mensagens esperadas e reais são comparadas por suas `Text` Propriedades. O teste declara que as duas `List<Message>` instâncias contêm as mesmas mensagens.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Outros testes nesse grupo criam objetos de modelo de página que incluem o <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> , o <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> , um <xref:Microsoft.AspNetCore.Mvc.ActionContext> para estabelecer o `PageContext` , um `ViewDataDictionary` e um `PageContext` . Eles são úteis na condução de testes. Por exemplo, o aplicativo de mensagem estabelece um `ModelState` erro com <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> para verificar se um válido <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> é retornado quando `OnPostAddMessageAsync` é executado:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Recursos adicionais

* [C# de teste de unidade no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Teste de unidade em seu código](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Introdução ao xUnit.net: usando o .NET Core com a linha de comando do SDK do .NET](https://xunit.github.io/docs/getting-started-dotnet-core)
* [MOQ](https://github.com/moq/moq4)
* [Início rápido do MOQ](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core dá suporte a testes de unidade de Razor aplicativos de páginas. Os testes da DAL (camada de acesso a dados) e dos modelos de página ajudam a garantir:

* Partes de um Razor aplicativo de páginas funcionam de forma independente e juntas como uma unidade durante a construção do aplicativo.
* Classes e métodos têm escopos de responsabilidade limitados.
* Há documentação adicional sobre como o aplicativo deve se comportar.
* Regressões, que são erros trazidos por atualizações para o código, são encontradas durante a criação e implantação automatizadas.

Este tópico pressupõe que você tenha uma compreensão básica de Razor aplicativos de páginas e testes de unidade. Se você não estiver familiarizado com Razor as páginas aplicativos ou conceitos de teste, consulte os seguintes tópicos:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [C# de teste de unidade no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O projeto de exemplo é composto por dois aplicativos:

| Aplicativo         | Pasta do projeto                     | Descrição |
| ----------- | ---------------------------------- | ----------- |
| Aplicativo de mensagens | *src/ Razor PagesTestSample*         | Permite que um usuário adicione uma mensagem, exclua uma mensagem, exclua todas as mensagens e analise as mensagens (Localize o número médio de palavras por mensagem). |
| Aplicativo de teste    | *testes/ Razor PagesTestSample. Tests* | Usado para testar a unidade do modelo de página DAL e índice do aplicativo de mensagem. |

Os testes podem ser executados usando os recursos de teste internos de um IDE, como o [Visual Studio](/visualstudio/test/unit-test-your-code) ou o [Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Se estiver usando [Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando na pasta *tests/ Razor PagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organização do aplicativo de mensagens

O aplicativo de mensagens é um Razor sistema de mensagens de páginas com as seguintes características:

* A página de índice do aplicativo (*pages/index. cshtml* e *pages/index. cshtml. cs*) fornece uma interface do usuário e métodos de modelo de página para controlar a adição, a exclusão e a análise de mensagens (localizar o número médio de palavras por mensagem).
* Uma mensagem é descrita pela `Message` classe (*Data/mensagem. cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). A `Text` propriedade é necessária e limitada a 200 caracteres.
* As mensagens são armazenadas usando&#8224; [de banco de dados na memória Entity Framework](/ef/core/providers/in-memory/) .
* O aplicativo contém uma DAL em sua classe de contexto de banco de `AppDbContext` dados, (*Data/AppDbContext. cs*). Os métodos DAL são marcados `virtual` , o que permite simular os métodos para uso nos testes.
* Se o banco de dados estiver vazio na inicialização do aplicativo, o repositório de mensagens será inicializado com três mensagens. Essas *mensagens propagadas* também são usadas em testes.

&#8224;tópico do EF, [teste com inmemory](/ef/core/miscellaneous/testing/in-memory), explica como usar um banco de dados na memória para testes com MSTest. Este tópico usa a estrutura de teste do [xUnit](https://xunit.github.io/) . Conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.

Embora o aplicativo de exemplo não use o padrão de repositório e não seja um exemplo eficaz do [padrão de rede de trabalho (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor as páginas dão suporte a esses padrões de desenvolvimento. Para obter mais informações, consulte [projetando a camada de persistência de infraestrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e <xref:mvc/controllers/testing> (o exemplo implementa o padrão de repositório).

## <a name="test-app-organization"></a>Organização de aplicativos de teste

O aplicativo de teste é um aplicativo de console dentro da pasta *tests/ Razor PagesTestSample. Tests* .

| Pasta do aplicativo de teste | Descrição |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contém os testes de unidade para a Dal.</li><li>*IndexPageTests.cs* contém os testes de unidade para o modelo de página de índice.</li></ul> |
| *Utilitários*     | Contém o `TestDbContextOptions` método usado para criar novas opções de contexto de banco de dados para cada teste de unidade da Dal para que o banco de dados seja redefinido para sua condição de linha de base para cada teste. |

A estrutura de teste é [xUnit](https://xunit.github.io/). A estrutura de simulação de objeto é [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testes de unidade da camada de acesso a dados (DAL)

O aplicativo de mensagem tem uma DAL com quatro métodos contidos na `AppDbContext` classe (*src/ Razor PagesTestSample/data/AppDbContext. cs*). Cada método tem um ou dois testes de unidade no aplicativo de teste.

| Método DAL               | Função                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtém um `List<Message>` do banco de dados classificado pela `Text` propriedade. |
| `AddMessageAsync`        | Adiciona um `Message` ao banco de dados.                                          |
| `DeleteAllMessagesAsync` | Exclui todas as `Message` entradas do banco de dados.                           |
| `DeleteMessageAsync`     | Exclui um único `Message` do banco de dados pelo `Id` .                      |

Testes de unidade da DAL exigem <xref:Microsoft.EntityFrameworkCore.DbContextOptions> ao criar um novo `AppDbContext` para cada teste. Uma abordagem para criar o `DbContextOptions` para cada teste é usar um <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

O problema dessa abordagem é que cada teste recebe o banco de dados em qualquer estado em que o teste anterior o deixou. Isso pode ser problemático ao tentar escrever testes de unidade atômicas que não interferem uns com os outros. Para forçar o `AppDbContext` a usar um novo contexto de banco de dados para cada teste, forneça uma `DbContextOptions` instância baseada em um novo provedor de serviços. O aplicativo de teste mostra como fazer isso usando seu `Utilities` método de classe `TestDbContextOptions` (*tests/ Razor PagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Usar o `DbContextOptions` nos testes de unidade Dal permite que cada teste seja executado atomicamente com uma nova instância de banco de dados:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Cada método de teste na `DataAccessLayerTest` classe (*UnitTests/DataAccessLayerTest. cs*) segue um padrão de Arrange-Act-Assert semelhante:

1. Organizar: o banco de dados está configurado para o teste e/ou o resultado esperado é definido.
1. Act: o teste é executado.
1. Assert: as asserções são feitas para determinar se o resultado do teste é um êxito.

Por exemplo, o `DeleteMessageAsync` método é responsável por remover uma única mensagem identificada por seu `Id` (*src/ Razor PagesTestSample/data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Há dois testes para esse método. Um teste verifica se o método exclui uma mensagem quando a mensagem está presente no banco de dados. O outro método testa se o banco de dados não é alterado se a mensagem `Id` para exclusão não existe. O `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` método é mostrado abaixo:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Primeiro, o método executa a etapa organizar, em que a preparação para a etapa Act ocorre. As mensagens de propagação são obtidas e mantidas em `seedMessages` . As mensagens de propagação são salvas no banco de dados. A mensagem com um `Id` de `1` é definida para exclusão. Quando o `DeleteMessageAsync` método é executado, as mensagens esperadas devem ter todas as mensagens, exceto aquela com um `Id` de `1` . A `expectedMessages` variável representa esse resultado esperado.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

O método age: o `DeleteMessageAsync` método é executado passando o `recId` de `1` :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Por fim, o método obtém o `Messages` do contexto e o compara com a `expectedMessages` declaração de que os dois são iguais:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Para comparar que os dois `List<Message>` são os mesmos:

* As mensagens são ordenadas por `Id` .
* Os pares de mensagens são comparados na `Text` propriedade.

Um método de teste semelhante, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` verifica o resultado da tentativa de excluir uma mensagem que não existe. Nesse caso, as mensagens esperadas no banco de dados devem ser iguais às mensagens reais depois que o `DeleteMessageAsync` método é executado. Não deve haver nenhuma alteração no conteúdo do banco de dados:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testes de unidade dos métodos de modelo de página

Outro conjunto de testes de unidade é responsável por testes de métodos de modelo de página. No aplicativo de mensagens, os modelos de página de índice são encontrados na `IndexModel` classe em *src/ Razor PagesTestSample/pages/index. cshtml. cs*.

| Método de modelo de página | Função |
| ----------------- | -------- |
| `OnGetAsync` | Obtém as mensagens da DAL para a interface do usuário usando o `GetMessagesAsync` método. |
| `OnPostAddMessageAsync` | Se [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) for válido, o chamará `AddMessageAsync` para adicionar uma mensagem ao banco de dados. |
| `OnPostDeleteAllMessagesAsync` | Chama `DeleteAllMessagesAsync` para excluir todas as mensagens no banco de dados. |
| `OnPostDeleteMessageAsync` | Executa `DeleteMessageAsync` para excluir uma mensagem com o `Id` especificado. |
| `OnPostAnalyzeMessagesAsync` | Se uma ou mais mensagens estiverem no banco de dados, o calculará o número médio de palavras por mensagem. |

Os métodos de modelo de página são testados usando sete testes na `IndexPageTests` classe (*tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Os testes usam o conhecido padrão Arrange-Assert-Act. Esses testes se concentram em:

* Determinando se os métodos seguem o comportamento correto quando [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) é inválido.
* A confirmação dos métodos gera o correto <xref:Microsoft.AspNetCore.Mvc.IActionResult> .
* Verificando se as atribuições de valor de propriedade são feitas corretamente.

Esse grupo de testes muitas vezes simula os métodos da DAL para produzir dados esperados para a etapa de ação em que um método de modelo de página é executado. Por exemplo, o `GetMessagesAsync` método de `AppDbContext` é simulado para produzir saída. Quando um método de modelo de página executa esse método, a simulação retorna o resultado. Os dados não são provenientes do Database. Isso cria condições de teste previsíveis e confiáveis para usar a DAL nos testes de modelo de página.

O `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` teste mostra como o `GetMessagesAsync` método é simulado para o modelo de página:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Quando o `OnGetAsync` método é executado na etapa Act, ele chama o método do modelo de página `GetMessagesAsync` .

Etapa de Act de teste de unidade (*tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` método do modelo de página `OnGetAsync` (*src/ Razor PagesTestSample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

O `GetMessagesAsync` método na Dal não retorna o resultado para essa chamada de método. A versão fictícia do método retorna o resultado.

Na `Assert` etapa, as mensagens reais ( `actualMessages` ) são atribuídas da `Messages` Propriedade do modelo de página. Uma verificação de tipo também é executada quando as mensagens são atribuídas. As mensagens esperadas e reais são comparadas por suas `Text` Propriedades. O teste declara que as duas `List<Message>` instâncias contêm as mesmas mensagens.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Outros testes nesse grupo criam objetos de modelo de página que incluem o <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> , o <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> , um <xref:Microsoft.AspNetCore.Mvc.ActionContext> para estabelecer o `PageContext` , um `ViewDataDictionary` e um `PageContext` . Eles são úteis na condução de testes. Por exemplo, o aplicativo de mensagem estabelece um `ModelState` erro com <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> para verificar se um válido <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> é retornado quando `OnPostAddMessageAsync` é executado:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Recursos adicionais

* [C# de teste de unidade no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Teste de unidade em seu código](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Introdução ao xUnit.net: usando o .NET Core com a linha de comando do SDK do .NET](https://xunit.github.io/docs/getting-started-dotnet-core)
* [MOQ](https://github.com/moq/moq4)
* [Início rápido do MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
* [JustMockLite](https://github.com/telerik/JustMockLite): uma estrutura fictícia para desenvolvedores .net. (*Não é mantido ou não tem suporte da Microsoft.*)

::: moniker-end
