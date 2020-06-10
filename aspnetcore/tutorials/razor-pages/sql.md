---
title: Parte 4, com um banco de dados e ASP.NET Core
author: rick-anderson
description: Parte 4 da série de tutoriais em Razor páginas.
ms.author: riande
ms.date: 7/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 21ae2ed4e91a0b3e52b1cdad1f4f4686c50614ba
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652974"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a>Parte 4, com um banco de dados e ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados. O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`. Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appSettings. JSON* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

O valor do nome do banco de dados (`Database={Database name}`) será diferente para o seu código gerado. O valor do nome é arbitrário.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um servidor de banco de dados real. Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:\Users\<user>\`.

<a name="ssox"></a>
* No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).

  ![Menu de exibição](sql/_static/ssox.png)

* Clique com o botão direito do mouse na tabela `Movie` e selecione **Designer de exibição**:

  ![Menus contextuais abertos na tabela Movie](sql/_static/design.png)

  ![Tabelas Movie abertas no Designer](sql/_static/dv.png)

Observe o ícone de chave ao lado de `ID`. Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.

* Clique com o botão direito do mouse na tabela `Movie` e selecione **Exibir dados**:

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Propagar o banco de dados

Crie uma classe chamada `SeedData` na pasta *Models* com o seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Adicionar o inicializador de semeadura

Em *Program.cs*, modifique o método `Main` para fazer o seguinte:

* Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.
* Chame o método de semente passando a ele o contexto.
* Descarte o contexto quando o método de semente for concluído.

O código a seguir mostra o arquivo *Program.cs* atualizado.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

A seguinte exceção ocorre quando `Update-Database` não foi executada:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Exclua todos os registros no BD. Você pode fazer isso com os links de exclusão no navegador ou em [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado. Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado. Faça isso com uma das seguintes abordagens:

  * Clique com o botão direito do mouse no ícone da bandeja do sistema IIS Express na área de notificação e toque em **sair** ou **parar site**:

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração.
    * Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Exclua todos os registros no BD (para que o método de semeadura seja executado). Interrompa e inicie o aplicativo para propagar o banco de dados.

O aplicativo mostra os dados propagados.

---

O próximo tutorial melhorará a apresentação dos dados.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizando as páginas](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados. O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Para obter mais informações sobre os métodos usados em `ConfigureServices`, veja:

* [Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) da UE no ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`. Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appSettings. JSON* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

O valor do nome do banco de dados (`Database={Database name}`) será diferente para o seu código gerado. O valor do nome é arbitrário.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um servidor de banco de dados real. Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:/Users/<user/>`.

<a name="ssox"></a>
* No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).

  ![Menu de exibição](sql/_static/ssox.png)

* Clique com o botão direito do mouse na tabela `Movie` e selecione **Designer de exibição**:

  ![Menu contextual aberto na tabela Movie](sql/_static/design.png)

  ![Tabela Movie aberta no Designer](sql/_static/dv.png)

Observe o ícone de chave ao lado de `ID`. Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.

* Clique com o botão direito do mouse na tabela `Movie` e selecione **Exibir dados**:

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Propagar o banco de dados

Crie uma classe chamada `SeedData` na pasta *Models* com o seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Adicionar o inicializador de semeadura

Em *Program.cs*, modifique o método `Main` para fazer o seguinte:

* Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.
* Chame o método de semente passando a ele o contexto.
* Descarte o contexto quando o método de semente for concluído.

O código a seguir mostra o arquivo *Program.cs* atualizado.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Um aplicativo de produção não chamaria `Database.Migrate`. Ele é adicionado ao código anterior para evitar a exceção a seguir quando `Update-Database` não foi executado:

SqlException: não pode abrir o banco de dados "RazorPagesMovieContext-21" solicitado pelo logon. Falha no logon.
O logon falhou para o usuário 'user name'.

### <a name="test-the-app"></a>Testar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Exclua todos os registros no BD. Você pode fazer isso com os links de exclusão no navegador ou em [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado. Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado. Faça isso com uma das seguintes abordagens:

  * Clique com botão direito do mouse no ícone na bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou em **Parar site**:

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração.
    * Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exclua todos os registros no BD (para que o método de semeadura seja executado). Interrompa e inicie o aplicativo para propagar o banco de dados.

O aplicativo mostra os dados propagados.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Exclua todos os registros no BD (para que o método de semeadura seja executado). Interrompa e inicie o aplicativo para propagar o banco de dados.

O aplicativo mostra os dados propagados.

---

O aplicativo mostra os dados propagados:

![Aplicativo de filme aberto no Chrome mostrando os dados do filme](sql/_static/m55.png)

O próximo tutorial limpará a apresentação dos dados.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Anterior: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) 
>  [seguinte: atualizando as páginas](xref:tutorials/razor-pages/da1)

::: moniker-end
