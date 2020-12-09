::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="f606d-101">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="f606d-101">Create a *Data* folder.</span></span>

<span data-ttu-id="f606d-102">Adicione a seguinte classe `MvcMovieContext` à pasta *Dados*:</span><span class="sxs-lookup"><span data-stu-id="f606d-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="f606d-103">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="f606d-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="f606d-104">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="f606d-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="f606d-105">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="f606d-105">Add a database connection string</span></span>

<span data-ttu-id="f606d-106">Adicione uma cadeia de conexão à *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="f606d-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="f606d-107">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="f606d-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f606d-108">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="f606d-108">Register the database context</span></span>

<span data-ttu-id="f606d-109">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f606d-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f606d-110">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f606d-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="f606d-111">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="f606d-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f606d-112">Adicione a seguinte classe `MvcMovieContext` à pasta *Models*:</span><span class="sxs-lookup"><span data-stu-id="f606d-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="f606d-113">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="f606d-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="f606d-114">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="f606d-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="f606d-115">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="f606d-115">Add a database connection string</span></span>

<span data-ttu-id="f606d-116">Adicione uma cadeia de conexão à *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="f606d-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="f606d-117">Adicionar os pacotes NuGet necessários</span><span class="sxs-lookup"><span data-stu-id="f606d-117">Add required NuGet packages</span></span>

<span data-ttu-id="f606d-118">Execute o seguinte comando da CLI do .NET Core para adicionar o SQLite e o CodeGeneration.Design ao projeto:</span><span class="sxs-lookup"><span data-stu-id="f606d-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="f606d-119">O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="f606d-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f606d-120">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="f606d-120">Register the database context</span></span>

<span data-ttu-id="f606d-121">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f606d-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f606d-122">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f606d-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="f606d-123">Compile o projeto como uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="f606d-123">Build the project as a check for errors.</span></span>
::: moniker-end