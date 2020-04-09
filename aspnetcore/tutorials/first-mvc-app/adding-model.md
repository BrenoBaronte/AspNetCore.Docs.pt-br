---
title: Adicione um modelo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Adicione um modelo a um aplicativo ASP.NET Core simples.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: e7fc0496438734e13cfafcecf432da4a94737897
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434506"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="765b4-103">Adicione um modelo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="765b4-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="765b4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="765b4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="765b4-105">Nesta seção, você adiciona classes para gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="765b4-106">Essas classes serão a parte “**M**odel” parte do aplicativo **M**VC.</span><span class="sxs-lookup"><span data-stu-id="765b4-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="765b4-107">Você usa essas classes com o [Entity Framework Core](/ef/core) (EF Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="765b4-108">O EF Core é uma estrutura ORM (de mapeamento relacional de objetos) que simplifica o código de acesso a dados que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="765b4-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="765b4-109">As classes de modelo que você cria são conhecidas como classes de dados POCO (de **o**bjetos **C**L**R\*\*\*\*b**ásicos) porque elas não têm nenhuma dependência no EF Core.</span><span class="sxs-lookup"><span data-stu-id="765b4-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="765b4-110">Elas apenas definem as propriedades dos dados que serão armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="765b4-111">Neste tutorial, você escreve as classes de modelo primeiro e o EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="765b4-112">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="765b4-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="765b4-114">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="765b4-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="765b4-115">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="765b4-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="765b4-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="765b4-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="765b4-117">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="765b4-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="765b4-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="765b4-119">Clique com o botão direito do mouse na pasta *Modelos* > **adicionar** > **nova classe** > **vazia**.</span><span class="sxs-lookup"><span data-stu-id="765b4-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="765b4-120">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="765b4-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="765b4-121">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="765b4-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="765b4-122">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="765b4-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="765b4-123">O <xref:System.ComponentModel.DataAnnotations.DataType> atributo em `ReleaseDate` especifica o`Date`tipo dos dados ( ).</span><span class="sxs-lookup"><span data-stu-id="765b4-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="765b4-124">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="765b4-124">With this attribute:</span></span>

* <span data-ttu-id="765b4-125">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="765b4-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="765b4-126">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="765b4-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="765b4-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="765b4-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="765b4-128">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="765b4-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="765b4-130">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="765b4-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="765b4-132">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="765b4-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="765b4-133">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="765b4-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="765b4-134">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="765b4-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="765b4-135">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="765b4-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="765b4-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="765b4-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="765b4-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="765b4-138">No menu **Projeto,** **selecione Gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="765b4-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="765b4-139">No campo **Pesquisar** no canto `Microsoft.EntityFrameworkCore.SQLite` superior direito, digite e pressione a tecla **Retornar** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="765b4-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="765b4-140">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote.**</span><span class="sxs-lookup"><span data-stu-id="765b4-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar núcleo de núcleo de entidade nuget pacote](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="765b4-142">A caixa de diálogo **Projetos Selecionados** será exibida, com o `MvcMovie` projeto selecionado.</span><span class="sxs-lookup"><span data-stu-id="765b4-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="765b4-143">Pressione o botão **Ok.**</span><span class="sxs-lookup"><span data-stu-id="765b4-143">Press the **Ok** button.</span></span>

<span data-ttu-id="765b4-144">Uma caixa de diálogo **aceitação** de licença será exibida.</span><span class="sxs-lookup"><span data-stu-id="765b4-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="765b4-145">Revise as licenças conforme desejado e clique no botão **Aceitar.**</span><span class="sxs-lookup"><span data-stu-id="765b4-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="765b4-146">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="765b4-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="765b4-147">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="765b4-147">Create a database context class</span></span>

<span data-ttu-id="765b4-148">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="765b4-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="765b4-149">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="765b4-150">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="765b4-150">Create a *Data* folder.</span></span>

<span data-ttu-id="765b4-151">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="765b4-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="765b4-152">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="765b4-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="765b4-153">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="765b4-154">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="765b4-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="765b4-155">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="765b4-155">Register the database context</span></span>

<span data-ttu-id="765b4-156">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="765b4-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="765b4-157">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765b4-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="765b4-158">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="765b4-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="765b4-159">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="765b4-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="765b4-160">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="765b4-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="765b4-161">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="765b4-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="765b4-162">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="765b4-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-164">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="765b4-165">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="765b4-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="765b4-166">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="765b4-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="765b4-167">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="765b4-167">Add a database connection string</span></span>

<span data-ttu-id="765b4-168">Adicione uma seqüência de conexões ao arquivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="765b4-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-170">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="765b4-171">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="765b4-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="765b4-172">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="765b4-172">Scaffold movie pages</span></span>

<span data-ttu-id="765b4-173">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="765b4-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="765b4-175">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="765b4-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="765b4-177">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="765b4-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="765b4-179">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="765b4-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="765b4-180">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="765b4-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="765b4-181">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="765b4-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc3.png)

* <span data-ttu-id="765b4-183">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="765b4-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="765b4-184">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="765b4-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="765b4-185">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="765b4-185">Select **Add**</span></span>

<span data-ttu-id="765b4-186">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="765b4-186">Visual Studio creates:</span></span>

* <span data-ttu-id="765b4-187">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="765b4-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="765b4-188">Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="765b4-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="765b4-189">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="765b4-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="765b4-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="765b4-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="765b4-191">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="765b4-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="765b4-192">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="765b4-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="765b4-193">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="765b4-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="765b4-194">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="765b4-195">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="765b4-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="765b4-196">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="765b4-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="765b4-197">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="765b4-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="765b4-198">Se você executar o aplicativo e clicar no link **do Aplicativo de Filme,** você receberá um *banco de dados não pode abrir* ou não *tal tabela:* mensagem de erro de filme.</span><span class="sxs-lookup"><span data-stu-id="765b4-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="765b4-199">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="765b4-199">Initial migration</span></span>

<span data-ttu-id="765b4-200">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="765b4-201">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="765b4-203">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="765b4-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="765b4-204">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="765b4-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="765b4-205">`Add-Migration InitialCreate`: Gera um arquivo de migração *Migrations/{timestamp}_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="765b4-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="765b4-206">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="765b4-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="765b4-207">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="765b4-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="765b4-208">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="765b4-209">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="765b4-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="765b4-210">`Update-Database`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="765b4-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="765b4-211">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="765b4-212">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="765b4-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="765b4-213">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="765b4-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="765b4-214">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="765b4-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="765b4-215">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="765b4-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="765b4-216">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="765b4-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-217">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="765b4-218">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="765b4-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="765b4-219">`ef migrations add InitialCreate`: Gera um arquivo de migração *Migrations/{timestamp}_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="765b4-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="765b4-220">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="765b4-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="765b4-221">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="765b4-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="765b4-222">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="765b4-223">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="765b4-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="765b4-224">`ef database update`: Atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="765b4-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="765b4-225">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="765b4-226">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="765b4-226">The InitialCreate class</span></span>

<span data-ttu-id="765b4-227">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="765b4-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="765b4-228">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="765b4-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="765b4-229">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="765b4-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="765b4-230">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="765b4-230">Test the app</span></span>

* <span data-ttu-id="765b4-231">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="765b4-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="765b4-232">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="765b4-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-234">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="765b4-235">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="765b4-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="765b4-236">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="765b4-236">Test the **Create** page.</span></span> <span data-ttu-id="765b4-237">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="765b4-238">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="765b4-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="765b4-239">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="765b4-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="765b4-240">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="765b4-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="765b4-241">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="765b4-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="765b4-242">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="765b4-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="765b4-244">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="765b4-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="765b4-245">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="765b4-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="765b4-246">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="765b4-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-247">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="765b4-248">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="765b4-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="765b4-249">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="765b4-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="765b4-250">Use SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="765b4-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="765b4-251">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="765b4-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="765b4-252">O código a seguir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> mostra como injetar em Startup.</span><span class="sxs-lookup"><span data-stu-id="765b4-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="765b4-253">`IWebHostEnvironment`é injetado `ConfigureServices` para que possa usar SQLite em desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="765b4-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="765b4-254">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="765b4-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="765b4-255">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="765b4-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="765b4-256">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="765b4-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="765b4-257">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="765b4-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="765b4-258">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="765b4-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="765b4-259">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="765b4-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="765b4-260">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="765b4-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="765b4-261">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="765b4-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="765b4-262">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="765b4-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="765b4-263">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="765b4-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="765b4-264">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="765b4-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="765b4-265">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="765b4-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="765b4-266">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="765b4-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="765b4-267">O `id` parâmetro é definido como um`int?`tipo [anulado](/dotnet/csharp/programming-guide/nullable-types/index) ( ) caso um valor de ID não seja fornecido.</span><span class="sxs-lookup"><span data-stu-id="765b4-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="765b4-268">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="765b4-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="765b4-269">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="765b4-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="765b4-270">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="765b4-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="765b4-271">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="765b4-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="765b4-272">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="765b4-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="765b4-273">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="765b4-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="765b4-274">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="765b4-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="765b4-275">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="765b4-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="765b4-276">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="765b4-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="765b4-277">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="765b4-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="765b4-278">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="765b4-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="765b4-279">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="765b4-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="765b4-280">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="765b4-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="765b4-281">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="765b4-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="765b4-282">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="765b4-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="765b4-283">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="765b4-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="765b4-284">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="765b4-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="765b4-285">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="765b4-285">Additional resources</span></span>

* [<span data-ttu-id="765b4-286">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="765b4-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="765b4-287">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="765b4-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="765b4-288">[Adicionando anteriormente uma exibição](adding-view.md)
> [em seguida trabalhando com sql](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="765b4-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="765b4-289">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="765b4-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="765b4-291">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="765b4-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="765b4-292">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="765b4-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-293">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="765b4-294">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="765b4-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="765b4-295">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="765b4-295">Scaffold the movie model</span></span>

<span data-ttu-id="765b4-296">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="765b4-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="765b4-297">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="765b4-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="765b4-299">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta \*Controladores\***> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="765b4-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="765b4-301">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="765b4-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="765b4-303">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="765b4-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="765b4-304">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="765b4-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="765b4-305">**Classe de contexto de dados:** selecione o ícone **+** e adicione o **MvcMovie.Models.MvcMovieContext** padrão</span><span class="sxs-lookup"><span data-stu-id="765b4-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adicionar contexto de dados](adding-model/_static/dc.png)

* <span data-ttu-id="765b4-307">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="765b4-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="765b4-308">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="765b4-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="765b4-309">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="765b4-309">Select **Add**</span></span>

![Caixa de diálogo Adicionar Controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="765b4-311">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="765b4-311">Visual Studio creates:</span></span>

* <span data-ttu-id="765b4-312">Uma [classe de contexto de banco de dados](xref:data/ef-mvc/intro#create-the-database-context) do Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="765b4-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="765b4-313">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="765b4-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="765b4-314">Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="765b4-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="765b4-315">A criação automática do contexto de banco de dados e das exibições e métodos de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (criar, ler, atualizar e excluir) é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="765b4-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="765b4-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="765b4-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="765b4-317">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="765b4-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="765b4-318">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="765b4-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="765b4-319">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="765b4-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="765b4-320">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="765b4-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="765b4-321">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="765b4-322">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="765b4-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="765b4-323">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="765b4-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="765b4-324">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="765b4-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="765b4-325">Se você executar o aplicativo e clicar no link **Filme do MVC**, receberá um erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="765b4-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-327">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="765b4-328">Você precisa criar o banco de dados e usará o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="765b4-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="765b4-329">As Migrações permitem criar um banco de dados que corresponde ao seu modelo de dados e atualizar o esquema de banco de dados quando o modelo de dados é alterado.</span><span class="sxs-lookup"><span data-stu-id="765b4-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="765b4-330">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="765b4-330">Initial migration</span></span>

<span data-ttu-id="765b4-331">Nesta seção, há estas tarefas:</span><span class="sxs-lookup"><span data-stu-id="765b4-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="765b4-332">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="765b4-332">Add an initial migration.</span></span>
* <span data-ttu-id="765b4-333">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="765b4-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="765b4-335">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="765b4-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="765b4-337">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="765b4-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="765b4-338">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="765b4-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="765b4-339">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="765b4-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="765b4-340">O argumento `Initial` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="765b4-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="765b4-341">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="765b4-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="765b4-342">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="765b4-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="765b4-343">O `Update-Database` comando `Up` executa o método no arquivo *Migrations/{time-stamp}_InitialCreate.cs,* que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-344">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="765b4-345">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="765b4-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="765b4-346">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="765b4-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="765b4-347">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="765b4-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="765b4-348">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="765b4-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="765b4-349">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="765b4-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="765b4-350">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="765b4-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="765b4-351">Os serviços (como o contexto de BD do EF Core) são registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765b4-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="765b4-352">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="765b4-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="765b4-353">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="765b4-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="765b4-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="765b4-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="765b4-355">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="765b4-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="765b4-356">Examine o seguinte método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="765b4-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="765b4-357">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="765b4-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="765b4-358">O `MvcMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="765b4-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="765b4-359">O contexto de dados (`MvcMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="765b4-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="765b4-360">O contexto de dados especifica quais entidades são incluídas no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="765b4-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="765b4-361">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="765b4-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="765b4-362">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="765b4-363">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="765b4-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="765b4-364">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="765b4-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="765b4-365">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="765b4-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="765b4-366">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="765b4-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="765b4-367">Você criou um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="765b4-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="765b4-368">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="765b4-368">Test the app</span></span>

* <span data-ttu-id="765b4-369">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="765b4-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="765b4-370">Se você receber uma exceção de banco de dados semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="765b4-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="765b4-371">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="765b4-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="765b4-372">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="765b4-372">Test the **Create** link.</span></span> <span data-ttu-id="765b4-373">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="765b4-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="765b4-374">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="765b4-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="765b4-375">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="765b4-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="765b4-376">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="765b4-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="765b4-377">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="765b4-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="765b4-378">Examine a classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="765b4-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="765b4-379">o código realçado acima mostra o contexto de banco de dados do filme que está sendo adicionado ao contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="765b4-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="765b4-380">`services.AddDbContext<MvcMovieContext>(options =>` especifica o banco de dados a ser usado e a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="765b4-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="765b4-381">`=>` é um [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="765b4-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="765b4-382">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="765b4-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="765b4-383">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="765b4-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="765b4-384">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="765b4-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="765b4-385">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="765b4-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="765b4-386">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="765b4-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="765b4-387">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="765b4-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="765b4-388">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="765b4-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="765b4-389">Essa abordagem fortemente tipada habilita uma melhor verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="765b4-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="765b4-390">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições quando ele criou os métodos e as exibições.</span><span class="sxs-lookup"><span data-stu-id="765b4-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="765b4-391">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="765b4-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="765b4-392">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="765b4-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="765b4-393">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="765b4-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="765b4-394">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="765b4-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="765b4-395">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="765b4-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="765b4-396">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="765b4-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="765b4-397">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="765b4-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="765b4-398">O `id` parâmetro é definido como um`int?`tipo [anulado](/dotnet/csharp/programming-guide/nullable-types/index) ( ) caso um valor de ID não seja fornecido.</span><span class="sxs-lookup"><span data-stu-id="765b4-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="765b4-399">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="765b4-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="765b4-400">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="765b4-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="765b4-401">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="765b4-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="765b4-402">Incluindo uma instrução `@model` na parte superior do arquivo de exibição, você pode especificar o tipo de objeto esperado pela exibição.</span><span class="sxs-lookup"><span data-stu-id="765b4-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="765b4-403">Quando você criou o controlador de filmes, a seguinte instrução `@model` foi automaticamente incluída na parte superior do arquivo *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="765b4-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="765b4-404">Esta diretiva `@model` permite acessar o filme que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="765b4-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="765b4-405">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="765b4-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="765b4-406">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="765b4-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="765b4-407">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="765b4-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="765b4-408">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="765b4-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="765b4-409">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="765b4-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="765b4-410">Quando você criou o controlador de filmes, o scaffolding incluiu automaticamente a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="765b4-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="765b4-411">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="765b4-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="765b4-412">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="765b4-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="765b4-413">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="765b4-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="765b4-414">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código:</span><span class="sxs-lookup"><span data-stu-id="765b4-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="765b4-415">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="765b4-415">Additional resources</span></span>

* [<span data-ttu-id="765b4-416">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="765b4-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="765b4-417">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="765b4-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="765b4-418">[Adicionando anteriormente uma exibição](adding-view.md)
> [em seguida trabalhando com um banco de dados](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="765b4-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
