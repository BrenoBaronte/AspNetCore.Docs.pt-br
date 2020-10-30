---
title: 'Parte 6, :::no-loc(Razor)::: páginas com EF Core em dados relacionados à leitura de ASP.NET Core'
author: rick-anderson
description: 'Parte 6 de :::no-loc(Razor)::: páginas e Entity Framework série de tutoriais.'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061061"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="3fe1a-103">Parte 6, :::no-loc(Razor)::: páginas com EF Core em dados relacionados à leitura de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fe1a-103">Part 6, :::no-loc(Razor)::: Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="3fe1a-104">Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3fe1a-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3fe1a-105">Este tutorial mostra como ler e exibir dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="3fe1a-106">Dados relacionados são dados que o EF Core carrega nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="3fe1a-107">As seguintes ilustrações mostram as páginas concluídas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index30.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="3fe1a-110">Carregamento adiantado, explícito e lento</span><span class="sxs-lookup"><span data-stu-id="3fe1a-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="3fe1a-111">Há várias maneiras pelas quais o EF Core pode carregar dados relacionados nas propriedades de navegação de uma entidade:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="3fe1a-112">[Carregamento adiantado](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="3fe1a-113">O carregamento adiantado é quando uma consulta para um tipo de entidade também carrega entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="3fe1a-114">Quando uma entidade é lida, seus dados relacionados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="3fe1a-115">Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="3fe1a-116">O EF Core emitirá várias consultas para alguns tipos de carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="3fe1a-117">A emissão de várias consultas pode ser mais eficiente do que uma única consulta gigante.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="3fe1a-118">O carregamento adiantado é especificado com os métodos `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="3fe1a-120">O carregamento adiantado envia várias consultas quando a navegação de coleção é incluída:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="3fe1a-121">Uma consulta para a consulta principal</span><span class="sxs-lookup"><span data-stu-id="3fe1a-121">One query for the main query</span></span> 
  * <span data-ttu-id="3fe1a-122">Uma consulta para cada "borda" de coleção na árvore de carregamento.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="3fe1a-123">Separe consultas com `Load`: os dados podem ser recuperados em consultas separadas e o EF Core "corrige" as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="3fe1a-124">"Correção" significa que o EF Core preenche automaticamente as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="3fe1a-125">A separação de consultas com `Load` é mais parecida com o carregamento explícito do que com o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="3fe1a-127">**Observação:** EF Core automaticamente corrige Propriedades de navegação para quaisquer outras entidades que foram carregadas anteriormente na instância de contexto.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="3fe1a-128">Mesmo se os dados de uma propriedade de navegação *não* foram incluídos de forma explícita, a propriedade ainda pode ser populada se algumas ou todas as entidades relacionadas foram carregadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="3fe1a-129">[Carregamento explícito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="3fe1a-130">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3fe1a-131">Um código precisa ser escrito para recuperar os dados relacionados quando eles forem necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="3fe1a-132">O carregamento explícito com consultas separadas resulta no envio de várias consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="3fe1a-133">Com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="3fe1a-134">Use o método `Load` para fazer o carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="3fe1a-135">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-135">For example:</span></span>

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="3fe1a-137">[Carregamento lento](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="3fe1a-138">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3fe1a-139">Na primeira vez que uma propriedade de navegação é acessada, os dados necessários para essa propriedade de navegação são recuperados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="3fe1a-140">Uma consulta é enviada para o banco de dados sempre que uma propriedade de navegação é acessada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="3fe1a-141">O carregamento lento pode prejudicar o desempenho, por exemplo, quando os desenvolvedores usam padrões de N + 1, carregando um pai e enumerando por meio de filhos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-141">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="3fe1a-142">Criar páginas do Curso</span><span class="sxs-lookup"><span data-stu-id="3fe1a-142">Create Course pages</span></span>

<span data-ttu-id="3fe1a-143">A entidade `Course` inclui uma propriedade de navegação que contém a entidade `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="3fe1a-145">Para exibir o nome do departamento atribuído para um curso:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="3fe1a-146">Carregue a entidade relacionada `Department` na propriedade de navegação `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="3fe1a-147">Obtenha o nome da propriedade `Department` da entidade `Name`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="3fe1a-148">Aplicar scaffold às páginas do curso</span><span class="sxs-lookup"><span data-stu-id="3fe1a-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3fe1a-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fe1a-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3fe1a-150">Siga as instruções em [páginas do aluno do Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) com as seguintes exceções:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="3fe1a-151">Crie uma pasta *Pages/Courses* .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="3fe1a-152">Use `Course` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="3fe1a-153">Use a classe de contexto existente, em vez de criar uma nova.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3fe1a-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fe1a-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3fe1a-155">Crie uma pasta *Pages/Courses* .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="3fe1a-156">Execute o comando a seguir para aplicar scaffold das páginas do Curso.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="3fe1a-157">**No Windows:**</span><span class="sxs-lookup"><span data-stu-id="3fe1a-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="3fe1a-158">**No Linux ou macOS:**</span><span class="sxs-lookup"><span data-stu-id="3fe1a-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="3fe1a-159">Abra *Pages/Courses/Index.cshtml.cs* e examine o método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="3fe1a-160">O mecanismo de scaffolding especificou o carregamento adiantado para a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="3fe1a-161">O método `Include` especifica o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="3fe1a-162">Execute o aplicativo e selecione o link **Cursos** .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="3fe1a-163">A coluna de departamento exibe a `DepartmentID`, que não é útil.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="3fe1a-164">Exibir o nome do departamento</span><span class="sxs-lookup"><span data-stu-id="3fe1a-164">Display the department name</span></span>

<span data-ttu-id="3fe1a-165">Atualize Pages/Courses/Index.cshtml.cs com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="3fe1a-166">O código anterior altera a propriedade `Course` para `Courses` e adiciona `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="3fe1a-167">`AsNoTracking` melhora o desempenho porque as entidades retornadas não são controladas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="3fe1a-168">As entidades não precisam ser controladas porque não são atualizadas no contexto atual.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="3fe1a-169">Atualize *Pages/Courses/Index.cshtml* com o seguinte código.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="3fe1a-170">As seguintes alterações foram feitas na biblioteca gerada por código em scaffolding:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="3fe1a-171">O nome da propriedade `Course` foi alterado para `Courses`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="3fe1a-172">Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="3fe1a-173">Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="3fe1a-174">No entanto, nesse caso, a chave primária é significativa.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="3fe1a-175">Alterou a coluna **Departamento** para que ela exiba o nome de departamento.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="3fe1a-176">O código exibe a propriedade `Name` da entidade `Department` que é carregada na propriedade de navegação `Department`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="3fe1a-177">Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="3fe1a-179">Carregando dados relacionados com Select</span><span class="sxs-lookup"><span data-stu-id="3fe1a-179">Loading related data with Select</span></span>

<span data-ttu-id="3fe1a-180">O método `OnGetAsync` carrega dados relacionados com o método `Include`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="3fe1a-181">O método `Select` é uma alternativa que carrega apenas os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="3fe1a-182">Para itens únicos, como o `Department.Name`, ele usa um SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="3fe1a-183">Para coleções, ele usa outro acesso ao banco de dados, assim como o operador `Include` em coleções.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="3fe1a-184">O seguinte código carrega dados relacionados com o método `Select`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="3fe1a-185">O código anterior não retorna nenhum tipo de entidade, portanto, nenhum controle é feito.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="3fe1a-186">Para obter mais informações sobre o controle do EF, consulte [acompanhamento versus No-Tracking consultas](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="3fe1a-187">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="3fe1a-188">Consulte [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="3fe1a-189">Criar as páginas de Instrutor</span><span class="sxs-lookup"><span data-stu-id="3fe1a-189">Create Instructor pages</span></span>

<span data-ttu-id="3fe1a-190">Esta seção aplica scaffold a páginas do Instrutor e adiciona Cursos e Inscrições relacionados à página Índice de Instrutores.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="3fe1a-191">![Página de índice de instrutores](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="3fe1a-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="3fe1a-192">Essa página lê e exibe dados relacionados das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="3fe1a-193">A lista de instrutores exibe dados relacionados da entidade `OfficeAssignment` (Office na imagem anterior).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="3fe1a-194">As entidades `Instructor` e `OfficeAssignment` estão em uma relação um para zero ou um.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="3fe1a-195">O carregamento adiantado é usado para as entidades `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="3fe1a-196">O carregamento adiantado costuma ser mais eficiente quando os dados relacionados precisam ser exibidos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="3fe1a-197">Nesse caso, as atribuições de escritório para os instrutores são exibidas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="3fe1a-198">Quando o usuário seleciona um instrutor, as entidades `Course` relacionadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="3fe1a-199">As entidades `Instructor` e `Course` estão em uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="3fe1a-200">O carregamento adiantado é usado para entidades `Course` e suas entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="3fe1a-201">Nesse caso, consultas separadas podem ser mais eficientes porque somente os cursos para o instrutor selecionado são necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="3fe1a-202">Este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="3fe1a-203">Quando o usuário seleciona um curso, dados relacionados da entidade `Enrollments` são exibidos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="3fe1a-204">Na imagem anterior, o nome do aluno e a nota são exibidos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="3fe1a-205">As entidades `Course` e `Enrollment` estão em uma relação um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="3fe1a-206">Criar um modelo de exibição</span><span class="sxs-lookup"><span data-stu-id="3fe1a-206">Create a view model</span></span>

<span data-ttu-id="3fe1a-207">A página Instrutores mostra dados de três tabelas diferentes.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="3fe1a-208">É necessário um modelo de exibição que inclui três propriedades que representam as três tabelas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="3fe1a-209">Crie *SchoolViewModels/InstructorIndexData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="3fe1a-210">Aplicar scaffold às páginas do Instrutor</span><span class="sxs-lookup"><span data-stu-id="3fe1a-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3fe1a-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fe1a-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3fe1a-212">Siga as instruções em [Aplicar scaffold às páginas do aluno](xref:data/ef-rp/intro#scaffold-student-pages) com as seguintes exceções:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="3fe1a-213">Crie uma pasta *Pages/Instructors* .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="3fe1a-214">Use `Instructor` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="3fe1a-215">Use a classe de contexto existente, em vez de criar uma nova.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3fe1a-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fe1a-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3fe1a-217">Crie uma pasta *Pages/Instructors* .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="3fe1a-218">Execute o comando a seguir para aplicar scaffold das páginas do instrutor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="3fe1a-219">**No Windows:**</span><span class="sxs-lookup"><span data-stu-id="3fe1a-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="3fe1a-220">**No Linux ou macOS:**</span><span class="sxs-lookup"><span data-stu-id="3fe1a-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="3fe1a-221">Para ver a aparência da página com scaffold antes de atualizá-la, execute o aplicativo e navegue até a página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="3fe1a-222">Atualize *páginas/instrutores/index. cshtml. cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="3fe1a-223">O método `OnGetAsync` aceita dados de rota opcionais para a ID do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="3fe1a-224">Examine a consulta no arquivo *Pages/Instructors/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3fe1a-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="3fe1a-225">O código especifica o carregamento adiantado para as seguintes propriedades de navegação:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="3fe1a-226">Observe a repetição dos métodos `Include` e `ThenInclude` para `CourseAssignments` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="3fe1a-227">Essa repetição é necessária para especificar o carregamento adiantado para duas propriedades de navegação da entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="3fe1a-228">O código a seguir é executado quando o instrutor é selecionado (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="3fe1a-229">O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="3fe1a-230">Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades `Course` da propriedade de navegação `CourseAssignments` desse instrutor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="3fe1a-231">O método `Where` retorna uma coleção.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="3fe1a-232">Mas, nesse caso, o filtro selecionará uma única entidade, de modo que o `Single` método é chamado para converter a coleção em uma única `Instructor` entidade.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="3fe1a-233">A entidade `Instructor` fornece acesso à propriedade `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="3fe1a-234">`CourseAssignments` fornece acesso às entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="3fe1a-236">O método `Single` é usado em uma coleção quando a coleção tem apenas um item.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="3fe1a-237">O método `Single` gera uma exceção se a coleção está vazia ou se há mais de um item.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="3fe1a-238">Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="3fe1a-239">O seguinte código popula a propriedade `Enrollments` do modelo de exibição quando um curso é selecionado:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="3fe1a-240">Atualizar a página Índice de instrutores</span><span class="sxs-lookup"><span data-stu-id="3fe1a-240">Update the instructors Index page</span></span>

<span data-ttu-id="3fe1a-241">Atualize *Pages/Instructors/Index.cshtml* com o seguinte código.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="3fe1a-242">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="3fe1a-243">Atualiza a diretiva `page` de `@page` para `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="3fe1a-244">`"{id:int?}"` é um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="3fe1a-245">O modelo de rota altera cadeias de consulta de inteiro na URL para dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="3fe1a-246">Por exemplo, clicar no link **Selecionar** de um o instrutor apenas com a diretiva `@page` produz uma URL semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="3fe1a-247">Quando a diretiva de página é `@page "{id:int?}"`, a URL é:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="3fe1a-248">Adiciona uma coluna **Escritório** que exibirá `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não for nulo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="3fe1a-249">Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="3fe1a-250">Adiciona uma coluna **Cursos** que exibe os cursos ministrados por cada instrutor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="3fe1a-251">Consulte [transição de linha explícita](xref:mvc/views/razor#explicit-line-transition) para obter mais informações sobre essa sintaxe do Razor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="3fe1a-252">Adiciona um código que adiciona dinamicamente `class="success"` ao elemento `tr` do instrutor e do curso selecionados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="3fe1a-253">Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="3fe1a-254">Adiciona um novo hiperlink rotulado **Selecionar** .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-254">Adds a new hyperlink labeled **Select** .</span></span> <span data-ttu-id="3fe1a-255">Este link envia a ID do instrutor selecionado para o método `Index` e define uma cor da tela de fundo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="3fe1a-256">Adiciona uma tabela de cursos para o Instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="3fe1a-257">Adiciona uma tabela de inscrições de alunos para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="3fe1a-258">Execute o aplicativo e selecione a guia **instrutores** . A página exibe o `Location` (Office) da entidade relacionada `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="3fe1a-259">Se `OfficeAssignment` for nulo, uma célula de tabela vazia será exibida.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="3fe1a-260">Clique no link **Selecionar** para um instrutor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="3fe1a-261">As alterações de estilo de linha e os cursos atribuídos a esse instrutor são exibidos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="3fe1a-262">Selecione um curso para ver a lista de alunos registrados e suas notas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-262">Select a course to see the list of enrolled students and their grades.</span></span>

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="3fe1a-264">Usando Single</span><span class="sxs-lookup"><span data-stu-id="3fe1a-264">Using Single</span></span>

<span data-ttu-id="3fe1a-265">O método `Single` pode passar a condição `Where` em vez de chamar o método `Where` separadamente:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="3fe1a-266">O uso de `Single` com uma condição Where é uma questão de preferência pessoal.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="3fe1a-267">Não oferece nenhum benefício sobre o uso do método `Where`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="3fe1a-268">Carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="3fe1a-268">Explicit loading</span></span>

<span data-ttu-id="3fe1a-269">O código atual especifica o carregamento adiantado para `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="3fe1a-270">Suponha que os usuários raramente desejem ver registros em um curso.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="3fe1a-271">Nesse caso, uma otimização será carregar apenas os dados de registro se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="3fe1a-272">Nesta seção, o `OnGetAsync` é atualizado para usar o carregamento explícito de `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="3fe1a-273">Atualize *Pages/Instructors/Index.cshtml.cs* pelo seguinte código.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="3fe1a-274">O código anterior remove as chamadas do método *ThenInclude* para dados de registro e de alunos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="3fe1a-275">Se um curso for selecionado, o código de carregamento explícito recuperará:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="3fe1a-276">As entidades `Enrollment` para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="3fe1a-277">As entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="3fe1a-278">Observe que o código anterior comenta `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="3fe1a-279">As propriedades de navegação apenas podem ser carregadas de forma explícita para entidades controladas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="3fe1a-280">Testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-280">Test the app.</span></span> <span data-ttu-id="3fe1a-281">De uma perspectiva dos usuários, o aplicativo se comporta de forma idêntica à versão anterior.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3fe1a-282">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3fe1a-282">Next steps</span></span>

<span data-ttu-id="3fe1a-283">O próximo tutorial mostra como atualizar os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="3fe1a-284">[Tutorial anterior](xref:data/ef-rp/complex-data-model) 
> [Próximo tutorial](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="3fe1a-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3fe1a-285">Neste tutorial, os dados relacionados são lidos e exibidos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="3fe1a-286">Dados relacionados são dados que o EF Core carrega nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="3fe1a-287">Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="3fe1a-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="3fe1a-288">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="3fe1a-289">As seguintes ilustrações mostram as páginas concluídas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="3fe1a-292">Carregamento adiantado, explícito e lento de dados relacionados</span><span class="sxs-lookup"><span data-stu-id="3fe1a-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="3fe1a-293">Há várias maneiras pelas quais o EF Core pode carregar dados relacionados nas propriedades de navegação de uma entidade:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="3fe1a-294">[Carregamento adiantado](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="3fe1a-295">O carregamento adiantado é quando uma consulta para um tipo de entidade também carrega entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="3fe1a-296">Quando a entidade é lida, seus dados relacionados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="3fe1a-297">Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="3fe1a-298">O EF Core emitirá várias consultas para alguns tipos de carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="3fe1a-299">A emissão de várias consultas pode ser mais eficiente do que era o caso para algumas consultas no EF6 quando havia uma única consulta.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="3fe1a-300">O carregamento adiantado é especificado com os métodos `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="3fe1a-302">O carregamento adiantado envia várias consultas quando a navegação de coleção é incluída:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="3fe1a-303">Uma consulta para a consulta principal</span><span class="sxs-lookup"><span data-stu-id="3fe1a-303">One query for the main query</span></span> 
  * <span data-ttu-id="3fe1a-304">Uma consulta para cada "borda" de coleção na árvore de carregamento.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="3fe1a-305">Separe consultas com `Load`: os dados podem ser recuperados em consultas separadas e o EF Core "corrige" as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="3fe1a-306">"Correção" significa que o EF Core popula automaticamente as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="3fe1a-307">A separação de consultas com `Load` é mais parecida com o carregamento explícito do que com o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="3fe1a-309">Observação: o EF Core corrige automaticamente as propriedades de navegação para outras entidades que foram carregadas anteriormente na instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="3fe1a-310">Mesmo se os dados de uma propriedade de navegação *não* foram incluídos de forma explícita, a propriedade ainda pode ser populada se algumas ou todas as entidades relacionadas foram carregadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="3fe1a-311">[Carregamento explícito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="3fe1a-312">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3fe1a-313">Um código precisa ser escrito para recuperar os dados relacionados quando eles forem necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="3fe1a-314">O carregamento explícito com consultas separadas resulta no envio de várias consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="3fe1a-315">Com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="3fe1a-316">Use o método `Load` para fazer o carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="3fe1a-317">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-317">For example:</span></span>

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="3fe1a-319">[Carregamento lento](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="3fe1a-320">[O carregamento lento foi adicionado ao EF Core na versão 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="3fe1a-321">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3fe1a-322">Na primeira vez que uma propriedade de navegação é acessada, os dados necessários para essa propriedade de navegação são recuperados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="3fe1a-323">Uma consulta é enviada para o BD sempre que uma propriedade de navegação é acessada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="3fe1a-324">O operador `Select` carrega somente os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="3fe1a-325">Criar uma página Course que exibe o nome do departamento</span><span class="sxs-lookup"><span data-stu-id="3fe1a-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="3fe1a-326">A entidade Course inclui uma propriedade de navegação que contém a entidade `Department`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="3fe1a-327">A entidade `Department` contém o departamento ao qual o curso é atribuído.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="3fe1a-328">Para exibir o nome do departamento atribuído em uma lista de cursos:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="3fe1a-329">Obtenha a propriedade `Name` da entidade `Department`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="3fe1a-330">A entidade `Department` é obtida da propriedade de navegação `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="3fe1a-332">Gerar o modelo Curso por scaffolding</span><span class="sxs-lookup"><span data-stu-id="3fe1a-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3fe1a-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fe1a-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="3fe1a-334">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-the-student-model) e use `Course` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3fe1a-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fe1a-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="3fe1a-336">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="3fe1a-337">O comando anterior gera o modelo `Course` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="3fe1a-338">Abra o projeto no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="3fe1a-339">Abra *Pages/Courses/Index.cshtml.cs* e examine o método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="3fe1a-340">O mecanismo de scaffolding especificou o carregamento adiantado para a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="3fe1a-341">O método `Include` especifica o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="3fe1a-342">Execute o aplicativo e selecione o link **Cursos** .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="3fe1a-343">A coluna de departamento exibe a `DepartmentID`, que não é útil.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="3fe1a-344">Atualize o método `OnGetAsync` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="3fe1a-345">O código anterior adiciona `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="3fe1a-346">`AsNoTracking` melhora o desempenho porque as entidades retornadas não são controladas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="3fe1a-347">As entidades não são controladas porque elas não são atualizadas no contexto atual.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="3fe1a-348">Atualize *Pages/Courses/Index.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="3fe1a-349">As seguintes alterações foram feitas na biblioteca gerada por código em scaffolding:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="3fe1a-350">Alterou o cabeçalho de Índice para Cursos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="3fe1a-351">Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="3fe1a-352">Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="3fe1a-353">No entanto, nesse caso, a chave primária é significativa.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="3fe1a-354">Alterou a coluna **Departamento** para que ela exiba o nome de departamento.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="3fe1a-355">O código exibe a propriedade `Name` da entidade `Department` que é carregada na propriedade de navegação `Department`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="3fe1a-356">Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="3fe1a-358">Carregando dados relacionados com Select</span><span class="sxs-lookup"><span data-stu-id="3fe1a-358">Loading related data with Select</span></span>

<span data-ttu-id="3fe1a-359">O método `OnGetAsync` carrega dados relacionados com o método `Include`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="3fe1a-360">O operador `Select` carrega somente os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="3fe1a-361">Para itens únicos, como o `Department.Name`, ele usa um SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="3fe1a-362">Para coleções, ele usa outro acesso ao banco de dados, assim como o operador `Include` em coleções.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="3fe1a-363">O seguinte código carrega dados relacionados com o método `Select`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="3fe1a-364">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="3fe1a-365">Consulte [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="3fe1a-366">Criar uma página Instrutores que mostra Cursos e Registros</span><span class="sxs-lookup"><span data-stu-id="3fe1a-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="3fe1a-367">Nesta seção, a página Instrutores é criada.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="3fe1a-368">![Página de índice de instrutores](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="3fe1a-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="3fe1a-369">Essa página lê e exibe dados relacionados das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="3fe1a-370">A lista de instrutores exibe dados relacionados da entidade `OfficeAssignment` (Office na imagem anterior).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="3fe1a-371">As entidades `Instructor` e `OfficeAssignment` estão em uma relação um para zero ou um.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="3fe1a-372">O carregamento adiantado é usado para as entidades `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="3fe1a-373">O carregamento adiantado costuma ser mais eficiente quando os dados relacionados precisam ser exibidos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="3fe1a-374">Nesse caso, as atribuições de escritório para os instrutores são exibidas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="3fe1a-375">Quando o usuário seleciona um instrutor (Pedro na imagem anterior), as entidades `Course` relacionadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="3fe1a-376">As entidades `Instructor` e `Course` estão em uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="3fe1a-377">O carregamento adiantado é usado para entidades `Course` e suas entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="3fe1a-378">Nesse caso, consultas separadas podem ser mais eficientes porque somente os cursos para o instrutor selecionado são necessários.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="3fe1a-379">Este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="3fe1a-380">Quando o usuário seleciona um curso (Química na imagem anterior), os dados relacionados da entidade `Enrollments` são exibidos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="3fe1a-381">Na imagem anterior, o nome do aluno e a nota são exibidos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="3fe1a-382">As entidades `Course` e `Enrollment` estão em uma relação um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="3fe1a-383">Criar um modelo de exibição para a exibição Índice de Instrutor</span><span class="sxs-lookup"><span data-stu-id="3fe1a-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="3fe1a-384">A página Instrutores mostra dados de três tabelas diferentes.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="3fe1a-385">É criado um modelo de exibição que inclui as três entidades que representam as três tabelas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="3fe1a-386">Na pasta *SchoolViewModels* , crie *InstructorIndexData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="3fe1a-387">Gerar o modelo Instrutor por scaffolding</span><span class="sxs-lookup"><span data-stu-id="3fe1a-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3fe1a-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fe1a-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="3fe1a-389">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-the-student-model) e use `Instructor` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3fe1a-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fe1a-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="3fe1a-391">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="3fe1a-392">O comando anterior gera o modelo `Instructor` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="3fe1a-393">Execute o aplicativo e navegue para a página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="3fe1a-394">Substitua *Pages/Instructors/Index.cshtml.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="3fe1a-395">O método `OnGetAsync` aceita dados de rota opcionais para a ID do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="3fe1a-396">Examine a consulta no arquivo *Pages/Instructors/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3fe1a-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="3fe1a-397">A consulta tem duas inclusões:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-397">The query has two includes:</span></span>

* <span data-ttu-id="3fe1a-398">`OfficeAssignment`: exibido na [exibição de instrutores](#IP).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="3fe1a-399">`CourseAssignments`: que exibe os cursos ministrados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="3fe1a-400">Atualizar a página Índice de instrutores</span><span class="sxs-lookup"><span data-stu-id="3fe1a-400">Update the instructors Index page</span></span>

<span data-ttu-id="3fe1a-401">Atualize *Pages/Instructors/Index.cshtml* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="3fe1a-402">A marcação anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="3fe1a-403">Atualiza a diretiva `page` de `@page` para `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="3fe1a-404">`"{id:int?}"` é um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="3fe1a-405">O modelo de rota altera cadeias de consulta de inteiro na URL para dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="3fe1a-406">Por exemplo, clicar no link **Selecionar** de um o instrutor apenas com a diretiva `@page` produz uma URL semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="3fe1a-407">Quando a diretiva de página é `@page "{id:int?}"`, a URL anterior é:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="3fe1a-408">O título de página é **Instrutores** .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-408">Page title is **Instructors** .</span></span>
* <span data-ttu-id="3fe1a-409">Adicionou uma coluna **Office** que exibe `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não é nulo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="3fe1a-410">Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="3fe1a-411">Adicionou uma coluna **Courses** que exibe os cursos ministrados por cada instrutor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="3fe1a-412">Consulte [transição de linha explícita](xref:mvc/views/razor#explicit-line-transition) para obter mais informações sobre essa sintaxe do Razor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="3fe1a-413">Adicionou um código que adiciona `class="success"` dinamicamente ao elemento `tr` do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="3fe1a-414">Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="3fe1a-415">Adicionou um novo hiperlink rotulado **Selecionar** .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-415">Added a new hyperlink labeled **Select** .</span></span> <span data-ttu-id="3fe1a-416">Este link envia a ID do instrutor selecionado para o método `Index` e define uma cor da tela de fundo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="3fe1a-417">Execute o aplicativo e selecione a guia **instrutores** . A página exibe o `Location` (Office) da entidade relacionada `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="3fe1a-418">Se OfficeAssignment é nulo, uma célula de tabela vazia é exibida.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="3fe1a-419">Clique no link **Selecionar** .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-419">Click on the **Select** link.</span></span> <span data-ttu-id="3fe1a-420">O estilo de linha é alterado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="3fe1a-421">Adicionar cursos ministrados pelo instrutor selecionado</span><span class="sxs-lookup"><span data-stu-id="3fe1a-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="3fe1a-422">Atualize o método `OnGetAsync` em *Pages/Instructors/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="3fe1a-423">Adicione `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="3fe1a-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="3fe1a-424">Examine a consulta atualizada:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="3fe1a-425">A consulta anterior adiciona as entidades `Department`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="3fe1a-426">O código a seguir é executado quando o instrutor é selecionado (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="3fe1a-427">O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="3fe1a-428">Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades `Course` da propriedade de navegação `CourseAssignments` desse instrutor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="3fe1a-429">O método `Where` retorna uma coleção.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="3fe1a-430">No método `Where` anterior, uma única entidade `Instructor` é retornada.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="3fe1a-431">O método `Single` converte a coleção em uma única entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="3fe1a-432">A entidade `Instructor` fornece acesso à propriedade `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="3fe1a-433">`CourseAssignments` fornece acesso às entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="3fe1a-435">O método `Single` é usado em uma coleção quando a coleção tem apenas um item.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="3fe1a-436">O método `Single` gera uma exceção se a coleção está vazia ou se há mais de um item.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="3fe1a-437">Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="3fe1a-438">O uso de `SingleOrDefault` é uma coleção vazia:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="3fe1a-439">Resulta em uma exceção (da tentativa de encontrar uma propriedade `Courses` em uma referência nula).</span><span class="sxs-lookup"><span data-stu-id="3fe1a-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="3fe1a-440">A mensagem de exceção indica menos claramente a causa do problema.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="3fe1a-441">O seguinte código popula a propriedade `Enrollments` do modelo de exibição quando um curso é selecionado:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="3fe1a-442">Adicione a seguinte marcação ao final da página *páginas/instrutores/index. cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="3fe1a-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="3fe1a-443">A marcação anterior exibe uma lista de cursos relacionados a um instrutor quando um instrutor é selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="3fe1a-444">Testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-444">Test the app.</span></span> <span data-ttu-id="3fe1a-445">Clique em um link **Selecionar** na página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="3fe1a-446">Mostrar dados de alunos</span><span class="sxs-lookup"><span data-stu-id="3fe1a-446">Show student data</span></span>

<span data-ttu-id="3fe1a-447">Nesta seção, o aplicativo é atualizado para mostrar os dados de alunos de um curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="3fe1a-448">Atualize a consulta no método `OnGetAsync` em *Pages/Instructors/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="3fe1a-449">Atualize *Pages/Instructors/Index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3fe1a-449">Update *Pages/Instructors/Index.cshtml* .</span></span> <span data-ttu-id="3fe1a-450">Adicione a seguinte marcação ao final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="3fe1a-451">A marcação anterior exibe uma lista dos alunos registrados no curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="3fe1a-452">Atualize a página e selecione um instrutor.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="3fe1a-453">Selecione um curso para ver a lista de alunos registrados e suas notas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-453">Select a course to see the list of enrolled students and their grades.</span></span>

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="3fe1a-455">Usando Single</span><span class="sxs-lookup"><span data-stu-id="3fe1a-455">Using Single</span></span>

<span data-ttu-id="3fe1a-456">O método `Single` pode passar a condição `Where` em vez de chamar o método `Where` separadamente:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="3fe1a-457">A abordagem `Single` anterior não oferece nenhum benefício em relação ao uso de `Where`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="3fe1a-458">Alguns desenvolvedores preferem o estilo de abordagem `Single`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="3fe1a-459">Carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="3fe1a-459">Explicit loading</span></span>

<span data-ttu-id="3fe1a-460">O código atual especifica o carregamento adiantado para `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="3fe1a-461">Suponha que os usuários raramente desejem ver registros em um curso.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="3fe1a-462">Nesse caso, uma otimização será carregar apenas os dados de registro se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="3fe1a-463">Nesta seção, o `OnGetAsync` é atualizado para usar o carregamento explícito de `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="3fe1a-464">Atualize o `OnGetAsync` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="3fe1a-465">O código anterior remove as chamadas do método *ThenInclude* para dados de registro e de alunos.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="3fe1a-466">Se um curso é selecionado, o código realçado recupera:</span><span class="sxs-lookup"><span data-stu-id="3fe1a-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="3fe1a-467">As entidades `Enrollment` para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="3fe1a-468">As entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="3fe1a-469">Observe que o código anterior comenta `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="3fe1a-470">As propriedades de navegação apenas podem ser carregadas de forma explícita para entidades controladas.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="3fe1a-471">Testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-471">Test the app.</span></span> <span data-ttu-id="3fe1a-472">De uma perspectiva dos usuários, o aplicativo se comporta de forma idêntica à versão anterior.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="3fe1a-473">O próximo tutorial mostra como atualizar os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="3fe1a-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3fe1a-474">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3fe1a-474">Additional resources</span></span>

* [<span data-ttu-id="3fe1a-475">Versão do YouTube deste tutorial (parte 1)</span><span class="sxs-lookup"><span data-stu-id="3fe1a-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="3fe1a-476">Versão do YouTube deste tutorial (parte 2)</span><span class="sxs-lookup"><span data-stu-id="3fe1a-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="3fe1a-477">[Anterior](xref:data/ef-rp/complex-data-model) 
> [Avançar](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="3fe1a-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
