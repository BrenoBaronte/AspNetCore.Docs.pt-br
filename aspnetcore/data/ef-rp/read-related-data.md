---
title: Páginas Razor com o EF Core no ASP.NET Core – Ler dados relacionados – 6 de 8
author: rick-anderson
description: Neste tutorial, você lê e exibe dados relacionados – ou seja, os dados que o Entity Framework carrega nas propriedades de navegação.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: d244ce1527486466bcbc6557ec35869aa206bc4f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656572"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="1f784-103">Páginas Razor com o EF Core no ASP.NET Core – Ler dados relacionados – 6 de 8</span><span class="sxs-lookup"><span data-stu-id="1f784-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="1f784-104">Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f784-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1f784-105">Este tutorial mostra como ler e exibir dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="1f784-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="1f784-106">Dados relacionados são dados que o EF Core carrega nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="1f784-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="1f784-107">As seguintes ilustrações mostram as páginas concluídas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="1f784-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index30.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="1f784-110">Carregamento adiantado, explícito e lento</span><span class="sxs-lookup"><span data-stu-id="1f784-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="1f784-111">Há várias maneiras pelas quais o EF Core pode carregar dados relacionados nas propriedades de navegação de uma entidade:</span><span class="sxs-lookup"><span data-stu-id="1f784-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="1f784-112">[Carregamento ansioso](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="1f784-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="1f784-113">O carregamento adiantado é quando uma consulta para um tipo de entidade também carrega entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="1f784-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="1f784-114">Quando uma entidade é lida, seus dados relacionados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="1f784-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="1f784-115">Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="1f784-116">O EF Core emitirá várias consultas para alguns tipos de carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="1f784-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="1f784-117">A emissão de várias consultas pode ser mais eficiente do que uma única consulta gigante.</span><span class="sxs-lookup"><span data-stu-id="1f784-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="1f784-118">O carregamento adiantado é especificado com os métodos `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="1f784-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="1f784-120">O carregamento adiantado envia várias consultas quando a navegação de coleção é incluída:</span><span class="sxs-lookup"><span data-stu-id="1f784-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="1f784-121">Uma consulta para a consulta principal</span><span class="sxs-lookup"><span data-stu-id="1f784-121">One query for the main query</span></span> 
  * <span data-ttu-id="1f784-122">Uma consulta para cada "borda" de coleção na árvore de carregamento.</span><span class="sxs-lookup"><span data-stu-id="1f784-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="1f784-123">Separe consultas com `Load`: os dados podem ser recuperados em consultas separadas e o EF Core "corrige" as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="1f784-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="1f784-124">"Correção" significa que o EF Core preenche automaticamente as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="1f784-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="1f784-125">A separação de consultas com `Load` é mais parecida com o carregamento explícito do que com o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="1f784-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="1f784-127">Observação: o EF Core corrige automaticamente as propriedades de navegação para outras entidades que foram carregadas anteriormente na instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="1f784-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1f784-128">Mesmo se os dados de uma propriedade de navegação *não* foram incluídos de forma explícita, a propriedade ainda pode ser populada se algumas ou todas as entidades relacionadas foram carregadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="1f784-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="1f784-129">[Carregamento explícito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="1f784-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="1f784-130">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="1f784-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1f784-131">Um código precisa ser escrito para recuperar os dados relacionados quando eles forem necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="1f784-132">O carregamento explícito com consultas separadas resulta no envio de várias consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1f784-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="1f784-133">Com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas.</span><span class="sxs-lookup"><span data-stu-id="1f784-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="1f784-134">Use o método `Load` para fazer o carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="1f784-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="1f784-135">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1f784-135">For example:</span></span>

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="1f784-137">[Carregamento preguiçoso](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="1f784-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1f784-138">[O carregamento lento foi adicionado ao EF Core na versão 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="1f784-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1f784-139">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="1f784-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1f784-140">Na primeira vez que uma propriedade de navegação é acessada, os dados necessários para essa propriedade de navegação são recuperados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="1f784-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="1f784-141">Uma consulta é enviada para o banco de dados sempre que uma propriedade de navegação é acessada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="1f784-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="1f784-142">Criar páginas do Curso</span><span class="sxs-lookup"><span data-stu-id="1f784-142">Create Course pages</span></span>

<span data-ttu-id="1f784-143">A entidade `Course` inclui uma propriedade de navegação que contém a entidade `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="1f784-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="1f784-145">Para exibir o nome do departamento atribuído para um curso:</span><span class="sxs-lookup"><span data-stu-id="1f784-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="1f784-146">Carregue a entidade relacionada `Department` na propriedade de navegação `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="1f784-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="1f784-147">Obtenha o nome da propriedade `Department` da entidade `Name`.</span><span class="sxs-lookup"><span data-stu-id="1f784-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="1f784-148">Aplicar scaffold às páginas do curso</span><span class="sxs-lookup"><span data-stu-id="1f784-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f784-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f784-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f784-150">Siga as instruções em [páginas do aluno do Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) com as seguintes exceções:</span><span class="sxs-lookup"><span data-stu-id="1f784-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="1f784-151">Crie uma pasta *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="1f784-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="1f784-152">Use `Course` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="1f784-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="1f784-153">Use a classe de contexto existente, em vez de criar uma nova.</span><span class="sxs-lookup"><span data-stu-id="1f784-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f784-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f784-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f784-155">Crie uma pasta *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="1f784-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="1f784-156">Execute o comando a seguir para aplicar scaffold das páginas do Curso.</span><span class="sxs-lookup"><span data-stu-id="1f784-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="1f784-157">**No Windows:**</span><span class="sxs-lookup"><span data-stu-id="1f784-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="1f784-158">**No Linux ou macOS:**</span><span class="sxs-lookup"><span data-stu-id="1f784-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="1f784-159">Abra *Pages/Courses/Index.cshtml.cs* e examine o método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="1f784-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="1f784-160">O mecanismo de scaffolding especificou o carregamento adiantado para a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="1f784-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="1f784-161">O método `Include` especifica o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="1f784-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="1f784-162">Execute o aplicativo e selecione o link **Cursos**.</span><span class="sxs-lookup"><span data-stu-id="1f784-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="1f784-163">A coluna de departamento exibe a `DepartmentID`, que não é útil.</span><span class="sxs-lookup"><span data-stu-id="1f784-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="1f784-164">Exibir o nome do departamento</span><span class="sxs-lookup"><span data-stu-id="1f784-164">Display the department name</span></span>

<span data-ttu-id="1f784-165">Atualize Pages/Courses/Index.cshtml.cs com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="1f784-166">O código anterior altera a propriedade `Course` para `Courses` e adiciona `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="1f784-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="1f784-167">`AsNoTracking` melhora o desempenho porque as entidades retornadas não são controladas.</span><span class="sxs-lookup"><span data-stu-id="1f784-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="1f784-168">As entidades não precisam ser controladas porque não são atualizadas no contexto atual.</span><span class="sxs-lookup"><span data-stu-id="1f784-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="1f784-169">Atualize *Pages/Courses/Index.cshtml* com o seguinte código.</span><span class="sxs-lookup"><span data-stu-id="1f784-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="1f784-170">As seguintes alterações foram feitas na biblioteca gerada por código em scaffolding:</span><span class="sxs-lookup"><span data-stu-id="1f784-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="1f784-171">O nome da propriedade `Course` foi alterado para `Courses`.</span><span class="sxs-lookup"><span data-stu-id="1f784-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="1f784-172">Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="1f784-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="1f784-173">Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="1f784-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="1f784-174">No entanto, nesse caso, a chave primária é significativa.</span><span class="sxs-lookup"><span data-stu-id="1f784-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="1f784-175">Alterou a coluna **Departamento** para que ela exiba o nome de departamento.</span><span class="sxs-lookup"><span data-stu-id="1f784-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="1f784-176">O código exibe a propriedade `Name` da entidade `Department` que é carregada na propriedade de navegação `Department`:</span><span class="sxs-lookup"><span data-stu-id="1f784-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="1f784-177">Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="1f784-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="1f784-179">Carregando dados relacionados com Select</span><span class="sxs-lookup"><span data-stu-id="1f784-179">Loading related data with Select</span></span>

<span data-ttu-id="1f784-180">O método `OnGetAsync` carrega dados relacionados com o método `Include`.</span><span class="sxs-lookup"><span data-stu-id="1f784-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="1f784-181">O método `Select` é uma alternativa que carrega apenas os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="1f784-182">Para itens únicos, como o `Department.Name`, ele usa um SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="1f784-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="1f784-183">Para coleções, ele usa outro acesso ao banco de dados, assim como o operador `Include` em coleções.</span><span class="sxs-lookup"><span data-stu-id="1f784-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="1f784-184">O seguinte código carrega dados relacionados com o método `Select`:</span><span class="sxs-lookup"><span data-stu-id="1f784-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="1f784-185">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="1f784-185">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="1f784-186">Consulte [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="1f784-186">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="1f784-187">Criar as páginas de Instrutor</span><span class="sxs-lookup"><span data-stu-id="1f784-187">Create Instructor pages</span></span>

<span data-ttu-id="1f784-188">Esta seção aplica scaffold a páginas do Instrutor e adiciona Cursos e Inscrições relacionados à página Índice de Instrutores.</span><span class="sxs-lookup"><span data-stu-id="1f784-188">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="1f784-189">![Página de índice de instrutores](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="1f784-189">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="1f784-190">Essa página lê e exibe dados relacionados das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="1f784-190">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="1f784-191">A lista de instrutores exibe dados relacionados da entidade `OfficeAssignment` (Office na imagem anterior).</span><span class="sxs-lookup"><span data-stu-id="1f784-191">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="1f784-192">As entidades `Instructor` e `OfficeAssignment` estão em uma relação um para zero ou um.</span><span class="sxs-lookup"><span data-stu-id="1f784-192">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1f784-193">O carregamento adiantado é usado para as entidades `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="1f784-193">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="1f784-194">O carregamento adiantado costuma ser mais eficiente quando os dados relacionados precisam ser exibidos.</span><span class="sxs-lookup"><span data-stu-id="1f784-194">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="1f784-195">Nesse caso, as atribuições de escritório para os instrutores são exibidas.</span><span class="sxs-lookup"><span data-stu-id="1f784-195">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="1f784-196">Quando o usuário seleciona um instrutor, as entidades `Course` relacionadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="1f784-196">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="1f784-197">As entidades `Instructor` e `Course` estão em uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="1f784-197">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="1f784-198">O carregamento adiantado é usado para entidades `Course` e suas entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="1f784-198">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="1f784-199">Nesse caso, consultas separadas podem ser mais eficientes porque somente os cursos para o instrutor selecionado são necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-199">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="1f784-200">Este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="1f784-200">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="1f784-201">Quando o usuário seleciona um curso, dados relacionados da entidade `Enrollments` são exibidos.</span><span class="sxs-lookup"><span data-stu-id="1f784-201">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="1f784-202">Na imagem anterior, o nome do aluno e a nota são exibidos.</span><span class="sxs-lookup"><span data-stu-id="1f784-202">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="1f784-203">As entidades `Course` e `Enrollment` estão em uma relação um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="1f784-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="1f784-204">Criar um modelo de exibição</span><span class="sxs-lookup"><span data-stu-id="1f784-204">Create a view model</span></span>

<span data-ttu-id="1f784-205">A página Instrutores mostra dados de três tabelas diferentes.</span><span class="sxs-lookup"><span data-stu-id="1f784-205">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="1f784-206">É necessário um modelo de exibição que inclui três propriedades que representam as três tabelas.</span><span class="sxs-lookup"><span data-stu-id="1f784-206">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="1f784-207">Crie *SchoolViewModels/InstructorIndexData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-207">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="1f784-208">Aplicar scaffold às páginas do Instrutor</span><span class="sxs-lookup"><span data-stu-id="1f784-208">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f784-209">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f784-209">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f784-210">Siga as instruções em [Aplicar scaffold às páginas do aluno](xref:data/ef-rp/intro#scaffold-student-pages) com as seguintes exceções:</span><span class="sxs-lookup"><span data-stu-id="1f784-210">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="1f784-211">Crie uma pasta *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="1f784-211">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="1f784-212">Use `Instructor` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="1f784-212">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="1f784-213">Use a classe de contexto existente, em vez de criar uma nova.</span><span class="sxs-lookup"><span data-stu-id="1f784-213">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f784-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f784-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f784-215">Crie uma pasta *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="1f784-215">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="1f784-216">Execute o comando a seguir para aplicar scaffold das páginas do instrutor.</span><span class="sxs-lookup"><span data-stu-id="1f784-216">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="1f784-217">**No Windows:**</span><span class="sxs-lookup"><span data-stu-id="1f784-217">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="1f784-218">**No Linux ou macOS:**</span><span class="sxs-lookup"><span data-stu-id="1f784-218">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="1f784-219">Para ver a aparência da página com scaffold antes de atualizá-la, execute o aplicativo e navegue até a página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="1f784-219">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="1f784-220">Atualizar *páginas/instrutores/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-220">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="1f784-221">O método `OnGetAsync` aceita dados de rota opcionais para a ID do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-221">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="1f784-222">Examine a consulta no arquivo *Pages/Instructors/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1f784-222">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="1f784-223">O código especifica o carregamento adiantado para as seguintes propriedades de navegação:</span><span class="sxs-lookup"><span data-stu-id="1f784-223">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="1f784-224">Observe a repetição dos métodos `Include` e `ThenInclude` para `CourseAssignments` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="1f784-224">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="1f784-225">Essa repetição é necessária para especificar o carregamento adiantado para duas propriedades de navegação da entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="1f784-225">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="1f784-226">O código a seguir é executado quando o instrutor é selecionado (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="1f784-226">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="1f784-227">O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="1f784-227">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="1f784-228">Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades `Course` da propriedade de navegação `CourseAssignments` desse instrutor.</span><span class="sxs-lookup"><span data-stu-id="1f784-228">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="1f784-229">O método `Where` retorna uma coleção.</span><span class="sxs-lookup"><span data-stu-id="1f784-229">The `Where` method returns a collection.</span></span> <span data-ttu-id="1f784-230">Mas, neste caso, o filtro selecionará uma única entidade.</span><span class="sxs-lookup"><span data-stu-id="1f784-230">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="1f784-231">Portanto, o método `Single` é chamado para converter a coleção em uma única entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="1f784-231">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="1f784-232">A entidade `Instructor` fornece acesso à propriedade `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="1f784-232">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="1f784-233">`CourseAssignments` fornece acesso às entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="1f784-233">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="1f784-235">O método `Single` é usado em uma coleção quando a coleção tem apenas um item.</span><span class="sxs-lookup"><span data-stu-id="1f784-235">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="1f784-236">O método `Single` gera uma exceção se a coleção está vazia ou se há mais de um item.</span><span class="sxs-lookup"><span data-stu-id="1f784-236">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="1f784-237">Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia.</span><span class="sxs-lookup"><span data-stu-id="1f784-237">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="1f784-238">O seguinte código popula a propriedade `Enrollments` do modelo de exibição quando um curso é selecionado:</span><span class="sxs-lookup"><span data-stu-id="1f784-238">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="1f784-239">Atualizar a página Índice de instrutores</span><span class="sxs-lookup"><span data-stu-id="1f784-239">Update the instructors Index page</span></span>

<span data-ttu-id="1f784-240">Atualize *Pages/Instructors/Index.cshtml* com o seguinte código.</span><span class="sxs-lookup"><span data-stu-id="1f784-240">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="1f784-241">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="1f784-241">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1f784-242">Atualiza a diretiva `page` de `@page` para `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="1f784-242">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="1f784-243">`"{id:int?}"` é um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="1f784-243">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="1f784-244">O modelo de rota altera cadeias de consulta de inteiro na URL para dados de rota.</span><span class="sxs-lookup"><span data-stu-id="1f784-244">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="1f784-245">Por exemplo, clicar no link **Selecionar** de um o instrutor apenas com a diretiva `@page` produz uma URL semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="1f784-245">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="1f784-246">Quando a diretiva de página é `@page "{id:int?}"`, a URL é:</span><span class="sxs-lookup"><span data-stu-id="1f784-246">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="1f784-247">Adiciona uma coluna **Escritório** que exibirá `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não for nulo.</span><span class="sxs-lookup"><span data-stu-id="1f784-247">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="1f784-248">Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="1f784-248">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="1f784-249">Adiciona uma coluna **Cursos** que exibe os cursos ministrados por cada instrutor.</span><span class="sxs-lookup"><span data-stu-id="1f784-249">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="1f784-250">Consulte [a transição de linha explícita](xref:mvc/views/razor#explicit-line-transition) para mais sobre esta sintaxe de navalha.</span><span class="sxs-lookup"><span data-stu-id="1f784-250">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="1f784-251">Adiciona um código que adiciona dinamicamente `class="success"` ao elemento `tr` do instrutor e do curso selecionados.</span><span class="sxs-lookup"><span data-stu-id="1f784-251">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="1f784-252">Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="1f784-252">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="1f784-253">Adiciona um novo hiperlink rotulado **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="1f784-253">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="1f784-254">Este link envia a ID do instrutor selecionado para o método `Index` e define uma cor da tela de fundo.</span><span class="sxs-lookup"><span data-stu-id="1f784-254">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="1f784-255">Adiciona uma tabela de cursos para o Instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-255">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="1f784-256">Adiciona uma tabela de inscrições de alunos para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-256">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="1f784-257">Execute o aplicativo e selecione a guia **Instrutores.** A página `Location` exibe o (escritório) da entidade relacionada. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="1f784-257">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="1f784-258">Se `OfficeAssignment` for nulo, uma célula de tabela vazia será exibida.</span><span class="sxs-lookup"><span data-stu-id="1f784-258">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="1f784-259">Clique no link **Selecionar** para um instrutor.</span><span class="sxs-lookup"><span data-stu-id="1f784-259">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="1f784-260">As alterações de estilo de linha e os cursos atribuídos a esse instrutor são exibidos.</span><span class="sxs-lookup"><span data-stu-id="1f784-260">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="1f784-261">Selecione um curso para ver a lista de alunos registrados e suas notas.</span><span class="sxs-lookup"><span data-stu-id="1f784-261">Select a course to see the list of enrolled students and their grades.</span></span>

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="1f784-263">Usando Single</span><span class="sxs-lookup"><span data-stu-id="1f784-263">Using Single</span></span>

<span data-ttu-id="1f784-264">O método `Single` pode passar a condição `Where` em vez de chamar o método `Where` separadamente:</span><span class="sxs-lookup"><span data-stu-id="1f784-264">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="1f784-265">O uso de `Single` com uma condição Where é uma questão de preferência pessoal.</span><span class="sxs-lookup"><span data-stu-id="1f784-265">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="1f784-266">Não oferece nenhum benefício sobre o uso do método `Where`.</span><span class="sxs-lookup"><span data-stu-id="1f784-266">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="1f784-267">Carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="1f784-267">Explicit loading</span></span>

<span data-ttu-id="1f784-268">O código atual especifica o carregamento adiantado para `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="1f784-268">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="1f784-269">Suponha que os usuários raramente desejem ver registros em um curso.</span><span class="sxs-lookup"><span data-stu-id="1f784-269">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="1f784-270">Nesse caso, uma otimização será carregar apenas os dados de registro se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="1f784-270">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="1f784-271">Nesta seção, o `OnGetAsync` é atualizado para usar o carregamento explícito de `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="1f784-271">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="1f784-272">Atualize *Pages/Instructors/Index.cshtml.cs* pelo seguinte código.</span><span class="sxs-lookup"><span data-stu-id="1f784-272">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="1f784-273">O código anterior remove as chamadas do método *ThenInclude* para dados de registro e de alunos.</span><span class="sxs-lookup"><span data-stu-id="1f784-273">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="1f784-274">Se um curso for selecionado, o código de carregamento explícito recuperará:</span><span class="sxs-lookup"><span data-stu-id="1f784-274">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="1f784-275">As entidades `Enrollment` para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-275">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="1f784-276">As entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="1f784-276">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="1f784-277">Observe que o código anterior comenta `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="1f784-277">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="1f784-278">As propriedades de navegação apenas podem ser carregadas de forma explícita para entidades controladas.</span><span class="sxs-lookup"><span data-stu-id="1f784-278">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="1f784-279">Testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1f784-279">Test the app.</span></span> <span data-ttu-id="1f784-280">De uma perspectiva dos usuários, o aplicativo se comporta de forma idêntica à versão anterior.</span><span class="sxs-lookup"><span data-stu-id="1f784-280">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1f784-281">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="1f784-281">Next steps</span></span>

<span data-ttu-id="1f784-282">O próximo tutorial mostra como atualizar os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="1f784-282">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="1f784-283">[Tutorial anterior](xref:data/ef-rp/complex-data-model)
>[Próximo tutorial](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="1f784-283">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f784-284">Neste tutorial, os dados relacionados são lidos e exibidos.</span><span class="sxs-lookup"><span data-stu-id="1f784-284">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="1f784-285">Dados relacionados são dados que o EF Core carrega nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="1f784-285">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="1f784-286">Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="1f784-286">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="1f784-287">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1f784-287">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="1f784-288">As seguintes ilustrações mostram as páginas concluídas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="1f784-288">The following illustrations show the completed pages for this tutorial:</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="1f784-291">Carregamento adiantado, explícito e lento de dados relacionados</span><span class="sxs-lookup"><span data-stu-id="1f784-291">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="1f784-292">Há várias maneiras pelas quais o EF Core pode carregar dados relacionados nas propriedades de navegação de uma entidade:</span><span class="sxs-lookup"><span data-stu-id="1f784-292">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="1f784-293">[Carregamento ansioso](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="1f784-293">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="1f784-294">O carregamento adiantado é quando uma consulta para um tipo de entidade também carrega entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="1f784-294">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="1f784-295">Quando a entidade é lida, seus dados relacionados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="1f784-295">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="1f784-296">Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-296">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="1f784-297">O EF Core emitirá várias consultas para alguns tipos de carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="1f784-297">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="1f784-298">A emissão de várias consultas pode ser mais eficiente do que era o caso para algumas consultas no EF6 quando havia uma única consulta.</span><span class="sxs-lookup"><span data-stu-id="1f784-298">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="1f784-299">O carregamento adiantado é especificado com os métodos `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="1f784-299">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="1f784-301">O carregamento adiantado envia várias consultas quando a navegação de coleção é incluída:</span><span class="sxs-lookup"><span data-stu-id="1f784-301">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="1f784-302">Uma consulta para a consulta principal</span><span class="sxs-lookup"><span data-stu-id="1f784-302">One query for the main query</span></span> 
  * <span data-ttu-id="1f784-303">Uma consulta para cada "borda" de coleção na árvore de carregamento.</span><span class="sxs-lookup"><span data-stu-id="1f784-303">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="1f784-304">Separe consultas com `Load`: os dados podem ser recuperados em consultas separadas e o EF Core "corrige" as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="1f784-304">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="1f784-305">"Correção" significa que o EF Core popula automaticamente as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="1f784-305">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="1f784-306">A separação de consultas com `Load` é mais parecida com o carregamento explícito do que com o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="1f784-306">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="1f784-308">Observação: o EF Core corrige automaticamente as propriedades de navegação para outras entidades que foram carregadas anteriormente na instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="1f784-308">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1f784-309">Mesmo se os dados de uma propriedade de navegação *não* foram incluídos de forma explícita, a propriedade ainda pode ser populada se algumas ou todas as entidades relacionadas foram carregadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="1f784-309">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="1f784-310">[Carregamento explícito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="1f784-310">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="1f784-311">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="1f784-311">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1f784-312">Um código precisa ser escrito para recuperar os dados relacionados quando eles forem necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-312">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="1f784-313">O carregamento explícito com consultas separadas resulta no envio de várias consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="1f784-313">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="1f784-314">Com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas.</span><span class="sxs-lookup"><span data-stu-id="1f784-314">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="1f784-315">Use o método `Load` para fazer o carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="1f784-315">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="1f784-316">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1f784-316">For example:</span></span>

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="1f784-318">[Carregamento preguiçoso](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="1f784-318">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1f784-319">[O carregamento lento foi adicionado ao EF Core na versão 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="1f784-319">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1f784-320">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="1f784-320">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1f784-321">Na primeira vez que uma propriedade de navegação é acessada, os dados necessários para essa propriedade de navegação são recuperados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="1f784-321">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="1f784-322">Uma consulta é enviada para o BD sempre que uma propriedade de navegação é acessada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="1f784-322">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="1f784-323">O operador `Select` carrega somente os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-323">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="1f784-324">Criar uma página Course que exibe o nome do departamento</span><span class="sxs-lookup"><span data-stu-id="1f784-324">Create a Course page that displays department name</span></span>

<span data-ttu-id="1f784-325">A entidade Course inclui uma propriedade de navegação que contém a entidade `Department`.</span><span class="sxs-lookup"><span data-stu-id="1f784-325">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="1f784-326">A entidade `Department` contém o departamento ao qual o curso é atribuído.</span><span class="sxs-lookup"><span data-stu-id="1f784-326">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="1f784-327">Para exibir o nome do departamento atribuído em uma lista de cursos:</span><span class="sxs-lookup"><span data-stu-id="1f784-327">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="1f784-328">Obtenha a propriedade `Name` da entidade `Department`.</span><span class="sxs-lookup"><span data-stu-id="1f784-328">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="1f784-329">A entidade `Department` é obtida da propriedade de navegação `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="1f784-329">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="1f784-331">Gerar o modelo Curso por scaffolding</span><span class="sxs-lookup"><span data-stu-id="1f784-331">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f784-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f784-332">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1f784-333">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-the-student-model) e use `Course` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="1f784-333">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f784-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f784-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="1f784-335">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="1f784-335">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="1f784-336">O comando anterior gera o modelo `Course` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="1f784-336">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="1f784-337">Abra o projeto no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1f784-337">Open the project in Visual Studio.</span></span>

<span data-ttu-id="1f784-338">Abra *Pages/Courses/Index.cshtml.cs* e examine o método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="1f784-338">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="1f784-339">O mecanismo de scaffolding especificou o carregamento adiantado para a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="1f784-339">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="1f784-340">O método `Include` especifica o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="1f784-340">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="1f784-341">Execute o aplicativo e selecione o link **Cursos**.</span><span class="sxs-lookup"><span data-stu-id="1f784-341">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="1f784-342">A coluna de departamento exibe a `DepartmentID`, que não é útil.</span><span class="sxs-lookup"><span data-stu-id="1f784-342">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="1f784-343">Atualize o método `OnGetAsync` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-343">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="1f784-344">O código anterior adiciona `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="1f784-344">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="1f784-345">`AsNoTracking` melhora o desempenho porque as entidades retornadas não são controladas.</span><span class="sxs-lookup"><span data-stu-id="1f784-345">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="1f784-346">As entidades não são controladas porque elas não são atualizadas no contexto atual.</span><span class="sxs-lookup"><span data-stu-id="1f784-346">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="1f784-347">Atualize *Pages/Courses/Index.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="1f784-347">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="1f784-348">As seguintes alterações foram feitas na biblioteca gerada por código em scaffolding:</span><span class="sxs-lookup"><span data-stu-id="1f784-348">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="1f784-349">Alterou o cabeçalho de Índice para Cursos.</span><span class="sxs-lookup"><span data-stu-id="1f784-349">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="1f784-350">Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="1f784-350">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="1f784-351">Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="1f784-351">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="1f784-352">No entanto, nesse caso, a chave primária é significativa.</span><span class="sxs-lookup"><span data-stu-id="1f784-352">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="1f784-353">Alterou a coluna **Departamento** para que ela exiba o nome de departamento.</span><span class="sxs-lookup"><span data-stu-id="1f784-353">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="1f784-354">O código exibe a propriedade `Name` da entidade `Department` que é carregada na propriedade de navegação `Department`:</span><span class="sxs-lookup"><span data-stu-id="1f784-354">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="1f784-355">Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="1f784-355">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="1f784-357">Carregando dados relacionados com Select</span><span class="sxs-lookup"><span data-stu-id="1f784-357">Loading related data with Select</span></span>

<span data-ttu-id="1f784-358">O método `OnGetAsync` carrega dados relacionados com o método `Include`:</span><span class="sxs-lookup"><span data-stu-id="1f784-358">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="1f784-359">O operador `Select` carrega somente os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-359">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="1f784-360">Para itens únicos, como o `Department.Name`, ele usa um SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="1f784-360">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="1f784-361">Para coleções, ele usa outro acesso ao banco de dados, assim como o operador `Include` em coleções.</span><span class="sxs-lookup"><span data-stu-id="1f784-361">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="1f784-362">O seguinte código carrega dados relacionados com o método `Select`:</span><span class="sxs-lookup"><span data-stu-id="1f784-362">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="1f784-363">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="1f784-363">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="1f784-364">Consulte [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="1f784-364">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="1f784-365">Criar uma página Instrutores que mostra Cursos e Registros</span><span class="sxs-lookup"><span data-stu-id="1f784-365">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="1f784-366">Nesta seção, a página Instrutores é criada.</span><span class="sxs-lookup"><span data-stu-id="1f784-366">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="1f784-367">![Página de índice de instrutores](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="1f784-367">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="1f784-368">Essa página lê e exibe dados relacionados das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="1f784-368">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="1f784-369">A lista de instrutores exibe dados relacionados da entidade `OfficeAssignment` (Office na imagem anterior).</span><span class="sxs-lookup"><span data-stu-id="1f784-369">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="1f784-370">As entidades `Instructor` e `OfficeAssignment` estão em uma relação um para zero ou um.</span><span class="sxs-lookup"><span data-stu-id="1f784-370">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1f784-371">O carregamento adiantado é usado para as entidades `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="1f784-371">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="1f784-372">O carregamento adiantado costuma ser mais eficiente quando os dados relacionados precisam ser exibidos.</span><span class="sxs-lookup"><span data-stu-id="1f784-372">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="1f784-373">Nesse caso, as atribuições de escritório para os instrutores são exibidas.</span><span class="sxs-lookup"><span data-stu-id="1f784-373">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="1f784-374">Quando o usuário seleciona um instrutor (Pedro na imagem anterior), as entidades `Course` relacionadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="1f784-374">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="1f784-375">As entidades `Instructor` e `Course` estão em uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="1f784-375">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="1f784-376">O carregamento adiantado é usado para entidades `Course` e suas entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="1f784-376">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="1f784-377">Nesse caso, consultas separadas podem ser mais eficientes porque somente os cursos para o instrutor selecionado são necessários.</span><span class="sxs-lookup"><span data-stu-id="1f784-377">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="1f784-378">Este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="1f784-378">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="1f784-379">Quando o usuário seleciona um curso (Química na imagem anterior), os dados relacionados da entidade `Enrollments` são exibidos.</span><span class="sxs-lookup"><span data-stu-id="1f784-379">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="1f784-380">Na imagem anterior, o nome do aluno e a nota são exibidos.</span><span class="sxs-lookup"><span data-stu-id="1f784-380">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="1f784-381">As entidades `Course` e `Enrollment` estão em uma relação um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="1f784-381">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="1f784-382">Criar um modelo de exibição para a exibição Índice de Instrutor</span><span class="sxs-lookup"><span data-stu-id="1f784-382">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="1f784-383">A página Instrutores mostra dados de três tabelas diferentes.</span><span class="sxs-lookup"><span data-stu-id="1f784-383">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="1f784-384">É criado um modelo de exibição que inclui as três entidades que representam as três tabelas.</span><span class="sxs-lookup"><span data-stu-id="1f784-384">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="1f784-385">Na pasta *SchoolViewModels*, crie *InstructorIndexData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-385">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="1f784-386">Gerar o modelo Instrutor por scaffolding</span><span class="sxs-lookup"><span data-stu-id="1f784-386">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f784-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f784-387">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1f784-388">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-the-student-model) e use `Instructor` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="1f784-388">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f784-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f784-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="1f784-390">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="1f784-390">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="1f784-391">O comando anterior gera o modelo `Instructor` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="1f784-391">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="1f784-392">Execute o aplicativo e navegue para a página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="1f784-392">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="1f784-393">Substitua *Pages/Instructors/Index.cshtml.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-393">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="1f784-394">O método `OnGetAsync` aceita dados de rota opcionais para a ID do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-394">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="1f784-395">Examine a consulta no arquivo *Pages/Instructors/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1f784-395">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="1f784-396">A consulta tem duas inclusões:</span><span class="sxs-lookup"><span data-stu-id="1f784-396">The query has two includes:</span></span>

* <span data-ttu-id="1f784-397">`OfficeAssignment`: exibido na [exibição de instrutores](#IP).</span><span class="sxs-lookup"><span data-stu-id="1f784-397">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="1f784-398">`CourseAssignments`: que exibe os cursos ministrados.</span><span class="sxs-lookup"><span data-stu-id="1f784-398">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="1f784-399">Atualizar a página Índice de instrutores</span><span class="sxs-lookup"><span data-stu-id="1f784-399">Update the instructors Index page</span></span>

<span data-ttu-id="1f784-400">Atualize *Pages/Instructors/Index.cshtml* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="1f784-400">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="1f784-401">A marcação anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="1f784-401">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="1f784-402">Atualiza a diretiva `page` de `@page` para `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="1f784-402">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="1f784-403">`"{id:int?}"` é um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="1f784-403">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="1f784-404">O modelo de rota altera cadeias de consulta de inteiro na URL para dados de rota.</span><span class="sxs-lookup"><span data-stu-id="1f784-404">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="1f784-405">Por exemplo, clicar no link **Selecionar** de um o instrutor apenas com a diretiva `@page` produz uma URL semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="1f784-405">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="1f784-406">Quando a diretiva de página é `@page "{id:int?}"`, a URL anterior é:</span><span class="sxs-lookup"><span data-stu-id="1f784-406">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="1f784-407">O título de página é **Instrutores**.</span><span class="sxs-lookup"><span data-stu-id="1f784-407">Page title is **Instructors**.</span></span>
* <span data-ttu-id="1f784-408">Adicionou uma coluna **Office** que exibe `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não é nulo.</span><span class="sxs-lookup"><span data-stu-id="1f784-408">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="1f784-409">Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="1f784-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="1f784-410">Adicionou uma coluna **Courses** que exibe os cursos ministrados por cada instrutor.</span><span class="sxs-lookup"><span data-stu-id="1f784-410">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="1f784-411">Consulte [a transição de linha explícita](xref:mvc/views/razor#explicit-line-transition) para mais sobre esta sintaxe de navalha.</span><span class="sxs-lookup"><span data-stu-id="1f784-411">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="1f784-412">Adicionou um código que adiciona `class="success"` dinamicamente ao elemento `tr` do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-412">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="1f784-413">Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="1f784-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="1f784-414">Adicionou um novo hiperlink rotulado **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="1f784-414">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="1f784-415">Este link envia a ID do instrutor selecionado para o método `Index` e define uma cor da tela de fundo.</span><span class="sxs-lookup"><span data-stu-id="1f784-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="1f784-416">Execute o aplicativo e selecione a guia **Instrutores.** A página `Location` exibe o (escritório) da entidade relacionada. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="1f784-416">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="1f784-417">Se OfficeAssignment é nulo, uma célula de tabela vazia é exibida.</span><span class="sxs-lookup"><span data-stu-id="1f784-417">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="1f784-418">Clique no link **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="1f784-418">Click on the **Select** link.</span></span> <span data-ttu-id="1f784-419">O estilo de linha é alterado.</span><span class="sxs-lookup"><span data-stu-id="1f784-419">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="1f784-420">Adicionar cursos ministrados pelo instrutor selecionado</span><span class="sxs-lookup"><span data-stu-id="1f784-420">Add courses taught by selected instructor</span></span>

<span data-ttu-id="1f784-421">Atualize o método `OnGetAsync` em *Pages/Instructors/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-421">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="1f784-422">Adicione `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="1f784-422">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="1f784-423">Examine a consulta atualizada:</span><span class="sxs-lookup"><span data-stu-id="1f784-423">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="1f784-424">A consulta anterior adiciona as entidades `Department`.</span><span class="sxs-lookup"><span data-stu-id="1f784-424">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="1f784-425">O código a seguir é executado quando o instrutor é selecionado (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="1f784-425">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="1f784-426">O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="1f784-426">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="1f784-427">Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades `Course` da propriedade de navegação `CourseAssignments` desse instrutor.</span><span class="sxs-lookup"><span data-stu-id="1f784-427">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="1f784-428">O método `Where` retorna uma coleção.</span><span class="sxs-lookup"><span data-stu-id="1f784-428">The `Where` method returns a collection.</span></span> <span data-ttu-id="1f784-429">No método `Where` anterior, uma única entidade `Instructor` é retornada.</span><span class="sxs-lookup"><span data-stu-id="1f784-429">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="1f784-430">O método `Single` converte a coleção em uma única entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="1f784-430">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="1f784-431">A entidade `Instructor` fornece acesso à propriedade `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="1f784-431">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="1f784-432">`CourseAssignments` fornece acesso às entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="1f784-432">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="1f784-434">O método `Single` é usado em uma coleção quando a coleção tem apenas um item.</span><span class="sxs-lookup"><span data-stu-id="1f784-434">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="1f784-435">O método `Single` gera uma exceção se a coleção está vazia ou se há mais de um item.</span><span class="sxs-lookup"><span data-stu-id="1f784-435">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="1f784-436">Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia.</span><span class="sxs-lookup"><span data-stu-id="1f784-436">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="1f784-437">O uso de `SingleOrDefault` é uma coleção vazia:</span><span class="sxs-lookup"><span data-stu-id="1f784-437">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="1f784-438">Resulta em uma exceção (da tentativa de encontrar uma propriedade `Courses` em uma referência nula).</span><span class="sxs-lookup"><span data-stu-id="1f784-438">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="1f784-439">A mensagem de exceção indica menos claramente a causa do problema.</span><span class="sxs-lookup"><span data-stu-id="1f784-439">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="1f784-440">O seguinte código popula a propriedade `Enrollments` do modelo de exibição quando um curso é selecionado:</span><span class="sxs-lookup"><span data-stu-id="1f784-440">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="1f784-441">Adicione a seguinte marcação ao final do Razor Page *Pages/Courses/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1f784-441">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="1f784-442">A marcação anterior exibe uma lista de cursos relacionados a um instrutor quando um instrutor é selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-442">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="1f784-443">Testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1f784-443">Test the app.</span></span> <span data-ttu-id="1f784-444">Clique em um link **Selecionar** na página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="1f784-444">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="1f784-445">Mostrar dados de alunos</span><span class="sxs-lookup"><span data-stu-id="1f784-445">Show student data</span></span>

<span data-ttu-id="1f784-446">Nesta seção, o aplicativo é atualizado para mostrar os dados de alunos de um curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-446">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="1f784-447">Atualize a consulta no método `OnGetAsync` em *Pages/Instructors/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-447">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="1f784-448">Atualize *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1f784-448">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="1f784-449">Adicione a seguinte marcação ao final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="1f784-449">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="1f784-450">A marcação anterior exibe uma lista dos alunos registrados no curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-450">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="1f784-451">Atualize a página e selecione um instrutor.</span><span class="sxs-lookup"><span data-stu-id="1f784-451">Refresh the page and select an instructor.</span></span> <span data-ttu-id="1f784-452">Selecione um curso para ver a lista de alunos registrados e suas notas.</span><span class="sxs-lookup"><span data-stu-id="1f784-452">Select a course to see the list of enrolled students and their grades.</span></span>

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="1f784-454">Usando Single</span><span class="sxs-lookup"><span data-stu-id="1f784-454">Using Single</span></span>

<span data-ttu-id="1f784-455">O método `Single` pode passar a condição `Where` em vez de chamar o método `Where` separadamente:</span><span class="sxs-lookup"><span data-stu-id="1f784-455">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="1f784-456">A abordagem `Single` anterior não oferece nenhum benefício em relação ao uso de `Where`.</span><span class="sxs-lookup"><span data-stu-id="1f784-456">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="1f784-457">Alguns desenvolvedores preferem o estilo de abordagem `Single`.</span><span class="sxs-lookup"><span data-stu-id="1f784-457">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="1f784-458">Carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="1f784-458">Explicit loading</span></span>

<span data-ttu-id="1f784-459">O código atual especifica o carregamento adiantado para `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="1f784-459">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="1f784-460">Suponha que os usuários raramente desejem ver registros em um curso.</span><span class="sxs-lookup"><span data-stu-id="1f784-460">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="1f784-461">Nesse caso, uma otimização será carregar apenas os dados de registro se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="1f784-461">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="1f784-462">Nesta seção, o `OnGetAsync` é atualizado para usar o carregamento explícito de `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="1f784-462">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="1f784-463">Atualize o `OnGetAsync` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f784-463">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="1f784-464">O código anterior remove as chamadas do método *ThenInclude* para dados de registro e de alunos.</span><span class="sxs-lookup"><span data-stu-id="1f784-464">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="1f784-465">Se um curso é selecionado, o código realçado recupera:</span><span class="sxs-lookup"><span data-stu-id="1f784-465">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="1f784-466">As entidades `Enrollment` para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="1f784-466">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="1f784-467">As entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="1f784-467">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="1f784-468">Observe que o código anterior comenta `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="1f784-468">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="1f784-469">As propriedades de navegação apenas podem ser carregadas de forma explícita para entidades controladas.</span><span class="sxs-lookup"><span data-stu-id="1f784-469">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="1f784-470">Testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1f784-470">Test the app.</span></span> <span data-ttu-id="1f784-471">De uma perspectiva dos usuários, o aplicativo se comporta de forma idêntica à versão anterior.</span><span class="sxs-lookup"><span data-stu-id="1f784-471">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="1f784-472">O próximo tutorial mostra como atualizar os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="1f784-472">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f784-473">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="1f784-473">Additional resources</span></span>

* [<span data-ttu-id="1f784-474">Versão do YouTube deste tutorial (parte 1)</span><span class="sxs-lookup"><span data-stu-id="1f784-474">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="1f784-475">Versão do YouTube deste tutorial (parte 2)</span><span class="sxs-lookup"><span data-stu-id="1f784-475">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="1f784-476">[Próximo](xref:data/ef-rp/complex-data-model)
>[anterior](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="1f784-476">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
