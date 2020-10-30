---
title: Parte 7, Razor páginas com EF Core no ASP.NET Core dados relacionados à atualização
author: rick-anderson
description: Parte 7 de Razor páginas e Entity Framework série de tutoriais.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 3ec88a862697c540a1a98e733c31d76922f81f7c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060528"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a>Parte 7, Razor páginas com EF Core no ASP.NET Core dados relacionados à atualização

Por [Tom Dykstra](https://github.com/tdykstra)e [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

O tutorial mostra como atualizar os dados relacionados. As ilustrações a seguir mostram algumas das páginas concluídas.

![Página Editar Curso](update-related-data/_static/course-edit30.png)
![Página Editar Instrutor](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Atualizar o curso criar e editar páginas

O código com scaffold das páginas Criar e Editar do Curso tem uma lista suspensa Departamento que mostra a ID do Departamento (um número inteiro). A lista suspensa deve mostrar o nome do Departamento, portanto, ambas as páginas precisam de uma lista de nomes de departamento. Para fornecer essa lista, use uma classe base para as páginas Criar e Editar.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Criar uma classe base para Criar e Editar o Curso

Crie um arquivo *Pages/Courses/DepartmentNamePageModel.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

O código anterior cria uma [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) para conter a lista de nomes de departamentos. Se `selectedDepartment` for especificado, esse departamento estará selecionado na `SelectList`.

As classes de modelo da página Criar e Editar serão derivadas de `DepartmentNamePageModel`.

### <a name="update-the-course-create-page-model"></a>Atualizar o modelo de página Criar do Curso

Um Curso é atribuído a um Departamento. A classe base para as páginas Criar e Editar fornece um `SelectList` para selecionar o departamento. A lista suspensa que usa `SelectList` define a propriedade de FK (chave estrangeira) `Course.DepartmentID`. O EF Core usa a `Course.DepartmentID` FK para carregar a propriedade de navegação `Department`.

![Criar curso](update-related-data/_static/ddl30.png)

Atualize *Pages/Courses/Create.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

O código anterior:

* Deriva de `DepartmentNamePageModel`.
* Usa `TryUpdateModelAsync` para impedir o [excesso de postagem](xref:data/ef-rp/crud#overposting).
* Remove `ViewData["DepartmentID"]`. `DepartmentNameSL` da classe base é um modelo fortemente tipado e será usado pela Razor página. Modelos fortemente tipados são preferíveis aos fracamente tipados. Para obter mais informações, consulte [Dados fracamente tipados (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-no-locrazor-page"></a>Atualizar a página Criar curso Razor

Atualize *Pages/Courses/Create.cshtml* com o seguinte código:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

O código anterior faz as seguintes alterações:

* Altera a legenda de **DepartmentID** para **Departamento** .
* Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).
* Adiciona a opção "Selecionar Departamento". Essa alteração renderiza "Selecionar Departamento" na lista suspensa quando nenhum departamento foi selecionado ainda, em vez do primeiro departamento.
* Adiciona uma mensagem de validação quando o departamento não está selecionado.

A Razor página usa o [auxiliar selecionar marca](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Teste a página Criar. A página Criar exibe o nome do departamento em vez de a ID do departamento.

### <a name="update-the-course-edit-page-model"></a>Atualizar o modelo da página Editar do Curso

Atualiza *Pages/Courses/Edit.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

As alterações são semelhantes às feitas no modelo da página Criar. No código anterior, `PopulateDepartmentsDropDownList` passa a ID do departamento, que seleciona o departamento na lista suspensa.

### <a name="update-the-course-edit-no-locrazor-page"></a>Atualizar a página de edição do curso Razor

Atualize *Pages/Courses/Edit.cshtml* com o seguinte código:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

O código anterior faz as seguintes alterações:

* Exibe a ID do curso. Geralmente, a PK (chave primária) de uma entidade não é exibida. Em geral, PKs não têm sentido para os usuários. Nesse caso, o PK é o número do curso.
* Altera a legenda da lista suspensa Departamento de **DepartmentID** para **Departamento** .
* Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).

A página contém um campo oculto (`<input type="hidden">`) para o número do curso. A adição de um auxiliar de marcação `<label>` com `asp-for="Course.CourseID"` não elimina a necessidade do campo oculto. `<input type="hidden">` é necessário para que o número seja incluído nos dados postados quando o usuário clicar em **Salvar** .

## <a name="update-the-course-details-and-delete-pages"></a>Atualizar as páginas Excluir e Detalhes do Curso

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) pode melhorar o desempenho quando o acompanhamento não é necessário.

### <a name="update-the-course-page-models"></a>Atualizar os modelos de página do Curso

Atualize *Pages/Courses/Delete.cshtml* com o seguinte código para adicionar `AsNoTracking`:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Faça a mesma alteração no arquivo *Pages/Courses/details.cshtml.cs* :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a>Atualizar as páginas do curso Razor

Atualize *Pages/Courses/Delete.cshtml* com o seguinte código:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Faça as mesmas alterações na página Detalhes.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Testar as páginas Curso

Teste as páginas criar, editar, detalhes e excluir.

## <a name="update-the-instructor-create-and-edit-pages"></a>Atualizar as páginas Criar e Editar do instrutor

Os instrutores podem ministrar a quantidade de cursos que desejarem. A imagem a seguir mostra a página Editar do instrutor com uma matriz de caixas de seleção do curso.

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses30.png)

As caixas de seleção permitem alterações em cursos aos quais um instrutor é atribuído. Uma caixa de seleção é exibida para cada curso no banco de dados. Os cursos aos quais o instrutor é atribuído são selecionados. O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso. Se o número de cursos fosse muito maior, uma interface do usuário diferente poderia funcionar melhor. Porém, o método de gerenciar uma relação muitos para muitos mostrada aqui não mudaria. Para criar ou excluir relacionamentos, você manipula uma entidade de junção.

### <a name="create-a-class-for-assigned-courses-data"></a>Criar uma classe para dados de cursos atribuídos

Crie *SchoolViewModels/AssignedCourseData.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

A classe `AssignedCourseData` contém dados para criar as caixas de seleção para os cursos atribuídos a um instrutor.

### <a name="create-an-instructor-page-model-base-class"></a>Criar uma classe base de modelo de página do Instrutor

Crie a classe base *Pages/Instructors/InstructorCoursesPageModel.cs* :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

A `InstructorCoursesPageModel` é a classe base que será usada para os modelos de página Editar e Criar. `PopulateAssignedCourseData` lê todas as entidades `Course` para popular `AssignedCourseDataList`. Para cada curso, o código define a `CourseID`, o título e se o instrutor está ou não atribuído ao curso. Um [HashSet](/dotnet/api/system.collections.generic.hashset-1) é usado para pesquisas eficientes.

Como a Razor página não tem uma coleção de entidades de curso, o associador de modelo não pode atualizar automaticamente a `CourseAssignments` propriedade de navegação. Em vez de usar o associador de modelos para atualizar a propriedade de navegação `CourseAssignments`, faça isso no novo método `UpdateInstructorCourses`. Portanto, você precisa excluir a propriedade `CourseAssignments` do model binding. Isso não requer nenhuma alteração no código que chama `TryUpdateModel` porque você está usando a sobrecarga com propriedades declaradas e `CourseAssignments` não está na lista de inclusões.

Se nenhuma caixa de seleção foi marcada, o código em `UpdateInstructorCourses` inicializa a propriedade de navegação `CourseAssignments` com uma coleção vazia e retorna:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Em seguida, o código executa um loop em todos os cursos no banco de dados e verifica cada curso em relação àqueles atribuídos no momento ao instrutor e em relação àqueles que foram selecionados na página. Para facilitar pesquisas eficientes, as últimas duas coleções são armazenadas em objetos `HashSet`.

Se a caixa de seleção para um curso foi marcada, mas o curso não está na propriedade de navegação `Instructor.CourseAssignments`, o curso é adicionado à coleção na propriedade de navegação.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Se a caixa de seleção para um curso não foi marcada, mas o curso está na propriedade de navegação `Instructor.CourseAssignments`, o curso é removido da propriedade de navegação.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Processar o local do escritório

Outra relação que a página de edição precisa tratar é a relação de um para zero ou um que a entidade Instrutor tem com a entidade `OfficeAssignment`. O código de edição do instrutor deve lidar com os seguintes cenários: 

* Se o usuário limpar a atribuição de escritório, exclua a entidade `OfficeAssignment`.
* Se o usuário inserir uma atribuição de escritório e ela estava vazia, crie uma nova entidade `OfficeAssignment`.
* Se o usuário alterar a atribuição de escritório, atualize a entidade `OfficeAssignment`.

### <a name="update-the-instructor-edit-page-model"></a>Atualizar o modelo de página Editar do Instrutor

Atualize *Pages/Instructors/Edit.cshtml.cs* com o código a seguir:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

O código anterior:

* Obtém a entidade `Instructor` atual do banco de dados usando o carregamento adiantado para as propriedades de navegação `OfficeAssignment`, `CourseAssignment` e `CourseAssignment.Course`.
* Atualiza a entidade `Instructor` recuperada com valores do associador de modelos. `TryUpdateModel` impede o [excesso de postagem](xref:data/ef-rp/crud#overposting).
* Se o local do escritório estiver em branco, `Instructor.OfficeAssignment` será definido como nulo. Quando `Instructor.OfficeAssignment` é nulo, a linha relacionada na tabela `OfficeAssignment` é excluída.
* Chama `PopulateAssignedCourseData` em `OnGetAsync` para fornecer informações para as caixas de seleção usando a classe do modelo de exibição `AssignedCourseData`.
* Chama `UpdateInstructorCourses` em `OnPostAsync` para aplicar informações das caixas de seleção à entidade do instrutor que está sendo editada.
* Chamará `PopulateAssignedCourseData` e `UpdateInstructorCourses` em `OnPostAsync` se `TryUpdateModel` falhar. Essas chamadas de método restauram os dados de curso atribuídos inseridos na página quando são exibidos novamente com uma mensagem de erro.

### <a name="update-the-instructor-edit-no-locrazor-page"></a>Atualizar a página de edição do instrutor Razor

Atualize *Pages/Instructors/Edit.cshtml* com o código a seguir:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Esse código anterior cria uma tabela HTML que contém três colunas. Cada coluna tem uma caixa de seleção e uma legenda que contém o número e o título do curso. Todas as caixas de seleção têm o mesmo nome ("selectedCourses"). O uso do mesmo nome instrui o associador de modelos a tratá-las como um grupo. O atributo de valor de cada caixa de seleção é definido como `CourseID`. Quando a página é postada, o associador de modelos passa uma matriz que consiste nos valores `CourseID` para apenas as caixas de seleção marcadas.

Quando as caixas de seleção são inicialmente renderizadas, os cursos atribuídos ao instrutor são selecionados.

Observação: a abordagem usada aqui para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos. Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são mais utilizáveis e eficientes.

Execute o aplicativo e teste a página Editar de Instrutores atualizada. Altere algumas atribuições de curso. As alterações são refletidas na página Índice.

### <a name="update-the-instructor-create-page"></a>Atualizar a página Criar do Instrutor

Atualize o modelo e a página do instrutor criar página Razor com um código semelhante à página Editar:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Teste a página Criar Instrutor.

## <a name="update-the-instructor-delete-page"></a>Atualizar a página Excluir do Instrutor

Atualize *Pages/Instructors/Delete.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

O código anterior faz as seguintes alterações:

* Usa o carregamento adiantado para a propriedade de navegação `CourseAssignments`. `CourseAssignments` deve ser incluído ou eles não são excluídos quando o instrutor é excluído. Para evitar a necessidade de lê-las, configure a exclusão em cascata no banco de dados.

* Se o instrutor a ser excluído é atribuído como administrador de qualquer departamento, remove a atribuição de instrutor desse departamento.

Execute o aplicativo e teste a página Excluir.

## <a name="next-steps"></a>Próximas etapas

> [!div class="step-by-step"]
> [Tutorial anterior](xref:data/ef-rp/read-related-data) 
>  [Próximo tutorial](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este tutorial demonstra como atualizar dados relacionados. Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instruções de download](xref:index#how-to-download-a-sample).

As ilustrações a seguir mostram algumas das páginas concluídas.

![Página Editar Curso](update-related-data/_static/course-edit.png)
![Página Editar Instrutor](update-related-data/_static/instructor-edit-courses.png)

Examine e teste as páginas de cursos Criar e Editar. Crie um novo curso. O departamento é selecionado por sua chave primária (um inteiro), não pelo nome. Edite o novo curso. Quando concluir o teste, exclua o novo curso.

## <a name="create-a-base-class-to-share-common-code"></a>Criar uma classe base para compartilhar um código comum

As páginas Cursos/Criar e Cursos/Editar precisam de uma lista de nomes de departamentos. Crie a classe base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* para as páginas Criar e Editar:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

O código anterior cria uma [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) para conter a lista de nomes de departamentos. Se `selectedDepartment` for especificado, esse departamento estará selecionado na `SelectList`.

As classes de modelo da página Criar e Editar serão derivadas de `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Personalizar as páginas Courses

Quando uma nova entidade de curso é criada, ela precisa ter uma relação com um departamento existente. Para adicionar um departamento durante a criação de um curso, a classe base para Create e Edit contém uma lista suspensa para seleção do departamento. A lista suspensa define a propriedade `Course.DepartmentID` de FK (chave estrangeira). O EF Core usa a `Course.DepartmentID` FK para carregar a propriedade de navegação `Department`.

![Criar curso](update-related-data/_static/ddl.png)

Atualize o modelo da página Criar com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

O código anterior:

* Deriva de `DepartmentNamePageModel`.
* Usa `TryUpdateModelAsync` para impedir o [excesso de postagem](xref:data/ef-rp/crud#overposting).
* Substitui `ViewData["DepartmentID"]` por `DepartmentNameSL` (da classe base).

`ViewData["DepartmentID"]` é substituído pelo `DepartmentNameSL` fortemente tipado. Modelos fortemente tipados são preferíveis aos fracamente tipados. Para obter mais informações, consulte [Dados fracamente tipados (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Atualizar a página Criar Cursos

Atualize *Pages/Courses/Create.cshtml* com o seguinte código:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

A marcação anterior faz as seguintes alterações:

* Altera a legenda de **DepartmentID** para **Departamento** .
* Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).
* Adiciona a opção "Selecionar Departamento". Essa alteração renderiza "Selecionar Departamento", em vez do departamento primeiro.
* Adiciona uma mensagem de validação quando o departamento não está selecionado.

A Razor página usa o [auxiliar selecionar marca](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Teste a página Criar. A página Criar exibe o nome do departamento em vez de a ID do departamento.

### <a name="update-the-courses-edit-page"></a>Atualize a página Editar Cursos.

Substitua o código em *Pages/Courses/Edit.cshtml.cs* pelo seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

As alterações são semelhantes às feitas no modelo da página Criar. No código anterior, `PopulateDepartmentsDropDownList` passa a ID do departamento, que seleciona o departamento especificado na lista suspensa.

Atualize *Pages/Courses/Edit.cshtml* com a seguinte marcação:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

A marcação anterior faz as seguintes alterações:

* Exibe a ID do curso. Geralmente, a PK (chave primária) de uma entidade não é exibida. Em geral, PKs não têm sentido para os usuários. Nesse caso, o PK é o número do curso.
* Altera a legenda de **DepartmentID** para **Departamento** .
* Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).

A página contém um campo oculto (`<input type="hidden">`) para o número do curso. A adição de um auxiliar de marcação `<label>` com `asp-for="Course.CourseID"` não elimina a necessidade do campo oculto. `<input type="hidden">` é necessário para que o número seja incluído nos dados postados quando o usuário clicar em **Salvar** .

Teste o código atualizado. Crie, edite e exclua um curso.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Adicionar AsNoTracking aos modelos de página Detalhes e Excluir

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) pode melhorar o desempenho quando o acompanhamento não é necessário. Adicione `AsNoTracking` ao modelo de página Excluir e Detalhes. O seguinte código mostra o modelo de página Excluir atualizado:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Atualize o método `OnGetAsync` no arquivo *Pages/Courses/Details.cshtml.cs* :

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Modificar as páginas Excluir e Detalhes

Atualize a Razor página excluir com a seguinte marcação:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Faça as mesmas alterações na página Detalhes.

### <a name="test-the-course-pages"></a>Testar as páginas Curso

Teste as páginas Criar, Editar, Detalhes e Excluir.

## <a name="update-the-instructor-pages"></a>Atualizar as páginas do instrutor

As seções a seguir atualizam as páginas do instrutor.

### <a name="add-office-location"></a>Adicionar local do escritório

Ao editar um registro de instrutor, é recomendável atualizar a atribuição de escritório do instrutor. A entidade `Instructor` tem uma relação um para zero ou um com a entidade `OfficeAssignment`. O código do instrutor precisa manipular:

* Se o usuário limpar a atribuição de escritório, exclua a entidade `OfficeAssignment`.
* Se o usuário inserir uma atribuição de escritório e ela estava vazia, crie uma nova entidade `OfficeAssignment`.
* Se o usuário alterar a atribuição de escritório, atualize a entidade `OfficeAssignment`.

Atualize o modelo de página Editar Instrutores com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

O código anterior:

* Obtém a entidade `Instructor` atual do banco de dados usando o carregamento adiantado para a propriedade de navegação `OfficeAssignment`.
* Atualiza a entidade `Instructor` recuperada com valores do associador de modelos. `TryUpdateModel` impede o [excesso de postagem](xref:data/ef-rp/crud#overposting).
* Se o local do escritório estiver em branco, `Instructor.OfficeAssignment` será definido como nulo. Quando `Instructor.OfficeAssignment` é nulo, a linha relacionada na tabela `OfficeAssignment` é excluída.

### <a name="update-the-instructor-edit-page"></a>Atualizar a página Editar Instrutor

Atualize *Pages/Instructors/Edit.cshtml* com o local do escritório:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Verifique se você pode alterar o local do escritório de um instrutor.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Adicionar atribuições de Curso à página Editar Instrutor

Os instrutores podem ministrar a quantidade de cursos que desejarem. Nesta seção, você adiciona a capacidade de alterar as atribuições de curso. A seguinte imagem mostra a página Editar Instrutor atualizada:

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses.png)

`Course` e `Instructor` têm uma relação muitos para muitos. Para adicionar e remover relações, adicione e remova entidades do conjunto de entidades de junção `CourseAssignments`.

As caixas de seleção permitem alterações em cursos aos quais um instrutor é atribuído. Uma caixa de seleção é exibida para cada curso no banco de dados. Os cursos aos quais o instrutor é atribuído estão marcados. O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso. Se a quantidade de cursos for muito maior:

* Provavelmente, você usará outra interface do usuário para exibir os cursos.
* O método de manipulação de uma entidade de junção para criar ou excluir relações não será alterado.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Adicionar classes para dar suporte às páginas Criar e Editar Instrutor

Crie *SchoolViewModels/AssignedCourseData.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

A classe `AssignedCourseData` contém dados para criar as caixas de seleção para os cursos atribuídos por um instrutor.

Crie a classe base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* :

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

A `InstructorCoursesPageModel` é a classe base que será usada para os modelos de página Editar e Criar. `PopulateAssignedCourseData` lê todas as entidades `Course` para popular `AssignedCourseDataList`. Para cada curso, o código define a `CourseID`, o título e se o instrutor está ou não atribuído ao curso. Um [HashSet](/dotnet/api/system.collections.generic.hashset-1) é usado para criar pesquisas eficientes.

### <a name="instructors-edit-page-model"></a>Modelo de página Editar Instrutor

Atualize o modelo de página Editar Instrutor com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

O código anterior manipula as alterações de atribuição de escritório.

Atualize a exibição do instrutor Razor :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Quando você cola o código no Visual Studio, as quebras de linha são alteradas de uma forma que divide o código. Pressione Ctrl+Z uma vez para desfazer a formatação automática. A tecla de atalho Ctrl+Z corrige as quebras de linha para que elas se pareçam com o que você vê aqui. O recuo não precisa ser perfeito, mas cada uma das linhas `@:</tr><tr>`, `@:<td>`, `@:</td>` e `@:</tr>` precisa estar em uma única linha, conforme mostrado. Com o bloco de novo código selecionado, pressione Tab três vezes para alinhar o novo código com o código existente. Vote ou examine o status deste bug [com este link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Esse código anterior cria uma tabela HTML que contém três colunas. Cada coluna tem uma caixa de seleção e uma legenda que contém o número e o título do curso. Todas as caixas de seleção têm o mesmo nome ("selectedCourses"). O uso do mesmo nome instrui o associador de modelos a tratá-las como um grupo. O atributo de valor de cada caixa de seleção é definido como `CourseID`. Quando a página é postada, o associador de modelos passa uma matriz que consiste nos valores `CourseID` para apenas as caixas de seleção marcadas.

Quando as caixas de seleção são inicialmente renderizadas, os cursos atribuídos ao instrutor têm atributos marcados.

Execute o aplicativo e teste a página Editar Instrutor atualizada. Altere algumas atribuições de curso. As alterações são refletidas na página Índice.

Observação: a abordagem usada aqui para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos. Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são mais utilizáveis e eficientes.

### <a name="update-the-instructors-create-page"></a>Atualizar a página Criar Instrutor

Atualize o modelo de página Criar instrutor com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

O código anterior é semelhante ao código de *Pages/Instructors/Edit.cshtml.cs* .

Atualize a página Criar do instrutor Razor com a seguinte marcação:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Teste a página Criar Instrutor.

## <a name="update-the-delete-page"></a>Atualizar a página Excluir

Atualize o modelo da página Excluir com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

O código anterior faz as seguintes alterações:

* Usa o carregamento adiantado para a propriedade de navegação `CourseAssignments`. `CourseAssignments` deve ser incluído ou eles não são excluídos quando o instrutor é excluído. Para evitar a necessidade de lê-las, configure a exclusão em cascata no banco de dados.

* Se o instrutor a ser excluído é atribuído como administrador de qualquer departamento, remove a atribuição de instrutor desse departamento.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial (Parte 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Versão do YouTube deste tutorial (Parte 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/read-related-data) 
>  [Avançar](xref:data/ef-rp/concurrency)

::: moniker-end
