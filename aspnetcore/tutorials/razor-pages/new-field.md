---
title: Parte 7, adicionar um novo campo
author: rick-anderson
description: Parte 7 da série de tutoriais em Razor páginas.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2961fcaa8bbb93527eb80f5aa7b18ef04761fb50
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486155"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="32c4e-103">Parte 7, adicione um novo campo a uma Razor página no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="32c4e-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="32c4e-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="32c4e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="32c4e-105">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="32c4e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="32c4e-106">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="32c4e-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="32c4e-107">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-107">Add a new field to the model.</span></span>
* <span data-ttu-id="32c4e-108">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="32c4e-109">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="32c4e-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="32c4e-110">Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="32c4e-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="32c4e-111">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="32c4e-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="32c4e-112">A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.</span><span class="sxs-lookup"><span data-stu-id="32c4e-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="32c4e-113">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="32c4e-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="32c4e-114">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="32c4e-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="32c4e-115">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-115">Build the app.</span></span>

1. <span data-ttu-id="32c4e-116">Edite *pages/Movies/ Index . cshtml* e adicione um `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="32c4e-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="32c4e-117">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="32c4e-117">Update the following pages:</span></span>
   1. <span data-ttu-id="32c4e-118">Adicione o campo `Rating` às páginas Excluir e Detalhes.</span><span class="sxs-lookup"><span data-stu-id="32c4e-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="32c4e-119">Atualize [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="32c4e-120">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="32c4e-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="32c4e-121">O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="32c4e-122">Executar o aplicativo sem uma atualização para o banco de dados gera um `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="32c4e-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="32c4e-123">A `SqlException` exceção é causada pela classe de modelo de filme atualizada que é diferente do esquema da tabela de filmes do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="32c4e-124">Não há nenhuma `Rating` coluna na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="32c4e-125">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="32c4e-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="32c4e-126">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="32c4e-127">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="32c4e-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="32c4e-128">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="32c4e-129">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="32c4e-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="32c4e-130">O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="32c4e-131">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="32c4e-132">A vantagem dessa abordagem é manter os dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="32c4e-133">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="32c4e-134">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="32c4e-135">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="32c4e-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="32c4e-136">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="32c4e-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="32c4e-137">Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.</span><span class="sxs-lookup"><span data-stu-id="32c4e-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="32c4e-138">Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="32c4e-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="32c4e-139">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="32c4e-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="32c4e-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32c4e-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="32c4e-141">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="32c4e-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="32c4e-142"> No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="32c4e-143">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="32c4e-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="32c4e-144">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="32c4e-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="32c4e-145">Compare o `Movie` modelo com o `Movie` esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="32c4e-146">Crie um código para migrar o esquema de banco de dados para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="32c4e-147">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="32c4e-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="32c4e-148">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="32c4e-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="32c4e-149">O `Update-Database` comando informa à estrutura para aplicar as alterações de esquema ao banco de dados e para preservar os existentes.</span><span class="sxs-lookup"><span data-stu-id="32c4e-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="32c4e-150">Se você excluir todos os registros no banco de dados, o inicializador propagará o banco de dados e incluirá o `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="32c4e-151">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="32c4e-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="32c4e-152">Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="32c4e-153">Para excluir o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="32c4e-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="32c4e-154">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="32c4e-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="32c4e-155">Clique com o botão direito do mouse no banco de dados e selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="32c4e-156">Marque **fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="32c4e-157">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-157">Select **OK**.</span></span>
1. <span data-ttu-id="32c4e-158">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="32c4e-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="32c4e-159">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="32c4e-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="32c4e-160">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="32c4e-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="32c4e-161">Para este tutorial, você usa o recurso de *migrações* Entity Framework Core sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="32c4e-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="32c4e-162">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="32c4e-163">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="32c4e-164">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="32c4e-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="32c4e-165">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="32c4e-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="32c4e-166">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="32c4e-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="32c4e-167">Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="32c4e-168">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="32c4e-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="32c4e-169">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="32c4e-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="32c4e-170">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-170">Creating a new table.</span></span>
>* <span data-ttu-id="32c4e-171">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="32c4e-172">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="32c4e-172">Dropping the old table.</span></span>
>* <span data-ttu-id="32c4e-173">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-173">Renaming the new table.</span></span>
>
><span data-ttu-id="32c4e-174">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="32c4e-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="32c4e-175">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="32c4e-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="32c4e-176">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="32c4e-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="32c4e-177">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="32c4e-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="32c4e-178">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="32c4e-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="32c4e-179">Exclua a pasta de migração.</span><span class="sxs-lookup"><span data-stu-id="32c4e-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="32c4e-180">Use os comandos a seguir para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="32c4e-181">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="32c4e-182">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32c4e-183">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="32c4e-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="32c4e-184">[Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="32c4e-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="32c4e-185">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="32c4e-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="32c4e-186">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="32c4e-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="32c4e-187">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-187">Add a new field to the model.</span></span>
* <span data-ttu-id="32c4e-188">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="32c4e-189">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="32c4e-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="32c4e-190">Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="32c4e-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="32c4e-191">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="32c4e-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="32c4e-192">A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.</span><span class="sxs-lookup"><span data-stu-id="32c4e-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="32c4e-193">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="32c4e-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="32c4e-194">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="32c4e-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="32c4e-195">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-195">Build the app.</span></span>

1. <span data-ttu-id="32c4e-196">Edite *pages/Movies/ Index . cshtml* e adicione um `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="32c4e-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="32c4e-197">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="32c4e-197">Update the following pages:</span></span>
   1. <span data-ttu-id="32c4e-198">Adicione o campo `Rating` às páginas Excluir e Detalhes.</span><span class="sxs-lookup"><span data-stu-id="32c4e-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="32c4e-199">Atualize [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="32c4e-200">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="32c4e-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="32c4e-201">O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="32c4e-202">Executar o aplicativo sem uma atualização para o banco de dados gera um `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="32c4e-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="32c4e-203">A `SqlException` exceção é causada pela classe de modelo de filme atualizada que é diferente do esquema da tabela de filmes do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="32c4e-204">Não há nenhuma `Rating` coluna na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="32c4e-205">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="32c4e-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="32c4e-206">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="32c4e-207">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="32c4e-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="32c4e-208">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="32c4e-209">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="32c4e-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="32c4e-210">O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="32c4e-211">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="32c4e-212">A vantagem dessa abordagem é manter os dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="32c4e-213">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="32c4e-214">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="32c4e-215">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="32c4e-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="32c4e-216">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="32c4e-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="32c4e-217">Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.</span><span class="sxs-lookup"><span data-stu-id="32c4e-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="32c4e-218">Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="32c4e-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="32c4e-219">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="32c4e-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="32c4e-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32c4e-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="32c4e-221">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="32c4e-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="32c4e-222"> No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="32c4e-223">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="32c4e-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="32c4e-224">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="32c4e-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="32c4e-225">Compare o `Movie` modelo com o `Movie` esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="32c4e-226">Crie um código para migrar o esquema de banco de dados para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="32c4e-227">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="32c4e-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="32c4e-228">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="32c4e-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="32c4e-229">O `Update-Database` comando informa à estrutura para aplicar as alterações de esquema ao banco de dados e para preservar os existentes.</span><span class="sxs-lookup"><span data-stu-id="32c4e-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="32c4e-230">Se você excluir todos os registros no banco de dados, o inicializador propagará o banco de dados e incluirá o `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="32c4e-231">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="32c4e-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="32c4e-232">Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="32c4e-233">Para excluir o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="32c4e-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="32c4e-234">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="32c4e-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="32c4e-235">Clique com o botão direito do mouse no banco de dados e selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="32c4e-236">Marque **fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="32c4e-237">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-237">Select **OK**.</span></span>
* <span data-ttu-id="32c4e-238">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="32c4e-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="32c4e-239">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="32c4e-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="32c4e-240">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="32c4e-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="32c4e-241">Para este tutorial, use o recurso de *migrações* Entity Framework Core sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="32c4e-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="32c4e-242">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="32c4e-243">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="32c4e-244">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="32c4e-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="32c4e-245">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="32c4e-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="32c4e-246">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="32c4e-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="32c4e-247">Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="32c4e-248">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="32c4e-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="32c4e-249">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="32c4e-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="32c4e-250">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-250">Creating a new table.</span></span>
>* <span data-ttu-id="32c4e-251">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="32c4e-252">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="32c4e-252">Dropping the old table.</span></span>
>* <span data-ttu-id="32c4e-253">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-253">Renaming the new table.</span></span>
>
><span data-ttu-id="32c4e-254">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="32c4e-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="32c4e-255">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="32c4e-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="32c4e-256">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="32c4e-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="32c4e-257">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="32c4e-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="32c4e-258">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="32c4e-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="32c4e-259">Exclua a pasta de migração.</span><span class="sxs-lookup"><span data-stu-id="32c4e-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="32c4e-260">Use os comandos a seguir para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="32c4e-261">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="32c4e-262">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32c4e-263">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="32c4e-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="32c4e-264">[Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="32c4e-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="32c4e-265">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="32c4e-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="32c4e-266">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="32c4e-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="32c4e-267">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-267">Add a new field to the model.</span></span>
* <span data-ttu-id="32c4e-268">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="32c4e-269">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="32c4e-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="32c4e-270">Adiciona uma [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabela ao banco de dados para controlar se o esquema do banco de dados está em sincronia com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="32c4e-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="32c4e-271">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF lançará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="32c4e-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="32c4e-272">A verificação automática de que o esquema e o modelo estão em sincronia facilita a localização de problemas de código de banco de dados inconsistentes.</span><span class="sxs-lookup"><span data-stu-id="32c4e-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="32c4e-273">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="32c4e-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="32c4e-274">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="32c4e-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="32c4e-275">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-275">Build the app.</span></span>

<span data-ttu-id="32c4e-276">Edite *pages/Movies/ Index . cshtml* e adicione um `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="32c4e-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="32c4e-277">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="32c4e-277">Update the following pages:</span></span>

* <span data-ttu-id="32c4e-278">Adicione o campo `Rating` às páginas Excluir e Detalhes.</span><span class="sxs-lookup"><span data-stu-id="32c4e-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="32c4e-279">Atualize [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="32c4e-280">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="32c4e-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="32c4e-281">O aplicativo não funcionará até que o banco de dados seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="32c4e-282">Se o aplicativo for executado agora, o aplicativo lançará `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="32c4e-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="32c4e-283">Esse erro é causado devido à classe de modelo Movie atualizada ser diferente do esquema da tabela Movie do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="32c4e-284">Não há nenhuma `Rating` coluna na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="32c4e-285">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="32c4e-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="32c4e-286">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="32c4e-287">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você evolua rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="32c4e-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="32c4e-288">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="32c4e-289">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="32c4e-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="32c4e-290">O descarte do banco de dados em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com o teste é geralmente uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="32c4e-291">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="32c4e-292">A vantagem dessa abordagem é manter os dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="32c4e-293">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="32c4e-294">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="32c4e-295">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="32c4e-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="32c4e-296">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="32c4e-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="32c4e-297">Uma alteração de exemplo é mostrada abaixo, mas faça essa alteração para cada `new Movie` bloco.</span><span class="sxs-lookup"><span data-stu-id="32c4e-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="32c4e-298">Consulte o [arquivo SeedData.cs concluído](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="32c4e-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="32c4e-299">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="32c4e-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="32c4e-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32c4e-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="32c4e-301">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="32c4e-301">Add a migration for the rating field</span></span>

<span data-ttu-id="32c4e-302"> No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="32c4e-303">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="32c4e-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="32c4e-304">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="32c4e-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="32c4e-305">Compare o `Movie` modelo com o `Movie` esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="32c4e-306">Crie um código para migrar o esquema de banco de dados para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="32c4e-307">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="32c4e-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="32c4e-308">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="32c4e-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="32c4e-309">O comando `Update-Database` informa à estrutura para aplicar as alterações de esquema no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="32c4e-310">Se você excluir todos os registros no banco de dados, o inicializador propagará o banco de dados e incluirá o `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="32c4e-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="32c4e-311">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="32c4e-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="32c4e-312">Outra opção é excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="32c4e-313">Para excluir o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="32c4e-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="32c4e-314">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="32c4e-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="32c4e-315">Clique com o botão direito do mouse no banco de dados e selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="32c4e-316">Marque **fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="32c4e-317">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="32c4e-317">Select **OK**.</span></span>
* <span data-ttu-id="32c4e-318">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="32c4e-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="32c4e-319">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="32c4e-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="32c4e-320">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="32c4e-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="32c4e-321">Para este tutorial, use o recurso de *migrações* Entity Framework Core sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="32c4e-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="32c4e-322">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="32c4e-323">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="32c4e-324">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="32c4e-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="32c4e-325">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="32c4e-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="32c4e-326">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="32c4e-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="32c4e-327">Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="32c4e-328">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="32c4e-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="32c4e-329">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="32c4e-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="32c4e-330">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-330">Creating a new table.</span></span>
>* <span data-ttu-id="32c4e-331">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="32c4e-332">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="32c4e-332">Dropping the old table.</span></span>
>* <span data-ttu-id="32c4e-333">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="32c4e-333">Renaming the new table.</span></span>
>
><span data-ttu-id="32c4e-334">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="32c4e-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="32c4e-335">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="32c4e-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="32c4e-336">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="32c4e-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="32c4e-337">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="32c4e-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="32c4e-338">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="32c4e-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="32c4e-339">Excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="32c4e-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="32c4e-340">Para excluir o banco de dados, exclua o arquivo de banco de dados (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="32c4e-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="32c4e-341">Em seguida, execute o comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="32c4e-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="32c4e-342">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="32c4e-343">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="32c4e-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32c4e-344">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="32c4e-344">Additional resources</span></span>

* [<span data-ttu-id="32c4e-345">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="32c4e-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="32c4e-346">[Anterior: Adicionar pesquisa](xref:tutorials/razor-pages/search) 
>  [Em seguida: Adicionar validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="32c4e-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
