---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7f7c2a09b74e6007ee3ea9c038398bac54988186
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488865"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Nesta seção, são adicionadas aulas para o gerenciamento de filmes. As classes modelo do aplicativo usam [o Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados. O EF Core é um mapeador relacional de objetos (O/RM) que simplifica o acesso aos dados.

As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core. Elas definem as propriedades dos dados que são armazenados no banco de dados.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Adicionar um modelo de dados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**. Nomeie a pasta como *Modelos*.

Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **classe**. Dê à classe o nome **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Adicione uma pasta denominada *Modelos*.
* Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No Solution Pad, clique com o botão direito do mouse no projeto **RazorPagesMovie** e, em seguida, **selecione Adicionar** > **nova pasta...**. Nomeie a pasta *Modelos*.
* Clique com o botão direito do mouse na pasta *Modelos* e, em seguida, **selecione Adicionar** > **novo arquivo...**.
* Na caixa de diálogo **Novo Arquivo**:

  * Selecione **Geral** no painel esquerdo.
  * Selecione **Classe Vazia** no painel central.
  * Nomeie a classe **Movie** e selecione **Novo**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Crie o projeto para verificar se não há erros de compilação.

## <a name="scaffold-the-movie-model"></a>Fazer scaffold do modelo de filme

Nesta seção, é feito o scaffold do modelo de filme. Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Crie uma pasta *Pages/Movies*:

* Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.
* Dê à pasta o nome *Movies*

Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.

![Imagem das instruções anteriores.](model/_static/sca.png)

Na caixa de diálogo **Adicionar andaime,** selecione **Páginas de navalha usando o Entity Framework (CRUD)** > **Adicionar**.

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:

* Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.
* Na linha **Classe de contexto de dados**, selecione o sinal de adição (**+**) e altere o nome gerado de RazorPagesMovie.**Models**.RazorPagesMovieContext para RazorPagesMovie.**Data**.RazorPagesMovieContext. [Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória. Ela cria a classe de contexto do banco de dados com o namespace correto.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/3/arp.png)

O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).
* Instale a ferramenta de scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Para Windows**: Execute o seguinte comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **No macOS e Linux**, execute o comando a seguir:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Crie uma pasta *Pages/Movies*:

* Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.
* Dê à pasta o nome *Movies*

Clique com o botão direito do mouse na pasta *Páginas/Filmes* > **adicionar** > **novos andaimes...**.

![Imagem das instruções anteriores.](model/_static/scaMac.png)

Na caixa de diálogo **Novos andaimes, selecione** **Páginas de Navalha usando O Quadro de Entidades (CRUD)** > **Next**.

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:

* Na **classe Modelo,** queda, selecione ou digite **, Filme (RazorPagesMovie.Models)**.
* Na linha **de classe de contexto de dados,** digite o nome para a nova classe, RazorPagesMovie. **Dados**. RazorPagesMovieContext. [Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória. Ela cria a classe de contexto do banco de dados com o namespace correto.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/arpMac.png)

O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

### <a name="add-ef-tools"></a>Adicionar ferramentas EF

Execute o seguinte comando .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
```

O comando anterior adiciona as Ferramentas core do framework da entidade para o .NET Core CLI.

---

### <a name="files-created"></a>Arquivos criados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

O processo de scaffold cria e atualiza os arquivos a seguir:

* *Pages/Movies*: Create, Delete, Details, Edit e Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

O processo de scaffold cria e atualiza os arquivos a seguir:

* *Pages/Movies*: Create, Delete, Details, Edit e Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

O processo de scaffold cria os arquivos a seguir:

* *Pages/Movies*: Create, Delete, Details, Edit e Index.

Os arquivos criados são explicados na próxima seção.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migração inicial

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:

* Adicionar uma migração inicial.
* Atualize o banco de dados com a migração inicial.

No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

No PMC, insira os seguintes comandos:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Os comandos anteriores geram o seguinte aviso: "Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo entidade 'Filme'. Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”

Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.

O comando migrations gera código para criar o esquema inicial do banco de dados. O esquema é baseado no modelo `DbContext`especificado em . O argumento `InitialCreate` é usado para nomear as migrações. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.

O `update` comando `Up` executa o método em migrações que não foram aplicadas. Neste caso, `update` executa o `Up` método em *Migrações/\<carimbo de tempo>_InitialCreate.cs,* que cria o banco de dados.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.

A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.

Examine o método `Startup.ConfigureServices`. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`. O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). O contexto de dados especifica quais entidades são incluídas no modelo de dados.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Examine o método `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Examine o método `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testar o aplicativo

* Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).

Se você obtiver o erro:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Você perdeu a [etapa de migrações](#pmc).

* Teste o link **Criar**.

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Teste os links **Editar**, **Detalhes** e **Excluir**.

O tutorial a seguir explica os arquivos criados por scaffolding.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: Comece a](xref:tutorials/razor-pages/razor-pages-start)
> [seguir: Páginas de navalha andaimes](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

Nesta seção, são adicionadas aulas para o gerenciamento de filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)multiplataforma . Aplicativos criados a partir de um modelo ASP.NET Core usam um banco de dados SQLite. As classes de modelo do aplicativo são usadas com [o Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)para trabalhar com o banco de dados. O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.

As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core. Elas definem as propriedades dos dados que são armazenados no banco de dados.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Adicionar um modelo de dados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**. Nomeie a pasta como *Modelos*.

Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **classe**. Dê à classe o nome **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Adicione uma pasta denominada *Modelos*.
* Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**. Nomeie a pasta como *Modelos*.
* Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.
* Na caixa de diálogo **Novo Arquivo**:

  * Selecione **Geral** no painel esquerdo.
  * Selecione **Classe Vazia** no painel central.
  * Nomeie a classe **Movie** e selecione **Novo**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Crie o projeto para verificar se não há erros de compilação.

## <a name="scaffold-the-movie-model"></a>Fazer scaffold do modelo de filme

Nesta seção, é feito o scaffold do modelo de filme. Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Crie uma pasta *Pages/Movies*:

* Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.
* Dê à pasta o nome *Movies*

Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.

![Imagem das instruções anteriores.](model/_static/sca.png)

Na caixa de diálogo **Adicionar andaime,** selecione **Páginas de navalha usando o Entity Framework (CRUD)** > **Adicionar**.

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.
* Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/arp.png)

O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).

* **Para Windows**: Execute o seguinte comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **No macOS e Linux**, execute o comando a seguir:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Crie uma pasta *Pages/Movies*:

* Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.
* Dê à pasta o nome *Movies*

Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.

![Imagem das instruções anteriores.](model/_static/scaMac.png)

Na **caixa de diálogo Adicionar novos andaimes,** selecione **Páginas de navalha usando o Entity Framework (CRUD)** > **Adicionar**.

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:

* Na **classe Modelo,** queda, selecione ou digite **Filme**.
* Na linha **de classe de contexto de dados,** selecione o **RazorPagesMovieContext,** isso criará uma nova classe de contexto db com o namespace correto. Neste caso, será **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/arpMac.png)

O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

---

O processo de scaffold cria e atualiza os arquivos a seguir:

### <a name="files-created"></a>Arquivos criados

* *Pages/Movies*: Create, Delete, Details, Edit e Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Arquivo atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migração inicial

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:

* Adicionar uma migração inicial.
* Atualize o banco de dados com a migração inicial.

No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

No PMC, insira os seguintes comandos:

```powershell
Add-Migration Initial
Update-Database
```

O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial. O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*). O `InitialCreate` argumento é usado para nomear a migração. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado. Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.

O comando `Update-Database` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*. O método `Up` cria o banco de dados.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Os comandos anteriores geram o seguinte aviso: "*Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Movie'. Isso fará com que os valores sejam silenciosamente truncados se não se encaixarem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do servidor SQL que pode acomodar todos os valores usando 'HasColumnType()'.* Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.

A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.

Examine o método `Startup.ConfigureServices`. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`. O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). O contexto de dados especifica quais entidades são incluídas no modelo de dados.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Examine o método `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Examine o método `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testar o aplicativo

* Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).

Se você obtiver o erro:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Você perdeu a [etapa de migrações](#pmc).

* Teste o link **Criar**.

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Teste os links **Editar**, **Detalhes** e **Excluir**.

O tutorial a seguir explica os arquivos criados por scaffolding.

## <a name="additional-resources"></a>Recursos adicionais

> [!div class="step-by-step"]
> [Anterior: Comece a](xref:tutorials/razor-pages/razor-pages-start)
> [seguir: Páginas de navalha andaimes](xref:tutorials/razor-pages/page)

::: moniker-end
