---
title: "ASP.NET Core MVC com o EF Core – atualizar dados relacionados – 7 de 10"
author: tdykstra
description: "Neste tutorial, você atualizará dados relacionados pela atualização dos campos de chave estrangeira e das propriedades de navegação."
manager: wpickett
ms.author: tdykstra
ms.date: 03/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 4085ca9340291f6ab594285360f3b65738699098
ms.sourcegitcommit: 18d1dc86770f2e272d93c7e1cddfc095c5995d9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2018
---
# <a name="updating-related-data---ef-core-with-aspnet-core-mvc-tutorial-7-of-10"></a><span data-ttu-id="3568b-103">Atualizando dados relacionados – tutorial do EF Core com o ASP.NET Core MVC (7 de 10)</span><span class="sxs-lookup"><span data-stu-id="3568b-103">Updating related data - EF Core with ASP.NET Core MVC tutorial (7 of 10)</span></span>

<span data-ttu-id="3568b-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3568b-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3568b-105">O aplicativo Web de exemplo Contoso University demonstra como criar aplicativos Web ASP.NET Core MVC usando o Entity Framework Core e o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3568b-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="3568b-106">Para obter informações sobre a série de tutoriais, consulte [o primeiro tutorial da série](intro.md).</span><span class="sxs-lookup"><span data-stu-id="3568b-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="3568b-107">No tutorial anterior, você exibiu dados relacionados; neste tutorial, você atualizará dados relacionados pela atualização dos campos de chave estrangeira e das propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="3568b-107">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="3568b-108">As ilustrações a seguir mostram algumas das páginas com as quais você trabalhará.</span><span class="sxs-lookup"><span data-stu-id="3568b-108">The following illustrations show some of the pages that you'll work with.</span></span>

![Página Editar Curso](update-related-data/_static/course-edit.png)

![Página Editar Instrutor](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a><span data-ttu-id="3568b-111">Personalizar as páginas Criar e Editar dos cursos</span><span class="sxs-lookup"><span data-stu-id="3568b-111">Customize the Create and Edit Pages for Courses</span></span>

<span data-ttu-id="3568b-112">Quando uma nova entidade de curso é criada, ela precisa ter uma relação com um departamento existente.</span><span class="sxs-lookup"><span data-stu-id="3568b-112">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="3568b-113">Para facilitar isso, o código gerado por scaffolding inclui métodos do controlador e exibições Criar e Editar que incluem uma lista suspensa para seleção do departamento.</span><span class="sxs-lookup"><span data-stu-id="3568b-113">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="3568b-114">A lista suspensa define a propriedade de chave estrangeira `Course.DepartmentID`, e isso é tudo o que o Entity Framework precisa para carregar a propriedade de navegação `Department` com a entidade Department apropriada.</span><span class="sxs-lookup"><span data-stu-id="3568b-114">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="3568b-115">Você usará o código gerado por scaffolding, mas o alterará ligeiramente para adicionar tratamento de erro e classificação à lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="3568b-115">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="3568b-116">Em *CoursesController.cs*, exclua os quatro métodos Create e Edit e substitua-os pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3568b-116">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="3568b-117">Após o método HttpPost `Edit`, crie um novo método que carrega informações de departamento para a lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="3568b-117">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="3568b-118">O método `PopulateDepartmentsDropDownList` obtém uma lista de todos os departamentos classificados por nome, cria uma coleção `SelectList` para uma lista suspensa e passa a coleção para a exibição em `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="3568b-118">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="3568b-119">O método aceita o parâmetro `selectedDepartment` opcional que permite que o código de chamada especifique o item que será selecionado quando a lista suspensa for renderizada.</span><span class="sxs-lookup"><span data-stu-id="3568b-119">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="3568b-120">A exibição passará o nome "DepartmentID" para o auxiliar de marcação `<select>` e o auxiliar então saberá que deve examinar o objeto `ViewBag` em busca de uma `SelectList` chamada "DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="3568b-120">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="3568b-121">O método HttpGet `Create` chama o método `PopulateDepartmentsDropDownList` sem definir o item selecionado, porque um novo curso do departamento ainda não foi estabelecido:</span><span class="sxs-lookup"><span data-stu-id="3568b-121">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="3568b-122">O método HttpGet `Edit` define o item selecionado, com base na ID do departamento já atribuído ao curso que está sendo editado:</span><span class="sxs-lookup"><span data-stu-id="3568b-122">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="3568b-123">Os métodos HttpPost para `Create` e `Edit` também incluem o código que define o item selecionado quando eles exibem novamente a página após um erro.</span><span class="sxs-lookup"><span data-stu-id="3568b-123">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="3568b-124">Isso garante que quando a página for exibida novamente para mostrar a mensagem de erro, qualquer que tenha sido o departamento selecionado permaneça selecionado.</span><span class="sxs-lookup"><span data-stu-id="3568b-124">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="3568b-125">Adicionar .AsNoTracking aos métodos Details e Delete</span><span class="sxs-lookup"><span data-stu-id="3568b-125">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="3568b-126">Para otimizar o desempenho das páginas Detalhes do Curso e Excluir, adicione chamadas `AsNoTracking` aos métodos HttpGet `Details` e `Delete`.</span><span class="sxs-lookup"><span data-stu-id="3568b-126">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="3568b-127">Modificar as exibições Curso</span><span class="sxs-lookup"><span data-stu-id="3568b-127">Modify the Course views</span></span>

<span data-ttu-id="3568b-128">Em *Views/Courses/Create.cshtml*, adicione uma opção "Selecionar Departamento" à lista suspensa **Departamento**, altere a legenda de **DepartmentID** para **Departamento** e adicione uma mensagem de validação.</span><span class="sxs-lookup"><span data-stu-id="3568b-128">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

<span data-ttu-id="3568b-129">Em *Views/Courses/Edit.cshtml*, faça a mesma alteração no campo Departamento que você acabou de fazer em *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3568b-129">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="3568b-130">Também em *Views/Courses/Edit.cshtml*, adicione um campo de número de curso antes do campo **Título**.</span><span class="sxs-lookup"><span data-stu-id="3568b-130">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="3568b-131">Como o número de curso é a chave primária, ele é exibido, mas não pode ser alterado.</span><span class="sxs-lookup"><span data-stu-id="3568b-131">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="3568b-132">Já existe um campo oculto (`<input type="hidden">`) para o número de curso na exibição Editar.</span><span class="sxs-lookup"><span data-stu-id="3568b-132">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="3568b-133">A adição de um auxiliar de marcação `<label>` não elimina a necessidade do campo oculto, porque ele não faz com que o número de curso seja incluído nos dados postados quando o usuário clica em **Salvar** na página **Editar**.</span><span class="sxs-lookup"><span data-stu-id="3568b-133">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="3568b-134">Em *Views/Courses/Delete.cshtml*, adicione um campo de número de curso na parte superior e altere a ID do departamento para o nome do departamento.</span><span class="sxs-lookup"><span data-stu-id="3568b-134">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="3568b-135">Em *Views/Courses/Details.cshtml*, faça a mesma alteração que você acabou de fazer para *Delete.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3568b-135">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="3568b-136">Testar as páginas Curso</span><span class="sxs-lookup"><span data-stu-id="3568b-136">Test the Course pages</span></span>

<span data-ttu-id="3568b-137">Execute o aplicativo, selecione a guia **Cursos**, clique em **Criar Novo** e insira dados para um novo curso:</span><span class="sxs-lookup"><span data-stu-id="3568b-137">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![Página Criar Curso](update-related-data/_static/course-create.png)

<span data-ttu-id="3568b-139">Clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3568b-139">Click **Create**.</span></span> <span data-ttu-id="3568b-140">A página Índice de Cursos é exibida com o novo curso adicionado à lista.</span><span class="sxs-lookup"><span data-stu-id="3568b-140">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="3568b-141">O nome do departamento na lista de páginas de Índice é obtido da propriedade de navegação, mostrando que a relação foi estabelecida corretamente.</span><span class="sxs-lookup"><span data-stu-id="3568b-141">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="3568b-142">Clique em **Editar** em um curso na página Índice de Cursos.</span><span class="sxs-lookup"><span data-stu-id="3568b-142">Click **Edit** on a course in the Courses Index page.</span></span>

![Página Editar Curso](update-related-data/_static/course-edit.png)

<span data-ttu-id="3568b-144">Altere dados na página e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="3568b-144">Change data on the page and click **Save**.</span></span> <span data-ttu-id="3568b-145">A página Índice de Cursos é exibida com os dados de cursos atualizados.</span><span class="sxs-lookup"><span data-stu-id="3568b-145">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-an-edit-page-for-instructors"></a><span data-ttu-id="3568b-146">Adicionar uma página Editar para instrutores</span><span class="sxs-lookup"><span data-stu-id="3568b-146">Add an Edit Page for Instructors</span></span>

<span data-ttu-id="3568b-147">Quando você edita um registro de instrutor, deseja poder atualizar a atribuição de escritório do instrutor.</span><span class="sxs-lookup"><span data-stu-id="3568b-147">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="3568b-148">A entidade Instructor tem uma relação um para zero ou um com a entidade OfficeAssignment, o que significa que o código deve manipular as seguintes situações:</span><span class="sxs-lookup"><span data-stu-id="3568b-148">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="3568b-149">Se o usuário apagar a atribuição de escritório e ela originalmente tinha um valor, exclua a entidade OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="3568b-149">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="3568b-150">Se o usuário inserir um valor de atribuição de escritório e ele originalmente estava vazio, crie uma nova entidade OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="3568b-150">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="3568b-151">Se o usuário alterar o valor de uma atribuição de escritório, altere o valor em uma entidade OfficeAssignment existente.</span><span class="sxs-lookup"><span data-stu-id="3568b-151">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="3568b-152">Atualizar o controlador Instrutores</span><span class="sxs-lookup"><span data-stu-id="3568b-152">Update the Instructors controller</span></span>

<span data-ttu-id="3568b-153">Em *InstructorsController.cs*, altere o código no método HttpGet `Edit` para que ele carregue a propriedade de navegação `OfficeAssignment` da entidade Instructor e chame `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="3568b-153">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

<span data-ttu-id="3568b-154">Substitua o método HttpPost `Edit` pelo seguinte código para manipular atualizações de atribuição de escritório:</span><span class="sxs-lookup"><span data-stu-id="3568b-154">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="3568b-155">O código faz o seguinte:</span><span class="sxs-lookup"><span data-stu-id="3568b-155">The code does the following:</span></span>

-  <span data-ttu-id="3568b-156">Altera o nome do método para `EditPost` porque a assinatura agora é a mesma do método HttpGet `Edit` (o atributo `ActionName` especifica que a URL `/Edit/` ainda é usada).</span><span class="sxs-lookup"><span data-stu-id="3568b-156">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

-  <span data-ttu-id="3568b-157">Obtém a entidade Instructor atual do banco de dados usando o carregamento adiantado para a propriedade de navegação `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="3568b-157">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="3568b-158">Isso é o mesmo que você fez no método HttpGet `Edit`.</span><span class="sxs-lookup"><span data-stu-id="3568b-158">This is the same as what you did in the HttpGet `Edit` method.</span></span>

-  <span data-ttu-id="3568b-159">Atualiza a entidade Instructor recuperada com valores do associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="3568b-159">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="3568b-160">A sobrecarga `TryUpdateModel` permite que você adicione à lista de permissões as propriedades que você deseja incluir.</span><span class="sxs-lookup"><span data-stu-id="3568b-160">The `TryUpdateModel` overload enables you to whitelist the properties you want to include.</span></span> <span data-ttu-id="3568b-161">Isso impede o excesso de postagem, conforme explicado no [segundo tutorial](crud.md).</span><span class="sxs-lookup"><span data-stu-id="3568b-161">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   <span data-ttu-id="3568b-162">Se o local do escritório estiver em branco, a propriedade Instructor.OfficeAssignment será definida como nula para que a linha relacionada na tabela OfficeAssignment seja excluída.</span><span class="sxs-lookup"><span data-stu-id="3568b-162">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- <span data-ttu-id="3568b-163">Salva as alterações no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3568b-163">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="3568b-164">Atualizar a exibição Editar Instrutor</span><span class="sxs-lookup"><span data-stu-id="3568b-164">Update the Instructor Edit view</span></span>

<span data-ttu-id="3568b-165">Em *Views/Instructors/Edit.cshtml*, adicione um novo campo para editar o local do escritório, ao final, antes do botão **Salvar**:</span><span class="sxs-lookup"><span data-stu-id="3568b-165">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="3568b-166">Execute o aplicativo, selecione a guia **Instrutores** e, em seguida, clique em **Editar** em um instrutor.</span><span class="sxs-lookup"><span data-stu-id="3568b-166">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="3568b-167">Altere o **Local do Escritório** e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="3568b-167">Change the **Office Location** and click **Save**.</span></span>

![Página Editar Instrutor](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="3568b-169">Adicionar atribuições de Curso à página Editar Instrutor</span><span class="sxs-lookup"><span data-stu-id="3568b-169">Add Course assignments to the Instructor Edit page</span></span>

<span data-ttu-id="3568b-170">Os instrutores podem ministrar a quantidade de cursos que desejarem.</span><span class="sxs-lookup"><span data-stu-id="3568b-170">Instructors may teach any number of courses.</span></span> <span data-ttu-id="3568b-171">Agora, você aprimorará a página Editar Instrutor adicionando a capacidade de alterar as atribuições de curso usando um grupo de caixas de seleção, conforme mostrado na seguinte captura de tela:</span><span class="sxs-lookup"><span data-stu-id="3568b-171">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="3568b-173">A relação entre as entidades Course e Instructor é muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="3568b-173">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="3568b-174">Para adicionar e remover relações, adicione e remova entidades bidirecionalmente no conjunto de entidades de junção CourseAssignments.</span><span class="sxs-lookup"><span data-stu-id="3568b-174">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="3568b-175">A interface do usuário que permite alterar a quais cursos um instrutor é atribuído é um grupo de caixas de seleção.</span><span class="sxs-lookup"><span data-stu-id="3568b-175">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="3568b-176">Uma caixa de seleção é exibida para cada curso no banco de dados, e aqueles aos quais o instrutor está atribuído no momento são marcados.</span><span class="sxs-lookup"><span data-stu-id="3568b-176">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="3568b-177">O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso.</span><span class="sxs-lookup"><span data-stu-id="3568b-177">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="3568b-178">Se a quantidade de cursos for muito maior, provavelmente, você desejará usar outro método de apresentação dos dados na exibição, mas usará o mesmo método de manipulação de uma entidade de junção para criar ou excluir relações.</span><span class="sxs-lookup"><span data-stu-id="3568b-178">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="3568b-179">Atualizar o controlador Instrutores</span><span class="sxs-lookup"><span data-stu-id="3568b-179">Update the Instructors controller</span></span>

<span data-ttu-id="3568b-180">Para fornecer dados à exibição para a lista de caixas de seleção, você usará uma classe de modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="3568b-180">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="3568b-181">Crie *AssignedCourseData.cs* na pasta *SchoolViewModels* e substitua o código existente pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3568b-181">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="3568b-182">Em *InstructorsController.cs*, substitua o método HttpGet `Edit` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3568b-182">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="3568b-183">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="3568b-183">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="3568b-184">O código adiciona o carregamento adiantado à propriedade de navegação `Courses` e chama o novo método `PopulateAssignedCourseData` para fornecer informações para a matriz de caixa de seleção usando a classe de modelo de exibição `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="3568b-184">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="3568b-185">O código no método `PopulateAssignedCourseData` lê todas as entidades Course para carregar uma lista de cursos usando a classe de modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="3568b-185">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="3568b-186">Para cada curso, o código verifica se o curso existe na propriedade de navegação `Courses` do instrutor.</span><span class="sxs-lookup"><span data-stu-id="3568b-186">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="3568b-187">Para criar uma pesquisa eficiente ao verificar se um curso é atribuído ao instrutor, os cursos atribuídos ao instrutor são colocados em uma coleção `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="3568b-187">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="3568b-188">A propriedade `Assigned` está definida como verdadeiro para os cursos aos quais instrutor é atribuído.</span><span class="sxs-lookup"><span data-stu-id="3568b-188">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="3568b-189">A exibição usará essa propriedade para determinar quais caixas de seleção precisam ser exibidas como selecionadas.</span><span class="sxs-lookup"><span data-stu-id="3568b-189">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="3568b-190">Por fim, a lista é passada para a exibição em `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="3568b-190">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="3568b-191">Em seguida, adicione o código que é executado quando o usuário clica em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="3568b-191">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="3568b-192">Substitua o método `EditPost` pelo código a seguir e adicione um novo método que atualiza a propriedade de navegação `Courses` da entidade Instructor.</span><span class="sxs-lookup"><span data-stu-id="3568b-192">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="3568b-193">A assinatura do método agora é diferente do método HttpGet `Edit` e, portanto, o nome do método é alterado de `EditPost` para `Edit` novamente.</span><span class="sxs-lookup"><span data-stu-id="3568b-193">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="3568b-194">Como a exibição não tem uma coleção de entidades Course, o associador de modelos não pode atualizar automaticamente a propriedade de navegação `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="3568b-194">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="3568b-195">Em vez de usar o associador de modelos para atualizar a propriedade de navegação `CourseAssignments`, faça isso no novo método `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="3568b-195">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="3568b-196">Portanto, você precisa excluir a propriedade `CourseAssignments` da associação de modelos.</span><span class="sxs-lookup"><span data-stu-id="3568b-196">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="3568b-197">Isso não exige nenhuma alteração no código que chama `TryUpdateModel`, porque você está usando a sobrecarga da lista de permissões e `CourseAssignments` não está na lista de inclusões.</span><span class="sxs-lookup"><span data-stu-id="3568b-197">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="3568b-198">Se nenhuma caixa de seleção foi marcada, o código em `UpdateInstructorCourses` inicializa a propriedade de navegação `CourseAssignments` com uma coleção vazia e retorna:</span><span class="sxs-lookup"><span data-stu-id="3568b-198">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="3568b-199">Em seguida, o código executa um loop em todos os cursos no banco de dados e verifica cada curso em relação àqueles atribuídos no momento ao instrutor e em relação àqueles que foram selecionados na exibição.</span><span class="sxs-lookup"><span data-stu-id="3568b-199">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="3568b-200">Para facilitar pesquisas eficientes, as últimas duas coleções são armazenadas em objetos `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="3568b-200">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="3568b-201">Se a caixa de seleção para um curso foi marcada, mas o curso não está na propriedade de navegação `Instructor.CourseAssignments`, o curso é adicionado à coleção na propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="3568b-201">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="3568b-202">Se a caixa de seleção para um curso não foi marcada, mas o curso está na propriedade de navegação `Instructor.CourseAssignments`, o curso é removido da propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="3568b-202">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="3568b-203">Atualizar as exibições Instrutor</span><span class="sxs-lookup"><span data-stu-id="3568b-203">Update the Instructor views</span></span>

<span data-ttu-id="3568b-204">Em *Views/Instructors/Edit.cshtml*, adicione um campo **Cursos** com uma matriz de caixas de seleção, adicionando o código a seguir imediatamente após os elementos `div` para o campo **Escritório** e antes do elemento `div` para o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="3568b-204">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE] 
> <span data-ttu-id="3568b-205">Quando você colar o código no Visual Studio, as quebras de linha serão alteradas de uma forma que divide o código.</span><span class="sxs-lookup"><span data-stu-id="3568b-205">When you paste the code in Visual Studio, line breaks will be changed in a way that breaks the code.</span></span>  <span data-ttu-id="3568b-206">Pressione Ctrl+Z uma vez para desfazer a formatação automática.</span><span class="sxs-lookup"><span data-stu-id="3568b-206">Press Ctrl+Z one time to undo the automatic formatting.</span></span>  <span data-ttu-id="3568b-207">Isso corrigirá as quebras de linha para que elas se pareçam com o que você vê aqui.</span><span class="sxs-lookup"><span data-stu-id="3568b-207">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="3568b-208">O recuo não precisa ser perfeito, mas cada uma das linhas `@</tr><tr>`, `@:<td>`, `@:</td>` e `@:</tr>` precisa estar em uma única linha, conforme mostrado, ou você receberá um erro de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="3568b-208">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="3568b-209">Com o bloco de novo código selecionado, pressione Tab três vezes para alinhar o novo código com o código existente.</span><span class="sxs-lookup"><span data-stu-id="3568b-209">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="3568b-210">Verifique o status deste problema [aqui](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="3568b-210">You can check the status of this problem [here](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="3568b-211">Esse código cria uma tabela HTML que contém três colunas.</span><span class="sxs-lookup"><span data-stu-id="3568b-211">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="3568b-212">Em cada coluna há uma caixa de seleção, seguida de uma legenda que consiste no número e título do curso.</span><span class="sxs-lookup"><span data-stu-id="3568b-212">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="3568b-213">Todas as caixas de seleção têm o mesmo nome ("selectedCourses"), o que informa ao associador de modelos de que elas devem ser tratadas como um grupo.</span><span class="sxs-lookup"><span data-stu-id="3568b-213">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="3568b-214">O atributo de valor de cada caixa de seleção é definido com o valor de `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="3568b-214">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="3568b-215">Quando a página é postada, o associador de modelos passa uma matriz para o controlador que consiste nos valores `CourseID` para apenas as caixas de seleção marcadas.</span><span class="sxs-lookup"><span data-stu-id="3568b-215">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="3568b-216">Quando as caixas de seleção são inicialmente renderizadas, aquelas que se destinam aos cursos atribuídos ao instrutor têm atributos marcados, que os seleciona (exibe-os como marcados).</span><span class="sxs-lookup"><span data-stu-id="3568b-216">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="3568b-217">Execute o aplicativo, selecione a guia **Instrutores** e clique em **Editar** em um instrutor para ver a página **Editar**.</span><span class="sxs-lookup"><span data-stu-id="3568b-217">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="3568b-219">Altere algumas atribuições de curso e clique em Salvar.</span><span class="sxs-lookup"><span data-stu-id="3568b-219">Change some course assignments and click Save.</span></span> <span data-ttu-id="3568b-220">As alterações feitas são refletidas na página Índice.</span><span class="sxs-lookup"><span data-stu-id="3568b-220">The changes you make are reflected on the Index page.</span></span>

> [!NOTE] 
> <span data-ttu-id="3568b-221">A abordagem usada aqui para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos.</span><span class="sxs-lookup"><span data-stu-id="3568b-221">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="3568b-222">Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são necessários.</span><span class="sxs-lookup"><span data-stu-id="3568b-222">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="3568b-223">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="3568b-223">Update the Delete page</span></span>

<span data-ttu-id="3568b-224">Em *InstructorsController.cs*, exclua o método `DeleteConfirmed` e insira o código a seguir em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="3568b-224">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="3568b-225">Este código faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="3568b-225">This code makes the following changes:</span></span>

* <span data-ttu-id="3568b-226">Executa o carregamento adiantado para a propriedade de navegação `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="3568b-226">Does eager loading for the `CourseAssignments` navigation property.</span></span>  <span data-ttu-id="3568b-227">Você precisa incluir isso ou o EF não reconhecerá as entidades `CourseAssignment` relacionadas e não as excluirá.</span><span class="sxs-lookup"><span data-stu-id="3568b-227">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span>  <span data-ttu-id="3568b-228">Para evitar a necessidade de lê-las aqui, você pode configurar a exclusão em cascata no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3568b-228">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="3568b-229">Se o instrutor a ser excluído é atribuído como administrador de qualquer departamento, remove a atribuição de instrutor desse departamento.</span><span class="sxs-lookup"><span data-stu-id="3568b-229">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-the-create-page"></a><span data-ttu-id="3568b-230">Adicionar local de escritório e cursos para a página Criar</span><span class="sxs-lookup"><span data-stu-id="3568b-230">Add office location and courses to the Create page</span></span>

<span data-ttu-id="3568b-231">Em *InstructorsController.cs*, exclua os métodos HttpGet e HttpPost `Create` e, em seguida, adicione o seguinte código em seu lugar:</span><span class="sxs-lookup"><span data-stu-id="3568b-231">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="3568b-232">Esse código é semelhante ao que você viu nos métodos `Edit`, exceto que inicialmente nenhum curso está selecionado.</span><span class="sxs-lookup"><span data-stu-id="3568b-232">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="3568b-233">O método HttpGet `Create` chama o método `PopulateAssignedCourseData`, não porque pode haver cursos selecionados, mas para fornecer uma coleção vazia para o loop `foreach` na exibição (caso contrário, o código de exibição gera uma exceção de referência nula).</span><span class="sxs-lookup"><span data-stu-id="3568b-233">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="3568b-234">O método HttpPost `Create` adiciona cada curso selecionado à propriedade de navegação `CourseAssignments` antes que ele verifica se há erros de validação e adiciona o novo instrutor ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3568b-234">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="3568b-235">Os cursos são adicionados, mesmo se há erros de modelo, de modo que quando houver erros de modelo (por exemplo, o usuário inseriu uma data inválida) e a página for exibida novamente com uma mensagem de erro, as seleções de cursos que foram feitas sejam restauradas automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3568b-235">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="3568b-236">Observe que para poder adicionar cursos à propriedade de navegação `CourseAssignments`, é necessário inicializar a propriedade como uma coleção vazia:</span><span class="sxs-lookup"><span data-stu-id="3568b-236">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="3568b-237">Como alternativa a fazer isso no código do controlador, faça isso no modelo Instrutor alterando o getter de propriedade para criar automaticamente a coleção se ela não existir, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="3568b-237">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

<span data-ttu-id="3568b-238">Se você modificar a propriedade `CourseAssignments` dessa forma, poderá remover o código de inicialização de propriedade explícita no controlador.</span><span class="sxs-lookup"><span data-stu-id="3568b-238">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="3568b-239">Em *Views/Instructor/Create.cshtml*, adicione uma caixa de texto de local do escritório e caixas de seleção para cursos antes do botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="3568b-239">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="3568b-240">Como no caso da página Editar, [corrija a formatação se o Visual Studio reformatar o código quando você o colar](#notepad).</span><span class="sxs-lookup"><span data-stu-id="3568b-240">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="3568b-241">Faça o teste executando o aplicativo e criando um instrutor.</span><span class="sxs-lookup"><span data-stu-id="3568b-241">Test by running the app and creating an instructor.</span></span> 

## <a name="handling-transactions"></a><span data-ttu-id="3568b-242">Manipulando transações</span><span class="sxs-lookup"><span data-stu-id="3568b-242">Handling Transactions</span></span>

<span data-ttu-id="3568b-243">Conforme explicado no [tutorial do CRUD](crud.md), o Entity Framework implementa transações de forma implícita.</span><span class="sxs-lookup"><span data-stu-id="3568b-243">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="3568b-244">Para cenários em que você precisa de mais controle – por exemplo, se desejar incluir operações feitas fora do Entity Framework em uma transação, consulte [Transações](https://docs.microsoft.com/ef/core/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="3568b-244">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](https://docs.microsoft.com/ef/core/saving/transactions).</span></span>

## <a name="summary"></a><span data-ttu-id="3568b-245">Resumo</span><span class="sxs-lookup"><span data-stu-id="3568b-245">Summary</span></span>

<span data-ttu-id="3568b-246">Agora você concluiu a introdução ao trabalho com os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="3568b-246">You have now completed the introduction to working with related data.</span></span> <span data-ttu-id="3568b-247">No próximo tutorial, você verá como lidar com conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="3568b-247">In the next tutorial you'll see how to handle concurrency conflicts.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="3568b-248">[Anterior](read-related-data.md)
[Próximo](concurrency.md)</span><span class="sxs-lookup"><span data-stu-id="3568b-248">[Previous](read-related-data.md)
[Next](concurrency.md)</span></span>  
