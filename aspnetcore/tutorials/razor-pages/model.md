---
title: Parte 2, adicionar um modelo
author: rick-anderson
description: Parte 2 da série de tutoriais em Razor páginas. Nesta seção, as classes de modelo são adicionadas.
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485986"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="d5e85-104">Parte 2, adicionar um modelo a um Razor aplicativo de páginas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5e85-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="d5e85-105">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d5e85-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="d5e85-106">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="d5e85-107">As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="d5e85-108">EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="d5e85-109">Primeiro, você escreve as classes de modelo e EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="d5e85-110">As classes de modelo são conhecidas como classes POCO (de "**P** Lain-**o** LD **C** LR **o** bjects") porque não têm uma dependência em EF Core.</span><span class="sxs-lookup"><span data-stu-id="d5e85-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="d5e85-111">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="d5e85-112">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d5e85-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="d5e85-113">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d5e85-115">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto *Razor PagesMovie* > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d5e85-116">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="d5e85-117">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="d5e85-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="d5e85-118">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="d5e85-119">Dê à classe o nome *Movie*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="d5e85-120">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-121">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-122">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-123">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-124">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-124">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-125">O usuário não é solicitado a inserir informações de hora no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-126">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5e85-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5e85-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d5e85-128">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="d5e85-129">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="d5e85-130">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-131">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-132">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-133">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-134">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-134">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-135">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-136">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="d5e85-137">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="d5e85-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="d5e85-138">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-138">Add a database context class</span></span>

1. <span data-ttu-id="d5e85-139">No projeto *Razor PagesMovie* , crie uma pasta chamada *Data*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="d5e85-140">Na pasta *dados* , adicione um arquivo chamado *Razor PagesMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d5e85-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="d5e85-141">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="d5e85-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="d5e85-142">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="d5e85-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="d5e85-143">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="d5e85-144">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-144">Add a database connection string</span></span>

<span data-ttu-id="d5e85-145">Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="d5e85-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="d5e85-146">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-146">Register the database context</span></span>

1. <span data-ttu-id="d5e85-147">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="d5e85-148">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5e85-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-149">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d5e85-150">Na **janela ferramenta de solução**, clique com o botão de controle no projeto *Razor PagesMovie* e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos* de pasta.</span><span class="sxs-lookup"><span data-stu-id="d5e85-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="d5e85-151">Controle-clique na pasta *modelos* e, em seguida, selecione **Adicionar** > **novo arquivo...**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="d5e85-152">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="d5e85-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="d5e85-153">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="d5e85-154">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="d5e85-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="d5e85-155">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="d5e85-156">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-157">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-158">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-159">`[DataType(DataType.Date)]`: O atributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-160">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-160">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-161">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-162">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="d5e85-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="d5e85-164">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="d5e85-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d5e85-165">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="d5e85-165">Scaffold the movie model</span></span>

<span data-ttu-id="d5e85-166">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="d5e85-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d5e85-167">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="d5e85-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d5e85-169">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="d5e85-170">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="d5e85-171">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="d5e85-172">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/5/sca.png)

1. <span data-ttu-id="d5e85-174">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

1. <span data-ttu-id="d5e85-176">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d5e85-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="d5e85-177">Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="d5e85-178">Na linha **Classe de contexto de dados**, selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="d5e85-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="d5e85-179">Na caixa de diálogo **adicionar contexto de dados** , o nome da classe *Razor PagesMovie. Data. Razor PagesMovieContext* é gerado.</span><span class="sxs-lookup"><span data-stu-id="d5e85-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="d5e85-180">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-180">Select **Add**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="d5e85-182">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="d5e85-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5e85-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5e85-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d5e85-184">Abra um shell de comando no diretório do projeto, que contém os arquivos *Program.cs*, *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5e85-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="d5e85-185">**Para Windows**: execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d5e85-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="d5e85-186">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="d5e85-187">A tabela a seguir detalha as opções do gerador de código ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5e85-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="d5e85-188">Opção</span><span class="sxs-lookup"><span data-stu-id="d5e85-188">Option</span></span>               | <span data-ttu-id="d5e85-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5e85-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="d5e85-190">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="d5e85-191">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="d5e85-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="d5e85-192">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="d5e85-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="d5e85-193">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="d5e85-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="d5e85-194">Adiciona `_ValidationScriptsPartial` para editar e criar páginas</span><span class="sxs-lookup"><span data-stu-id="d5e85-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="d5e85-195">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="d5e85-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="d5e85-196">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="d5e85-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d5e85-197">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="d5e85-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d5e85-198">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d5e85-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d5e85-199">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d5e85-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="d5e85-200">`IWebHostEnvironment` é injetado para que o aplicativo possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-201">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d5e85-202">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="d5e85-203">Clique no botão de controle na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="d5e85-204">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="d5e85-205">Clique no botão de controle na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/scaMac.png)

1. <span data-ttu-id="d5e85-207">Na caixa de diálogo **novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="d5e85-209">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d5e85-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="d5e85-210">Na **classe DbContext a ser usada:** Row, nomeie a classe *Razor PagesMovie. Data. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="d5e85-211">Selecione **Concluir**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-211">Select **Finish**.</span></span>

   ![Imagem das instruções anteriores.](model/_static/5/arpMac.png)

<span data-ttu-id="d5e85-213">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="d5e85-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d5e85-214">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="d5e85-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d5e85-215">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d5e85-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d5e85-216">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d5e85-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="d5e85-217">`IWebHostEnvironment` é injetado para que o aplicativo possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="d5e85-218">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="d5e85-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-220">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d5e85-221">*Páginas/filmes*: criar, excluir, detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="d5e85-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d5e85-222">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d5e85-223">Atualizado</span><span class="sxs-lookup"><span data-stu-id="d5e85-223">Updated</span></span>

* <span data-ttu-id="d5e85-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-224">*Startup.cs*</span></span>

<span data-ttu-id="d5e85-225">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5e85-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5e85-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d5e85-227">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="d5e85-228">*Páginas/filmes*: criar, excluir, detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="d5e85-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="d5e85-229">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-230">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d5e85-231">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d5e85-232">*Páginas/filmes*: criar, excluir, detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="d5e85-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d5e85-233">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d5e85-234">Atualizado</span><span class="sxs-lookup"><span data-stu-id="d5e85-234">Updated</span></span>

* <span data-ttu-id="d5e85-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-235">*Startup.cs*</span></span>

<span data-ttu-id="d5e85-236">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="d5e85-237">Criar o esquema de banco de dados inicial usando o recurso de migração do EF</span><span class="sxs-lookup"><span data-stu-id="d5e85-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="d5e85-238">O recurso de migrações no Entity Framework Core fornece uma maneira de:</span><span class="sxs-lookup"><span data-stu-id="d5e85-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="d5e85-239">Crie o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-239">Create the initial database schema.</span></span>
* <span data-ttu-id="d5e85-240">Atualize incrementalmente o esquema de banco de dados para mantê-lo em sincronia com o modelo de dado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="d5e85-241">O banco de dados existente é preservado.</span><span class="sxs-lookup"><span data-stu-id="d5e85-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-243">Nesta seção, a janela do **Package Manager Console** (PMC) é usada para:</span><span class="sxs-lookup"><span data-stu-id="d5e85-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="d5e85-244">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-244">Add an initial migration.</span></span>
* <span data-ttu-id="d5e85-245">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="d5e85-246">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu do PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="d5e85-248">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5e85-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="d5e85-250">Execute os seguintes comandos da CLI do .NET:</span><span class="sxs-lookup"><span data-stu-id="d5e85-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="d5e85-251">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="d5e85-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d5e85-252">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="d5e85-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d5e85-253">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="d5e85-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="d5e85-254">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="d5e85-255">O comando `migrations` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="d5e85-256">O esquema é baseado no modelo especificado em `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="d5e85-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="d5e85-257">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="d5e85-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="d5e85-258">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="d5e85-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="d5e85-259">O `update` comando executa o `Up` método em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="d5e85-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="d5e85-260">Nesse caso, `update` o executa o `Up` método no arquivo *migrations/ \<time-stamp> _InitialCreate. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d5e85-262">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d5e85-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d5e85-263">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d5e85-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d5e85-264">Serviços, como o contexto de banco de dados EF Core, são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d5e85-265">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="d5e85-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d5e85-266">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d5e85-267">A ferramenta scaffolding criou automaticamente um contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d5e85-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d5e85-268">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5e85-269">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="d5e85-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="d5e85-270">As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como criar, ler, atualizar e excluir, para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="d5e85-271">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="d5e85-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="d5e85-272">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d5e85-273">O código anterior cria uma [propriedade \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="d5e85-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="d5e85-274">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d5e85-275">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="d5e85-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d5e85-276">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="d5e85-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="d5e85-277">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-278">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d5e85-279">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="d5e85-280">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d5e85-280">Test the app</span></span>

1. <span data-ttu-id="d5e85-281">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="d5e85-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="d5e85-282">Se você receber o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="d5e85-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="d5e85-283">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="d5e85-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="d5e85-284">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-284">Test the **Create** link.</span></span>

   ![Criar página](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="d5e85-286">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d5e85-287">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="d5e85-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d5e85-288">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d5e85-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="d5e85-289">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d5e85-290">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d5e85-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5e85-291">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d5e85-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d5e85-292">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="d5e85-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="d5e85-293">Nesta seção, as classes são adicionadas para o gerenciamento de filmes.</span><span class="sxs-lookup"><span data-stu-id="d5e85-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="d5e85-294">As classes de modelo do aplicativo usam [Entity Framework Core (EF Core)](/ef/core) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="d5e85-295">EF Core é um mapeador relacional de objeto (O/RM) que simplifica O acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="d5e85-296">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="d5e85-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="d5e85-297">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="d5e85-298">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d5e85-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="d5e85-299">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-301">Clique com o botão direito do mouse no projeto **Razor PagesMovie** > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d5e85-302">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-302">Name the folder *Models*.</span></span>

<span data-ttu-id="d5e85-303">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="d5e85-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="d5e85-304">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="d5e85-305">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-305">Name the class **Movie**.</span></span>

<span data-ttu-id="d5e85-306">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-307">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-308">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-309">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-310">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-310">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-311">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-312">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d5e85-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5e85-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5e85-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d5e85-315">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="d5e85-316">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="d5e85-317">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-318">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-319">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-320">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-321">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-321">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-322">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-323">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d5e85-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="d5e85-325">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="d5e85-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="d5e85-326">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-326">Add a database context class</span></span>

* <span data-ttu-id="d5e85-327">No projeto *Razor PagesMovie* , crie uma nova pasta denominada *dados*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="d5e85-328">Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Dados*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d5e85-329">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="d5e85-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="d5e85-330">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="d5e85-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="d5e85-331">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="d5e85-332">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-332">Add a database connection string</span></span>

<span data-ttu-id="d5e85-333">Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="d5e85-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="d5e85-334">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-334">Register the database context</span></span>

<span data-ttu-id="d5e85-335">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="d5e85-336">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-337">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5e85-338">Na **janela ferramenta de solução**, clique com o botão de controle no projeto **Razor PagesMovie** e selecione **Adicionar** > **nova pasta...**. Nomeie os *modelos* de pasta.</span><span class="sxs-lookup"><span data-stu-id="d5e85-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="d5e85-339">Clique com o botão direito do mouse na pasta *modelos* e selecione **Adicionar** > **novo arquivo...**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="d5e85-340">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="d5e85-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d5e85-341">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="d5e85-342">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="d5e85-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="d5e85-343">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="d5e85-344">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-345">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-346">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-347">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-348">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-348">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-349">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-350">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="d5e85-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="d5e85-352">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="d5e85-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d5e85-353">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="d5e85-353">Scaffold the movie model</span></span>

<span data-ttu-id="d5e85-354">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="d5e85-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d5e85-355">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="d5e85-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-357">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d5e85-358">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d5e85-359">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="d5e85-360">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="d5e85-362">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="d5e85-364">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d5e85-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d5e85-365">Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d5e85-366">Na linha **classe de contexto de dados** , selecione o **+** sinal de (adição) e altere o nome gerado de Razor PagesMovie.**Modelos**. Razor PagesMovieContext Razor PagesMovie.**Dados**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="d5e85-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="d5e85-367">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="d5e85-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="d5e85-368">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="d5e85-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="d5e85-369">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-369">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="d5e85-371">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="d5e85-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5e85-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5e85-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d5e85-373">Abra uma janela de comando no diretório do projeto, que contém os arquivos *Program.cs*, *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5e85-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="d5e85-374">**Para Windows**: execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d5e85-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="d5e85-375">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="d5e85-376">A tabela a seguir detalha as opções do gerador de código ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5e85-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="d5e85-377">Opção</span><span class="sxs-lookup"><span data-stu-id="d5e85-377">Option</span></span>               | <span data-ttu-id="d5e85-378">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5e85-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="d5e85-379">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="d5e85-380">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="d5e85-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="d5e85-381">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="d5e85-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="d5e85-382">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="d5e85-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="d5e85-383">Adiciona `_ValidationScriptsPartial` para editar e criar páginas</span><span class="sxs-lookup"><span data-stu-id="d5e85-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="d5e85-384">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="d5e85-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="d5e85-385">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="d5e85-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d5e85-386">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="d5e85-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d5e85-387">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d5e85-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d5e85-388">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="d5e85-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="d5e85-389">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-390">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d5e85-391">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d5e85-392">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d5e85-393">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="d5e85-394">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="d5e85-396">Na caixa de diálogo **novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="d5e85-398">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d5e85-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d5e85-399">Na lista suspensa **classe de modelo** , selecione ou digite **filme ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d5e85-400">Na linha **classe de contexto de dados** , digite o nome da nova classe, Razor PagesMovie.**Dados**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="d5e85-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="d5e85-401">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="d5e85-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="d5e85-402">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="d5e85-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="d5e85-403">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-403">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="d5e85-405">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="d5e85-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="d5e85-406">Adicionar ferramentas do EF</span><span class="sxs-lookup"><span data-stu-id="d5e85-406">Add EF tools</span></span>

<span data-ttu-id="d5e85-407">Execute o seguinte comando de CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5e85-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="d5e85-408">O comando anterior adiciona as ferramentas de Entity Framework Core para o CLI do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5e85-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="d5e85-409">Para obter mais informações, consulte [referência de ferramentas de Entity Framework Core-CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="d5e85-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d5e85-410">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="d5e85-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d5e85-411">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d5e85-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d5e85-412">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="d5e85-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="d5e85-413">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="d5e85-414">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="d5e85-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-416">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d5e85-417">*Páginas/filmes*: criar, excluir, detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="d5e85-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d5e85-418">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d5e85-419">Atualizado</span><span class="sxs-lookup"><span data-stu-id="d5e85-419">Updated</span></span>

* <span data-ttu-id="d5e85-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-420">*Startup.cs*</span></span>

<span data-ttu-id="d5e85-421">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-422">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d5e85-423">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d5e85-424">*Páginas/filmes*: criar, excluir, detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="d5e85-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d5e85-425">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d5e85-426">Atualizado</span><span class="sxs-lookup"><span data-stu-id="d5e85-426">Updated</span></span>

* <span data-ttu-id="d5e85-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-427">*Startup.cs*</span></span>

<span data-ttu-id="d5e85-428">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5e85-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5e85-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d5e85-430">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="d5e85-431">*Páginas/filmes*: criar, excluir, detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="d5e85-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="d5e85-432">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="d5e85-433">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="d5e85-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-435">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="d5e85-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="d5e85-436">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-436">Add an initial migration.</span></span>
* <span data-ttu-id="d5e85-437">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="d5e85-438">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d5e85-440">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5e85-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-441">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d5e85-442">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5e85-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="d5e85-443">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="d5e85-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d5e85-444">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="d5e85-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d5e85-445">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="d5e85-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="d5e85-446">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="d5e85-447">O comando migrações gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="d5e85-448">O esquema é baseado no modelo especificado em `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="d5e85-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="d5e85-449">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="d5e85-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="d5e85-450">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="d5e85-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="d5e85-451">O `update` comando executa o `Up` método em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="d5e85-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="d5e85-452">Nesse caso, `update` o executa o `Up` método no arquivo  *migrations/ \<time-stamp> _InitialCreate. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d5e85-454">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d5e85-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d5e85-455">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d5e85-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d5e85-456">Serviços, como o contexto de contexto de banco de dados EF Core, são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d5e85-457">Os componentes que exigem esses serviços, como Razor páginas, são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="d5e85-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="d5e85-458">O código do construtor que obtém uma instância de contexto de contexto de banco de dados é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d5e85-459">A ferramenta scaffolding criou automaticamente um contexto de contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d5e85-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d5e85-460">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5e85-461">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="d5e85-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="d5e85-462">As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como criar, ler, atualizar e excluir, para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="d5e85-463">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="d5e85-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="d5e85-464">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d5e85-465">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="d5e85-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d5e85-466">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d5e85-467">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="d5e85-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d5e85-468">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="d5e85-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="d5e85-469">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-470">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d5e85-471">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d5e85-472">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d5e85-472">Test the app</span></span>

* <span data-ttu-id="d5e85-473">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="d5e85-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="d5e85-474">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="d5e85-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d5e85-475">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="d5e85-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="d5e85-476">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-476">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="d5e85-478">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d5e85-479">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="d5e85-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d5e85-480">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d5e85-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d5e85-481">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d5e85-482">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d5e85-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5e85-483">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d5e85-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d5e85-484">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="d5e85-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5e85-485">Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada.</span><span class="sxs-lookup"><span data-stu-id="d5e85-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="d5e85-486">Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="d5e85-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="d5e85-487">As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="d5e85-488">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="d5e85-489">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="d5e85-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="d5e85-490">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="d5e85-491">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d5e85-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="d5e85-492">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-494">Clique com o botão direito do mouse no projeto **Razor PagesMovie** > **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d5e85-495">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-495">Name the folder *Models*.</span></span>

<span data-ttu-id="d5e85-496">Clique com o botão direito do mouse na pasta *modelos* .</span><span class="sxs-lookup"><span data-stu-id="d5e85-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="d5e85-497">Selecione **Adicionar**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="d5e85-498">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-498">Name the class **Movie**.</span></span>

<span data-ttu-id="d5e85-499">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-500">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-501">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-502">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-503">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-503">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-504">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-505">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d5e85-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5e85-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5e85-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d5e85-508">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="d5e85-509">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="d5e85-510">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-511">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-512">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-513">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-514">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-514">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-515">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-516">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d5e85-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="d5e85-518">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="d5e85-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="d5e85-519">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-519">Add a database context class</span></span>

<span data-ttu-id="d5e85-520">No Razor projeto PagesMovie, crie uma nova pasta denominada *dados*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="d5e85-521">Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Dados*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d5e85-522">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="d5e85-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="d5e85-523">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="d5e85-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="d5e85-524">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="d5e85-525">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-525">Add a database connection string</span></span>

<span data-ttu-id="d5e85-526">Adicione uma cadeia de conexão ao *appsettings.json* arquivo, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="d5e85-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="d5e85-527">Adicionar os pacotes NuGet necessários</span><span class="sxs-lookup"><span data-stu-id="d5e85-527">Add required NuGet packages</span></span>

<span data-ttu-id="d5e85-528">Execute o seguinte comando CLI do .NET Core para adicionar SQLite e CodeGeneration. Design ao projeto:</span><span class="sxs-lookup"><span data-stu-id="d5e85-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="d5e85-529">O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d5e85-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="d5e85-530">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="d5e85-530">Register the database context</span></span>

<span data-ttu-id="d5e85-531">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="d5e85-532">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="d5e85-533">Compile o projeto como uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="d5e85-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-534">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5e85-535">Na **janela da ferramenta de solução**, clique com o botão de controle no projeto *Razor PagesMovie* e selecione **Adicionar**  >  **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="d5e85-536">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="d5e85-537">Controle-clique na pasta *modelos* e, em seguida, selecione **Adicionar** > **novo arquivo**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="d5e85-538">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="d5e85-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d5e85-539">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="d5e85-540">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="d5e85-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="d5e85-541">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="d5e85-542">Adicione as seguintes propriedades à classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="d5e85-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d5e85-543">A classe `Movie` contém:</span><span class="sxs-lookup"><span data-stu-id="d5e85-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="d5e85-544">O campo `ID` é necessário para o banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="d5e85-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d5e85-545">`[DataType(DataType.Date)]`: O atributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) especifica o tipo dos dados ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d5e85-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d5e85-546">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="d5e85-546">With this attribute:</span></span>

  * <span data-ttu-id="d5e85-547">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="d5e85-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d5e85-548">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d5e85-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="d5e85-550">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="d5e85-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d5e85-551">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="d5e85-551">Scaffold the movie model</span></span>

<span data-ttu-id="d5e85-552">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="d5e85-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d5e85-553">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="d5e85-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-555">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d5e85-556">Clique com o botão direito do mouse na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d5e85-557">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="d5e85-558">Clique com o botão direito do mouse na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="d5e85-560">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="d5e85-562">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d5e85-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="d5e85-563">Na lista suspensa **classe de modelo** , selecione **filme ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d5e85-564">Na linha **classe de contexto de dados** , selecione o **+** sinal de (adição) e aceite o nome gerado **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="d5e85-565">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-565">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="d5e85-567">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="d5e85-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d5e85-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d5e85-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d5e85-569">Abra uma janela de comando no diretório do projeto, que contém os arquivos *Program.cs*, *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5e85-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="d5e85-570">**Para Windows**: execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d5e85-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="d5e85-571">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="d5e85-572">A tabela a seguir detalha as opções do gerador de código ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5e85-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="d5e85-573">Opção</span><span class="sxs-lookup"><span data-stu-id="d5e85-573">Option</span></span>               | <span data-ttu-id="d5e85-574">Descrição</span><span class="sxs-lookup"><span data-stu-id="d5e85-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="d5e85-575">O nome do modelo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="d5e85-576">A classe `DbContext` a ser usada.</span><span class="sxs-lookup"><span data-stu-id="d5e85-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="d5e85-577">Use o layout padrão.</span><span class="sxs-lookup"><span data-stu-id="d5e85-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="d5e85-578">O caminho da pasta de saída relativa para criar as exibições.</span><span class="sxs-lookup"><span data-stu-id="d5e85-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="d5e85-579">Adiciona `_ValidationScriptsPartial` para editar e criar páginas</span><span class="sxs-lookup"><span data-stu-id="d5e85-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="d5e85-580">Use a `-h` opção para obter ajuda sobre o `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="d5e85-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="d5e85-581">Para obter mais informações, consulte [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="d5e85-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-582">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d5e85-583">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="d5e85-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d5e85-584">Clique no botão de controle na pasta *páginas* > **Adicionar** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d5e85-585">Nomeie a pasta *filmes*.</span><span class="sxs-lookup"><span data-stu-id="d5e85-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="d5e85-586">Clique no botão de controle na pasta *páginas/filmes* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/scaMac.png)

<span data-ttu-id="d5e85-588">Na caixa de diálogo **Adicionar novo scaffolding** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="d5e85-590">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d5e85-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d5e85-591">Na lista suspensa **classe de modelo** , selecione ou digite **filme**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="d5e85-592">Na linha **classe de contexto de dados** , digite Select **Razor PagesMovieContext** isso criará uma nova classe de contexto de banco de dado com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="d5e85-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="d5e85-593">Nesse caso, será **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="d5e85-594">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-594">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arpMac.png)

<span data-ttu-id="d5e85-596">O *appsettings.json* arquivo é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="d5e85-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="d5e85-597">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d5e85-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="d5e85-598">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="d5e85-598">Files created</span></span>

* <span data-ttu-id="d5e85-599">*Páginas/filmes*: criar, excluir, detalhes, editar e Index .</span><span class="sxs-lookup"><span data-stu-id="d5e85-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d5e85-600">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="d5e85-601">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="d5e85-601">File updated</span></span>

* <span data-ttu-id="d5e85-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d5e85-602">*Startup.cs*</span></span>

<span data-ttu-id="d5e85-603">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d5e85-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="d5e85-604">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="d5e85-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5e85-606">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="d5e85-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="d5e85-607">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-607">Add an initial migration.</span></span>
* <span data-ttu-id="d5e85-608">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="d5e85-609">No menu **Ferramentas** selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d5e85-611">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d5e85-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="d5e85-612">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="d5e85-613">O esquema é baseado no modelo especificado no `DbContext` , no arquivo *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="d5e85-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="d5e85-614">O `InitialCreate` argumento é usado para nomear a migração.</span><span class="sxs-lookup"><span data-stu-id="d5e85-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="d5e85-615">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="d5e85-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="d5e85-616">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="d5e85-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="d5e85-617">O `Update-Database` comando executa o `Up` método no arquivo *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="d5e85-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="d5e85-618">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-619">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d5e85-620">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5e85-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="d5e85-621">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="d5e85-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d5e85-622">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="d5e85-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d5e85-623">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="d5e85-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="d5e85-624">Ignore o aviso, pois ele será abordado em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="d5e85-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5e85-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5e85-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d5e85-626">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d5e85-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d5e85-627">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d5e85-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d5e85-628">Serviços (como o contexto de contexto de banco de dados EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d5e85-629">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="d5e85-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d5e85-630">O código do construtor que obtém uma instância de contexto contextB de contexto do banco de dados é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="d5e85-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d5e85-631">A ferramenta scaffolding criou automaticamente um contexto de contexto de banco de dados e o registrou com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d5e85-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d5e85-632">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5e85-633">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="d5e85-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="d5e85-634">As `RazorPagesMovieContext` coordenadas EF Core funcionalidade, como criar, ler, atualizar e excluir, para o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="d5e85-635">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="d5e85-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="d5e85-636">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d5e85-637">O código anterior cria uma [propriedade \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="d5e85-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d5e85-638">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d5e85-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d5e85-639">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="d5e85-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d5e85-640">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="d5e85-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="d5e85-641">Para o desenvolvimento local, o [sistema de configuração](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="d5e85-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d5e85-642">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d5e85-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d5e85-643">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d5e85-644">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d5e85-644">Test the app</span></span>

* <span data-ttu-id="d5e85-645">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="d5e85-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="d5e85-646">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="d5e85-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d5e85-647">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="d5e85-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="d5e85-648">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-648">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="d5e85-650">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="d5e85-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d5e85-651">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="d5e85-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d5e85-652">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d5e85-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d5e85-653">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="d5e85-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d5e85-654">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d5e85-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5e85-655">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d5e85-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d5e85-656">[Anterior:](xref:tutorials/razor-pages/razor-pages-start) 
>  introdução [Em seguida: com Scaffold Razor Páginas](xref:tutorials/razor-pages/page) do</span><span class="sxs-lookup"><span data-stu-id="d5e85-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
