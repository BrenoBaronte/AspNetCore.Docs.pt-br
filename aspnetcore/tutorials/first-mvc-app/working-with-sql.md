---
title: Parte 5, trabalhar com um banco de dados em um aplicativo MVC ASP.NET Core
author: rick-anderson
description: Parte 5, adicionar um modelo a um aplicativo ASP.NET Core MVC
ms.author: riande
ms.date: 11/10/2020
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
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: c082457258346637499b8ea8000dd8792931112f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94422646"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a>Parte 5, trabalhar com um banco de dados em um aplicativo MVC ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O objeto `MvcMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados. O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` no arquivo *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`. Para o desenvolvimento local, ele obtém a cadeia de conexão do *appsettings.json* arquivo:

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`. Para o desenvolvimento local, ele obtém a cadeia de conexão do *appsettings.json* arquivo:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um SQL Server de produção. Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

O LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express, que é direcionado para o desenvolvimento de programas. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o banco de dados LocalDB cria arquivos *.mdf* no diretório *C:/Users/{user}*.

* No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).

  ![Menu de exibição](working-with-sql/_static/ssox.png)

* Clique com o botão direito do mouse na tabela `Movie`**> Designer de Exibição**

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/design.png)

  ![Tabela Movie aberta no Designer](working-with-sql/_static/dv.png)

Observe o ícone de chave ao lado de `ID`. Por padrão, o EF tornará uma propriedade chamada `ID` a chave primária.

* Clique com o botão direito do mouse na tabela `Movie`**> Dados de Exibição**

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/ssox2.png)

  ![Tabela Movie aberta mostrando os dados da tabela](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Propagar o banco de dados

Crie uma nova classe chamada `SeedData` na pasta *Models*. Substitua o código gerado pelo seguinte:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Adicionar o inicializador de semeadura

Substitua o conteúdo do *Program.cs* pelo seguinte código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

Testar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Exclua todos os registros no BD. Faça isso com os links Excluir no navegador ou no SSOX.
* Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado. Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado. Faça isso com uma das seguintes abordagens:

  * Clique com botão direito do mouse no ícone de bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou **Parar Site**

    ![Ícone de bandeja do sistema do IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](working-with-sql/_static/stopIIS.png)

    * Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração
    * Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Exclua todos os registros no BD (para que o método de semeadura seja executado). Interrompa e inicie o aplicativo para propagar o banco de dados.

---

O aplicativo mostra os dados propagados.

![Aplicativo de filme MVC aberto no Microsoft Edge, mostrando os dados do filme](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [Anterior](adding-model.md) 
>  [Avançar](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O objeto `MvcMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados. O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` no arquivo *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`. Para o desenvolvimento local, ele obtém a cadeia de conexão do *appsettings.json* arquivo:

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`. Para o desenvolvimento local, ele obtém a cadeia de conexão do *appsettings.json* arquivo:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Quando você implanta o aplicativo em um servidor de teste ou de produção, você pode usar uma variável de ambiente ou outra abordagem para definir a cadeia de conexão como um SQL Server real. Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

O LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express, que é direcionado para o desenvolvimento de programas. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o banco de dados LocalDB cria arquivos *.mdf* no diretório *C:/Users/{user}*.

* No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).

  ![Menu de exibição](working-with-sql/_static/ssox.png)

* Clique com o botão direito do mouse na tabela `Movie`**> Designer de Exibição**

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/design.png)

  ![Tabela Movie aberta no Designer](working-with-sql/_static/dv.png)

Observe o ícone de chave ao lado de `ID`. Por padrão, o EF tornará uma propriedade chamada `ID` a chave primária.

* Clique com o botão direito do mouse na tabela `Movie`**> Dados de Exibição**

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/ssox2.png)

  ![Tabela Movie aberta mostrando os dados da tabela](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Propagar o banco de dados

Crie uma nova classe chamada `SeedData` na pasta *Models*. Substitua o código gerado pelo seguinte:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Adicionar o inicializador de semeadura

Substitua o conteúdo do *Program.cs* pelo seguinte código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

Testar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Exclua todos os registros no BD. Faça isso com os links Excluir no navegador ou no SSOX.
* Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado. Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado. Faça isso com uma das seguintes abordagens:

  * Clique com botão direito do mouse no ícone de bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou **Parar Site**

    ![Ícone de bandeja do sistema do IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](working-with-sql/_static/stopIIS.png)

    * Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração
    * Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Exclua todos os registros no BD (para que o método de semeadura seja executado). Interrompa e inicie o aplicativo para propagar o banco de dados.

---

O aplicativo mostra os dados propagados.

![Aplicativo de filme MVC aberto no Microsoft Edge, mostrando os dados do filme](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [Anterior](adding-model.md) 
>  [Avançar](controller-methods-views.md)

::: moniker-end
