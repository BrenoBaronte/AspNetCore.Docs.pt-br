---
title: Parte 4, adicionar um modelo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Parte 4 da série de tutoriais no ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: d99d5bfd84c8c7e7d0c479964211591be434330c
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855515"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="a1f54-103">Parte 4, adicionar um modelo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a1f54-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="a1f54-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a1f54-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="a1f54-105">Nesta seção, você adiciona classes para gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="a1f54-106">Essas classes serão a parte “**M** odel” parte do aplicativo **M** VC.</span><span class="sxs-lookup"><span data-stu-id="a1f54-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="a1f54-107">Você usa essas classes com o [Entity Framework Core](/ef/core) (EF Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="a1f54-108">O EF Core é uma estrutura ORM (de mapeamento relacional de objetos) que simplifica o código de acesso a dados que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="a1f54-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="a1f54-109">As classes de modelo que você cria são conhecidas como classes de dados POCO (de **o** bjetos **C** L **R\*\*\*\*b** ásicos) porque elas não têm nenhuma dependência no EF Core.</span><span class="sxs-lookup"><span data-stu-id="a1f54-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="a1f54-110">Elas apenas definem as propriedades dos dados que serão armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="a1f54-111">Neste tutorial, você escreve as classes de modelo primeiro e o EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="a1f54-112">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-114">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="a1f54-115">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a1f54-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1f54-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a1f54-117">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a1f54-119">Clique com o botão direito do mouse na pasta *modelos* > **Adicionar**  >  **nova** classe  >  **vazia classe**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="a1f54-120">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="a1f54-121">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a1f54-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="a1f54-122">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="a1f54-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="a1f54-123">O <xref:System.ComponentModel.DataAnnotations.DataType> atributo em `ReleaseDate` especifica o tipo de dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="a1f54-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="a1f54-124">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="a1f54-124">With this attribute:</span></span>

* <span data-ttu-id="a1f54-125">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="a1f54-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="a1f54-126">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="a1f54-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="a1f54-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="a1f54-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="a1f54-128">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="a1f54-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-130">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a1f54-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="a1f54-132">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a1f54-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="a1f54-133">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="a1f54-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="a1f54-134">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="a1f54-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="a1f54-135">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a1f54-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1f54-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a1f54-138">No menu **projeto** , selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="a1f54-139">No campo de **pesquisa** no canto superior direito, insira `Microsoft.EntityFrameworkCore.SQLite` e pressione a tecla de **retorno** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="a1f54-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="a1f54-140">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="a1f54-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar Entity Framework Core pacote NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="a1f54-142">A caixa de diálogo **selecionar projetos** será exibida, com o `MvcMovie` projeto selecionado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="a1f54-143">Pressione o botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="a1f54-143">Press the **Ok** button.</span></span>

<span data-ttu-id="a1f54-144">Uma caixa de diálogo de **aceitação de licença** será exibida.</span><span class="sxs-lookup"><span data-stu-id="a1f54-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="a1f54-145">Examine as licenças conforme desejado e clique no botão **aceitar** .</span><span class="sxs-lookup"><span data-stu-id="a1f54-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="a1f54-146">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="a1f54-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="a1f54-147">Execute o seguinte comando da CLI do .NET:</span><span class="sxs-lookup"><span data-stu-id="a1f54-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="a1f54-148">O comando anterior adiciona a [ferramenta ASPNET-CodeGenerator scaffolding](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="a1f54-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="a1f54-149">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-149">Create a database context class</span></span>

<span data-ttu-id="a1f54-150">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="a1f54-151">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="a1f54-152">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-152">Create a *Data* folder.</span></span>

<span data-ttu-id="a1f54-153">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a1f54-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="a1f54-154">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="a1f54-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="a1f54-155">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="a1f54-156">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="a1f54-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="a1f54-157">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-157">Register the database context</span></span>

<span data-ttu-id="a1f54-158">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a1f54-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a1f54-159">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1f54-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="a1f54-160">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="a1f54-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="a1f54-161">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="a1f54-162">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="a1f54-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="a1f54-163">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="a1f54-164">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a1f54-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-166">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="a1f54-167">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="a1f54-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="a1f54-168">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="a1f54-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="a1f54-169">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-169">Add a database connection string</span></span>

<span data-ttu-id="a1f54-170">Adicione uma cadeia de conexão ao *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="a1f54-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-172">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="a1f54-173">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="a1f54-174">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="a1f54-174">Scaffold movie pages</span></span>

<span data-ttu-id="a1f54-175">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="a1f54-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-177">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="a1f54-179">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="a1f54-181">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="a1f54-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="a1f54-182">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="a1f54-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="a1f54-183">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="a1f54-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc5.png)

* <span data-ttu-id="a1f54-185">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="a1f54-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="a1f54-186">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="a1f54-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="a1f54-187">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="a1f54-187">Select **Add**</span></span>

<span data-ttu-id="a1f54-188">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="a1f54-188">Visual Studio creates:</span></span>

* <span data-ttu-id="a1f54-189">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a1f54-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="a1f54-190">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas (*exibições/filmes/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="a1f54-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="a1f54-191">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="a1f54-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1f54-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="a1f54-193">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="a1f54-194">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="a1f54-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="a1f54-195">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="a1f54-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a1f54-197">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="a1f54-198">Exportar o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="a1f54-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="a1f54-199">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="a1f54-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="a1f54-200">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="a1f54-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="a1f54-201">Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .</span><span class="sxs-lookup"><span data-stu-id="a1f54-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="a1f54-202">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="a1f54-202">Initial migration</span></span>

<span data-ttu-id="a1f54-203">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="a1f54-204">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-206">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a1f54-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="a1f54-207">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a1f54-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="a1f54-208">`Add-Migration InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="a1f54-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="a1f54-209">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="a1f54-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a1f54-210">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="a1f54-211">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="a1f54-212">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="a1f54-213">`Update-Database`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="a1f54-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="a1f54-214">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="a1f54-215">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="a1f54-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="a1f54-216">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="a1f54-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="a1f54-217">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="a1f54-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="a1f54-218">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="a1f54-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="a1f54-219">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="a1f54-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-220">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="a1f54-221">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="a1f54-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="a1f54-222">`ef migrations add InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="a1f54-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="a1f54-223">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="a1f54-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a1f54-224">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="a1f54-225">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="a1f54-226">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="a1f54-227">`ef database update`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="a1f54-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="a1f54-228">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="a1f54-229">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="a1f54-229">The InitialCreate class</span></span>

<span data-ttu-id="a1f54-230">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="a1f54-231">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="a1f54-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="a1f54-232">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="a1f54-233">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a1f54-233">Test the app</span></span>

* <span data-ttu-id="a1f54-234">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="a1f54-235">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="a1f54-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-237">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="a1f54-238">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="a1f54-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="a1f54-239">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-239">Test the **Create** page.</span></span> <span data-ttu-id="a1f54-240">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="a1f54-241">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a1f54-242">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="a1f54-243">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="a1f54-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="a1f54-244">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="a1f54-245">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="a1f54-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-247">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="a1f54-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a1f54-248">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a1f54-249">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-250">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a1f54-251">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a1f54-252">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="a1f54-253">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="a1f54-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="a1f54-254">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a1f54-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="a1f54-255">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a1f54-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="a1f54-256">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="a1f54-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="a1f54-257">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="a1f54-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="a1f54-258">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="a1f54-259">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="a1f54-260">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="a1f54-261">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="a1f54-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="a1f54-262">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="a1f54-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="a1f54-263">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="a1f54-264">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="a1f54-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="a1f54-265">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="a1f54-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="a1f54-266">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="a1f54-267">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="a1f54-268">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="a1f54-269">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="a1f54-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="a1f54-270">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="a1f54-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="a1f54-271">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="a1f54-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="a1f54-272">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="a1f54-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="a1f54-273">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="a1f54-274">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="a1f54-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="a1f54-275">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="a1f54-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="a1f54-276">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="a1f54-277">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="a1f54-278">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="a1f54-279">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="a1f54-280">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="a1f54-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="a1f54-281">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="a1f54-282">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="a1f54-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="a1f54-283">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="a1f54-284">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="a1f54-285">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="a1f54-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="a1f54-286">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="a1f54-287">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="a1f54-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1f54-288">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a1f54-288">Additional resources</span></span>

* [<span data-ttu-id="a1f54-289">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="a1f54-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a1f54-290">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="a1f54-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="a1f54-291">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="a1f54-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="a1f54-292">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-294">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="a1f54-295">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a1f54-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1f54-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a1f54-297">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-298">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a1f54-299">Clique com o botão direito do mouse na pasta *modelos* > **Adicionar**  >  **nova** classe  >  **vazia classe**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="a1f54-300">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="a1f54-301">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a1f54-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="a1f54-302">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="a1f54-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="a1f54-303">O <xref:System.ComponentModel.DataAnnotations.DataType> atributo em `ReleaseDate` especifica o tipo de dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="a1f54-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="a1f54-304">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="a1f54-304">With this attribute:</span></span>

* <span data-ttu-id="a1f54-305">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="a1f54-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="a1f54-306">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="a1f54-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="a1f54-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="a1f54-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="a1f54-308">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="a1f54-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-310">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a1f54-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="a1f54-312">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a1f54-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="a1f54-313">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="a1f54-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="a1f54-314">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="a1f54-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="a1f54-315">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a1f54-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1f54-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-317">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a1f54-318">No menu **projeto** , selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="a1f54-319">No campo de **pesquisa** no canto superior direito, insira `Microsoft.EntityFrameworkCore.SQLite` e pressione a tecla de **retorno** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="a1f54-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="a1f54-320">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="a1f54-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar Entity Framework Core pacote NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="a1f54-322">A caixa de diálogo **selecionar projetos** será exibida, com o `MvcMovie` projeto selecionado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="a1f54-323">Pressione o botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="a1f54-323">Press the **Ok** button.</span></span>

<span data-ttu-id="a1f54-324">Uma caixa de diálogo de **aceitação de licença** será exibida.</span><span class="sxs-lookup"><span data-stu-id="a1f54-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="a1f54-325">Examine as licenças conforme desejado e clique no botão **aceitar** .</span><span class="sxs-lookup"><span data-stu-id="a1f54-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="a1f54-326">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="a1f54-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="a1f54-327">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-327">Create a database context class</span></span>

<span data-ttu-id="a1f54-328">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="a1f54-329">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="a1f54-330">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-330">Create a *Data* folder.</span></span>

<span data-ttu-id="a1f54-331">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a1f54-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="a1f54-332">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="a1f54-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="a1f54-333">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="a1f54-334">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="a1f54-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="a1f54-335">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-335">Register the database context</span></span>

<span data-ttu-id="a1f54-336">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a1f54-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a1f54-337">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1f54-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="a1f54-338">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="a1f54-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="a1f54-339">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="a1f54-340">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="a1f54-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="a1f54-341">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="a1f54-342">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a1f54-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-344">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="a1f54-345">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="a1f54-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="a1f54-346">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="a1f54-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="a1f54-347">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-347">Add a database connection string</span></span>

<span data-ttu-id="a1f54-348">Adicione uma cadeia de conexão ao *appsettings.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="a1f54-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-350">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="a1f54-351">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="a1f54-352">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="a1f54-352">Scaffold movie pages</span></span>

<span data-ttu-id="a1f54-353">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="a1f54-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-355">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="a1f54-357">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="a1f54-359">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="a1f54-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="a1f54-360">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="a1f54-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="a1f54-361">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="a1f54-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc3.png)

* <span data-ttu-id="a1f54-363">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="a1f54-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="a1f54-364">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="a1f54-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="a1f54-365">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="a1f54-365">Select **Add**</span></span>

<span data-ttu-id="a1f54-366">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="a1f54-366">Visual Studio creates:</span></span>

* <span data-ttu-id="a1f54-367">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a1f54-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="a1f54-368">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas (*exibições/filmes/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="a1f54-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="a1f54-369">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="a1f54-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1f54-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="a1f54-371">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="a1f54-372">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="a1f54-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="a1f54-373">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="a1f54-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-374">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a1f54-375">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="a1f54-376">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="a1f54-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="a1f54-377">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="a1f54-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="a1f54-378">Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .</span><span class="sxs-lookup"><span data-stu-id="a1f54-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="a1f54-379">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="a1f54-379">Initial migration</span></span>

<span data-ttu-id="a1f54-380">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="a1f54-381">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-383">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a1f54-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="a1f54-384">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a1f54-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="a1f54-385">`Add-Migration InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="a1f54-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="a1f54-386">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="a1f54-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a1f54-387">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="a1f54-388">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="a1f54-389">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="a1f54-390">`Update-Database`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="a1f54-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="a1f54-391">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="a1f54-392">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="a1f54-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="a1f54-393">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="a1f54-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="a1f54-394">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="a1f54-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="a1f54-395">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="a1f54-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="a1f54-396">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="a1f54-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-397">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="a1f54-398">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="a1f54-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="a1f54-399">`ef migrations add InitialCreate`: Gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="a1f54-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="a1f54-400">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="a1f54-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a1f54-401">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="a1f54-402">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="a1f54-403">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="a1f54-404">`ef database update`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="a1f54-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="a1f54-405">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="a1f54-406">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="a1f54-406">The InitialCreate class</span></span>

<span data-ttu-id="a1f54-407">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="a1f54-408">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="a1f54-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="a1f54-409">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="a1f54-410">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a1f54-410">Test the app</span></span>

* <span data-ttu-id="a1f54-411">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="a1f54-412">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="a1f54-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-414">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="a1f54-415">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="a1f54-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="a1f54-416">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-416">Test the **Create** page.</span></span> <span data-ttu-id="a1f54-417">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="a1f54-418">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a1f54-419">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="a1f54-420">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="a1f54-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="a1f54-421">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="a1f54-422">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="a1f54-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-424">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="a1f54-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a1f54-425">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a1f54-426">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-427">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a1f54-428">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a1f54-429">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="a1f54-430">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="a1f54-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="a1f54-431">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a1f54-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="a1f54-432">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a1f54-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="a1f54-433">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="a1f54-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="a1f54-434">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="a1f54-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="a1f54-435">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="a1f54-436">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="a1f54-437">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="a1f54-438">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="a1f54-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="a1f54-439">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="a1f54-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="a1f54-440">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="a1f54-441">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="a1f54-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="a1f54-442">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="a1f54-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="a1f54-443">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="a1f54-444">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="a1f54-445">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="a1f54-446">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="a1f54-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="a1f54-447">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="a1f54-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="a1f54-448">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="a1f54-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="a1f54-449">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="a1f54-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="a1f54-450">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="a1f54-451">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="a1f54-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="a1f54-452">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="a1f54-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="a1f54-453">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="a1f54-454">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="a1f54-455">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="a1f54-456">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="a1f54-457">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="a1f54-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="a1f54-458">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="a1f54-459">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="a1f54-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="a1f54-460">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="a1f54-461">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="a1f54-462">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="a1f54-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="a1f54-463">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="a1f54-464">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="a1f54-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1f54-465">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a1f54-465">Additional resources</span></span>

* [<span data-ttu-id="a1f54-466">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="a1f54-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a1f54-467">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="a1f54-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="a1f54-468">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="a1f54-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="a1f54-469">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="a1f54-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-471">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="a1f54-472">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-473">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a1f54-474">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="a1f54-475">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="a1f54-475">Scaffold the movie model</span></span>

<span data-ttu-id="a1f54-476">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="a1f54-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="a1f54-477">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="a1f54-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-479">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="a1f54-481">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="a1f54-483">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="a1f54-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="a1f54-484">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="a1f54-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="a1f54-485">**Classe de contexto de dados:** selecione o ícone **+** e adicione o **MvcMovie.Models.MvcMovieContext** padrão</span><span class="sxs-lookup"><span data-stu-id="a1f54-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adicionar contexto de dados](adding-model/_static/dc.png)

* <span data-ttu-id="a1f54-487">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="a1f54-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="a1f54-488">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="a1f54-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="a1f54-489">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="a1f54-489">Select **Add**</span></span>

![Caixa de diálogo Adicionar Controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="a1f54-491">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="a1f54-491">Visual Studio creates:</span></span>

* <span data-ttu-id="a1f54-492">Uma [classe de contexto de banco de dados](xref:data/ef-mvc/intro#create-the-database-context) do Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="a1f54-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="a1f54-493">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a1f54-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="a1f54-494">Razor exibir arquivos para criar, excluir, detalhes, editar e indexar páginas (*exibições/filmes/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="a1f54-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="a1f54-495">A criação automática do contexto de banco de dados e das exibições e métodos de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (criar, ler, atualizar e excluir) é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="a1f54-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a1f54-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1f54-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="a1f54-497">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="a1f54-498">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="a1f54-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="a1f54-499">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="a1f54-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="a1f54-500">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="a1f54-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-501">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a1f54-502">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="a1f54-503">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="a1f54-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="a1f54-504">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="a1f54-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="a1f54-505">Se você executar o aplicativo e clicar no link **Filme do MVC**, receberá um erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="a1f54-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-507">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="a1f54-508">Você precisa criar o banco de dados e usará o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="a1f54-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="a1f54-509">As Migrações permitem criar um banco de dados que corresponde ao seu modelo de dados e atualizar o esquema de banco de dados quando o modelo de dados é alterado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="a1f54-510">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="a1f54-510">Initial migration</span></span>

<span data-ttu-id="a1f54-511">Nesta seção, há estas tarefas:</span><span class="sxs-lookup"><span data-stu-id="a1f54-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="a1f54-512">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-512">Add an initial migration.</span></span>
* <span data-ttu-id="a1f54-513">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a1f54-515">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a1f54-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="a1f54-517">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a1f54-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="a1f54-518">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="a1f54-519">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="a1f54-520">O argumento `Initial` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="a1f54-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="a1f54-521">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="a1f54-522">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="a1f54-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="a1f54-523">O `Update-Database` comando executa o `Up` método no arquivo *Migrations/{time-Stamp} _InitialCreate. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-524">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="a1f54-525">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="a1f54-526">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="a1f54-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="a1f54-527">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="a1f54-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="a1f54-528">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="a1f54-529">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="a1f54-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="a1f54-530">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a1f54-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a1f54-531">Os serviços (como o contexto de BD do EF Core) são registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a1f54-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="a1f54-532">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="a1f54-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="a1f54-533">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a1f54-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1f54-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1f54-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1f54-535">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="a1f54-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="a1f54-536">Examine o seguinte método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a1f54-537">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="a1f54-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="a1f54-538">O `MvcMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="a1f54-539">O contexto de dados (`MvcMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="a1f54-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="a1f54-540">O contexto de dados especifica quais entidades são incluídas no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="a1f54-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="a1f54-541">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="a1f54-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="a1f54-542">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="a1f54-543">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="a1f54-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="a1f54-544">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="a1f54-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="a1f54-545">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="a1f54-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a1f54-546">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a1f54-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a1f54-547">Você criou um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="a1f54-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="a1f54-548">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a1f54-548">Test the app</span></span>

* <span data-ttu-id="a1f54-549">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="a1f54-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="a1f54-550">Se você receber uma exceção de banco de dados semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="a1f54-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="a1f54-551">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="a1f54-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="a1f54-552">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-552">Test the **Create** link.</span></span> <span data-ttu-id="a1f54-553">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="a1f54-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="a1f54-554">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a1f54-555">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="a1f54-556">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="a1f54-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="a1f54-557">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="a1f54-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="a1f54-558">Examine a classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="a1f54-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="a1f54-559">o código realçado acima mostra o contexto de banco de dados do filme que está sendo adicionado ao contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="a1f54-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="a1f54-560">`services.AddDbContext<MvcMovieContext>(options =>` especifica o banco de dados a ser usado e a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="a1f54-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="a1f54-561">`=>` é um [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="a1f54-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="a1f54-562">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="a1f54-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="a1f54-563">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="a1f54-564">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1f54-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="a1f54-565">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="a1f54-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="a1f54-566">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="a1f54-567">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="a1f54-568">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="a1f54-569">Essa abordagem fortemente tipada habilita uma melhor verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="a1f54-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="a1f54-570">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições quando ele criou os métodos e as exibições.</span><span class="sxs-lookup"><span data-stu-id="a1f54-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="a1f54-571">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="a1f54-572">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="a1f54-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="a1f54-573">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="a1f54-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="a1f54-574">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="a1f54-575">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="a1f54-576">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="a1f54-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="a1f54-577">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="a1f54-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="a1f54-578">O `id` parâmetro é definido como um [tipo anulável](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) no caso de um valor de ID não ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="a1f54-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="a1f54-579">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="a1f54-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="a1f54-580">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="a1f54-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="a1f54-581">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="a1f54-582">Incluindo uma instrução `@model` na parte superior do arquivo de exibição, você pode especificar o tipo de objeto esperado pela exibição.</span><span class="sxs-lookup"><span data-stu-id="a1f54-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="a1f54-583">Quando você criou o controlador de filmes, a seguinte instrução `@model` foi automaticamente incluída na parte superior do arquivo *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="a1f54-584">Esta diretiva `@model` permite acessar o filme que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="a1f54-585">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="a1f54-586">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="a1f54-587">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="a1f54-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="a1f54-588">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="a1f54-589">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="a1f54-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="a1f54-590">Quando você criou o controlador de filmes, o scaffolding incluiu automaticamente a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a1f54-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="a1f54-591">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="a1f54-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="a1f54-592">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="a1f54-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="a1f54-593">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a1f54-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="a1f54-594">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código:</span><span class="sxs-lookup"><span data-stu-id="a1f54-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1f54-595">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a1f54-595">Additional resources</span></span>

* [<span data-ttu-id="a1f54-596">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="a1f54-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a1f54-597">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="a1f54-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="a1f54-598">[Adição anterior de uma exibição](adding-view.md) 
>  [Em seguida, trabalhando com um banco de dados](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="a1f54-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
