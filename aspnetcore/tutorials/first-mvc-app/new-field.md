---
title: Parte 8, adicionar um novo campo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Parte 8 da série de tutoriais sobre o ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: c1548f5c3607883a32186e82624cddb71f756003
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021751"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="6a880-103">Parte 8, adicionar um novo campo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6a880-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="6a880-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6a880-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6a880-105">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="6a880-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="6a880-106">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="6a880-106">Add a new field to the model.</span></span>
* <span data-ttu-id="6a880-107">Migrar o novo campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a880-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="6a880-108">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="6a880-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="6a880-109">Adiciona uma tabela no banco de dados para rastrear o esquema do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a880-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="6a880-110">Verifica se o banco de dados está em sincronia com as classes de modelo das quais foi gerado.</span><span class="sxs-lookup"><span data-stu-id="6a880-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="6a880-111">Se ele não estiver sincronizado, o EF gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6a880-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="6a880-112">Isso facilita a descoberta de problemas de código/banco de dados inconsistente.</span><span class="sxs-lookup"><span data-stu-id="6a880-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="6a880-113">Adicionar uma Propriedade de Classificação ao Modelo de Filme</span><span class="sxs-lookup"><span data-stu-id="6a880-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="6a880-114">Adicionar uma propriedade `Rating` a *Models/Movie.cs*:</span><span class="sxs-lookup"><span data-stu-id="6a880-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="6a880-115">Compilar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6a880-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="6a880-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a880-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="6a880-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="6a880-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="6a880-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a880-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a880-119">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6a880-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6a880-120">Command ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="6a880-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="6a880-121">Como adicionou um novo campo à classe `Movie`, você também precisará atualizar a lista de permissões de associação para que essa nova propriedade seja incluída.</span><span class="sxs-lookup"><span data-stu-id="6a880-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="6a880-122">Em *MoviesController.cs*, atualize o atributo `[Bind]` dos métodos de ação `Create` e `Edit` para incluir a propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="6a880-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="6a880-123">Atualize os modelos de exibição para exibir, criar e editar a nova propriedade `Rating` na exibição do navegador.</span><span class="sxs-lookup"><span data-stu-id="6a880-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="6a880-124">Edite o arquivo */Views/Movies/Index.cshtml* e adicione um campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="6a880-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="6a880-125">Atualize */Views/Movies/Create.cshtml* com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="6a880-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="6a880-126">Visual Studio/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6a880-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="6a880-127">Copie/cole o “grupo de formulário” anterior e permita que o IntelliSense ajude você a atualizar os campos.</span><span class="sxs-lookup"><span data-stu-id="6a880-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="6a880-128">O IntelliSense funciona com os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="6a880-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![O desenvolvedor digitou a letra R para o valor do atributo asp-for no segundo elemento de rótulo da exibição.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a880-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a880-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="6a880-133">Atualize os modelos restantes.</span><span class="sxs-lookup"><span data-stu-id="6a880-133">Update the remaining templates.</span></span>

<span data-ttu-id="6a880-134">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="6a880-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="6a880-135">Uma alteração de amostra é mostrada abaixo, mas é recomendável fazer essa alteração em cada `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="6a880-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="6a880-136">O aplicativo não funcionará até que o BD seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="6a880-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="6a880-137">Se ele tiver sido executado agora, o seguinte `SqlException` será gerado:</span><span class="sxs-lookup"><span data-stu-id="6a880-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="6a880-138">Este erro ocorre porque a classe de modelo Movie atualizada é diferente do esquema da tabela Movie do banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="6a880-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="6a880-139">(Não há nenhuma coluna `Rating` na tabela de banco de dados.)</span><span class="sxs-lookup"><span data-stu-id="6a880-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="6a880-140">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="6a880-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="6a880-141">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados com base no novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="6a880-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="6a880-142">Essa abordagem é muito conveniente no início do ciclo de desenvolvimento, quando você está fazendo o desenvolvimento ativo em um banco de dados de teste. Ela permite que você desenvolva rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="6a880-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="6a880-143">No entanto, a desvantagem é que você perde os dados existentes no banco de dados – portanto, você não deseja usar essa abordagem em um banco de dados de produção!</span><span class="sxs-lookup"><span data-stu-id="6a880-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="6a880-144">Muitas vezes, o uso de um inicializador para propagar um banco de dados com os dados de teste automaticamente é uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6a880-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="6a880-145">Isso é uma boa abordagem para o desenvolvimento inicial e ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="6a880-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="6a880-146">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="6a880-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="6a880-147">A vantagem dessa abordagem é que você mantém os dados.</span><span class="sxs-lookup"><span data-stu-id="6a880-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="6a880-148">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a880-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="6a880-149">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a880-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="6a880-150">Para este tutorial, as Migrações do Code First são usadas.</span><span class="sxs-lookup"><span data-stu-id="6a880-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a880-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a880-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6a880-152"> No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="6a880-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Menu do PMC](adding-model/_static/pmc.png)

<span data-ttu-id="6a880-154">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6a880-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="6a880-155">O comando `Add-Migration` informa a estrutura de migração para examinar o atual modelo `Movie` com o atual esquema de BD `Movie` e criar o código necessário para migrar o BD para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="6a880-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="6a880-156">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="6a880-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="6a880-157">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="6a880-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="6a880-158">Se você excluir todos os registros do BD, o método de inicialização propagará o BD e incluirá o campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="6a880-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6a880-159">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6a880-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="6a880-160">Excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a880-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="6a880-161">Para excluir o banco de dados, exclua o arquivo de banco de dados (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="6a880-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="6a880-162">Em seguida, execute o comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="6a880-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="6a880-163">Execute o aplicativo e verifique se você pode criar, editar e exibir filmes com um `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="6a880-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6a880-164">[Anterior](search.md) 
>  [Avançar](validation.md)</span><span class="sxs-lookup"><span data-stu-id="6a880-164">[Previous](search.md)
[Next](validation.md)</span></span>
