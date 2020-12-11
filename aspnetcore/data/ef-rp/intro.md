---
title: Razor Páginas com Entity Framework Core no ASP.NET Core-tutorial 1 de 8
author: rick-anderson
description: Mostra como criar um Razor aplicativo de páginas usando Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 86b57a9cad27673b72ad174a18741f5528f9f78a
ms.sourcegitcommit: c321518bfe367280ef262aecaada287f17fe1bc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011852"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="0437b-103">Razor Páginas com Entity Framework Core no ASP.NET Core-tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="0437b-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="0437b-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0437b-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0437b-105">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core Razor pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="0437b-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="0437b-106">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="0437b-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0437b-107">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="0437b-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0437b-108">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="0437b-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="0437b-109">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="0437b-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="0437b-110">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="0437b-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0437b-111">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0437b-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0437b-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0437b-112">Prerequisites</span></span>

* <span data-ttu-id="0437b-113">Se você for novo em Razor páginas, consulte a série de tutoriais [introdução às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta.</span><span class="sxs-lookup"><span data-stu-id="0437b-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="0437b-116">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-116">Database engines</span></span>

<span data-ttu-id="0437b-117">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="0437b-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="0437b-118">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="0437b-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="0437b-119">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="0437b-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0437b-120">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="0437b-120">Troubleshooting</span></span>

<span data-ttu-id="0437b-121">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0437b-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0437b-122">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0437b-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="0437b-123">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="0437b-123">The sample app</span></span>

<span data-ttu-id="0437b-124">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="0437b-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="0437b-125">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="0437b-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0437b-126">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0437b-126">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="0437b-129">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="0437b-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="0437b-130">O foco do tutorial é sobre como usar EF Core com ASP.NET Core, não como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0437b-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="0437b-131">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="0437b-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0437b-133">Inicie o Visual Studio e selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="0437b-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="0437b-134">Na caixa de diálogo **criar um novo projeto** , selecione **ASP.NET Core aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0437b-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="0437b-135">Na caixa de diálogo **configurar seu novo projeto** , digite `ContosoUniversity` para **nome do projeto**.</span><span class="sxs-lookup"><span data-stu-id="0437b-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="0437b-136">É importante usar esse nome exato, incluindo a capitalização, para que cada `namespace` correspondência seja feita quando o código for copiado.</span><span class="sxs-lookup"><span data-stu-id="0437b-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="0437b-137">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0437b-137">Select **Create**.</span></span>
1. <span data-ttu-id="0437b-138">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione:</span><span class="sxs-lookup"><span data-stu-id="0437b-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="0437b-139">**.NET Core** e **ASP.NET Core 5,0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="0437b-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="0437b-140">**ASP.NET Core aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="0437b-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="0437b-141"> 
       Criar ![ Caixa de diálogo novo projeto de ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="0437b-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0437b-143">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="0437b-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="0437b-144">Execute os seguintes comandos para criar um Razor projeto de páginas e `cd` para a nova pasta de projeto:</span><span class="sxs-lookup"><span data-stu-id="0437b-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0437b-145">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="0437b-145">Set up the site style</span></span>

<span data-ttu-id="0437b-146">Copie e cole o código a seguir no arquivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0437b-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="0437b-147">O arquivo de layout define o cabeçalho, o rodapé e o menu do site.</span><span class="sxs-lookup"><span data-stu-id="0437b-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="0437b-148">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="0437b-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="0437b-149">Cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="0437b-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0437b-150">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="0437b-150">There are three occurrences.</span></span>
* <span data-ttu-id="0437b-151">As entradas do menu **página inicial** e **privacidade** são excluídas.</span><span class="sxs-lookup"><span data-stu-id="0437b-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="0437b-152">As entradas são adicionadas para **about**, **estudantes**, **cursos**, **instrutores** e **departamentos**.</span><span class="sxs-lookup"><span data-stu-id="0437b-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="0437b-153">Em *pages/index. cshtml*, substitua o conteúdo do arquivo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0437b-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="0437b-154">O código anterior substitui o texto sobre ASP.NET Core por texto sobre este aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="0437b-155">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="0437b-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="0437b-156">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-156">The data model</span></span>

<span data-ttu-id="0437b-157">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="0437b-157">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="0437b-159">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="0437b-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="0437b-160">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="0437b-160">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="0437b-162">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="0437b-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="0437b-163">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="0437b-164">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="0437b-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0437b-165">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0437b-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0437b-166">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="0437b-167">Para obter mais informações, consulte [chaves de EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="0437b-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="0437b-168">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0437b-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0437b-169">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="0437b-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0437b-170">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="0437b-171">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="0437b-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="0437b-172">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="0437b-173">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="0437b-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="0437b-174">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="0437b-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="0437b-175">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="0437b-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="0437b-176">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="0437b-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="0437b-177">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="0437b-178">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="0437b-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="0437b-179">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="0437b-179">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="0437b-181">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="0437b-182">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="0437b-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0437b-183">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="0437b-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="0437b-184">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="0437b-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="0437b-185">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="0437b-186">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="0437b-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0437b-187">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="0437b-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="0437b-188">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="0437b-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0437b-189">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0437b-190">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="0437b-191">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="0437b-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0437b-192">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="0437b-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0437b-193">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0437b-194">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0437b-195">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0437b-196">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0437b-197">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="0437b-197">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="0437b-199">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="0437b-200">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="0437b-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0437b-201">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="0437b-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0437b-202">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="0437b-203">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="0437b-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="0437b-204">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="0437b-204">Scaffold Student pages</span></span>

<span data-ttu-id="0437b-205">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="0437b-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="0437b-206">Uma classe de EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0437b-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="0437b-207">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="0437b-208">Ele deriva da classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0437b-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="0437b-209">Razor páginas que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="0437b-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0437b-211">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="0437b-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="0437b-212">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Páginas/Alunos* e selecione **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="0437b-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0437b-213">Na caixa de diálogo **Adicionar novo item do Scaffold** :</span><span class="sxs-lookup"><span data-stu-id="0437b-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="0437b-214">Na guia à esquerda, selecione **instalado > Razor páginas > comuns**</span><span class="sxs-lookup"><span data-stu-id="0437b-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="0437b-215">Selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0437b-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="0437b-216">Na caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0437b-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="0437b-217">Na lista suspensa **classe Modelo**, selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="0437b-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="0437b-218">Na linha **Classe de contexto de dados**, selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="0437b-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="0437b-219">Altere o nome do contexto de dados para terminar em `SchoolContext` em vez de `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="0437b-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="0437b-220">O nome do contexto atualizado: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="0437b-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
    * <span data-ttu-id="0437b-221">Selecione **Adicionar** para concluir a adição da classe de contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-221">Select **Add** to finish adding the data context class.</span></span>
   * <span data-ttu-id="0437b-222">Selecione **Adicionar** para concluir a caixa de diálogo **Adicionar Razor páginas** .</span><span class="sxs-lookup"><span data-stu-id="0437b-222">Select **Add** to finish the **Add Razor Pages** dialog.</span></span>

<span data-ttu-id="0437b-223">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="0437b-223">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0437b-225">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="0437b-225">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="0437b-226">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0437b-226">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="0437b-227">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0437b-227">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="0437b-228">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="0437b-228">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="0437b-229">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0437b-229">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="0437b-230">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-230">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="0437b-231">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="0437b-231">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="0437b-232">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="0437b-232">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="0437b-233">Se a etapa anterior falhar, compile o projeto e tente a etapa Scaffold novamente.</span><span class="sxs-lookup"><span data-stu-id="0437b-233">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="0437b-234">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="0437b-234">The scaffolding process:</span></span>

* <span data-ttu-id="0437b-235">Cria Razor páginas na pasta *páginas/alunos* :</span><span class="sxs-lookup"><span data-stu-id="0437b-235">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="0437b-236">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-236">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="0437b-237">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-237">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="0437b-238">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-238">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="0437b-239">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-239">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="0437b-240">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-240">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="0437b-241">Cria *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="0437b-241">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="0437b-242">Adiciona o contexto à injeção de dependência em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0437b-242">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="0437b-243">Adiciona uma cadeia de conexão de banco de dados a *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="0437b-243">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="0437b-244">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-244">Database connection string</span></span>

<span data-ttu-id="0437b-245">A ferramenta scaffolding gera uma cadeia de conexão no *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0437b-245">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0437b-247">A cadeia de conexão especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="0437b-247">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="0437b-248">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="0437b-248">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0437b-249">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-249">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0437b-251">Encurte a cadeia de conexão do SQLite para *cu. db*:</span><span class="sxs-lookup"><span data-stu-id="0437b-251">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="0437b-252">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-252">Update the database context class</span></span>

<span data-ttu-id="0437b-253">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-253">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0437b-254">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0437b-254">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0437b-255">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-255">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0437b-256">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0437b-256">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0437b-257">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-257">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="0437b-258">O código anterior muda do singular `DbSet<Student> Student` para o plural `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="0437b-258">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="0437b-259">Para fazer com que o Razor código das páginas corresponda ao novo `DBSet` nome, faça uma alteração global de: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="0437b-259">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="0437b-260">Para: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="0437b-260">to: `_context.Students.`</span></span>

<span data-ttu-id="0437b-261">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="0437b-261">There are 8 occurrences.</span></span>

<span data-ttu-id="0437b-262">Como um conjunto de entidades contém várias entidades, muitos desenvolvedores preferem que os `DBSet` nomes de propriedade devem ser plural.</span><span class="sxs-lookup"><span data-stu-id="0437b-262">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="0437b-263">O código realçado:</span><span class="sxs-lookup"><span data-stu-id="0437b-263">The highlighted code:</span></span>

* <span data-ttu-id="0437b-264">Cria uma [propriedade \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0437b-264">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0437b-265">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="0437b-265">In EF Core terminology:</span></span>
  * <span data-ttu-id="0437b-266">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-266">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="0437b-267">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="0437b-267">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="0437b-268">Chama <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="0437b-268">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="0437b-269">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="0437b-269">`OnModelCreating`:</span></span>
  * <span data-ttu-id="0437b-270">É chamado quando foi `SchoolContext` inicializado, mas antes de o modelo ser bloqueado e usado para inicializar o contexto.</span><span class="sxs-lookup"><span data-stu-id="0437b-270">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="0437b-271">É necessário porque, posteriormente, no tutorial `Student` , a entidade terá referências a outras entidades.</span><span class="sxs-lookup"><span data-stu-id="0437b-271">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="0437b-272">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="0437b-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="0437b-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0437b-273">Startup.cs</span></span>

<span data-ttu-id="0437b-274">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0437b-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0437b-275">Serviços como o `SchoolContext` são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-275">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="0437b-276">Os componentes que exigem esses serviços, como Razor páginas, são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="0437b-276">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="0437b-277">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0437b-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0437b-278">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0437b-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0437b-280">As linhas realçadas a seguir foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0437b-280">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0437b-282">Verifique o código adicionado pelas chamadas scaffolder `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="0437b-282">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0437b-283">Consulte [usar o SQLite para desenvolvimento, SQL Server para produção](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) para obter informações sobre como usar um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="0437b-283">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="0437b-284">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="0437b-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0437b-285">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0437b-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="0437b-286">Adicionar o filtro de exceção de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-286">Add the database exception filter</span></span>

<span data-ttu-id="0437b-287">Adicione <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> ao `ConfigureServices` conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0437b-287">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-288">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="0437b-289">Adicione o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="0437b-289">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="0437b-290">No PMC, insira o seguinte para adicionar o pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="0437b-290">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="0437b-292">O `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` pacote NuGet fornece ASP.NET Core middleware para Entity Framework Core páginas de erro.</span><span class="sxs-lookup"><span data-stu-id="0437b-292">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="0437b-293">Esse middleware ajuda a detectar e diagnosticar erros com Entity Framework Core migrações.</span><span class="sxs-lookup"><span data-stu-id="0437b-293">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="0437b-294">O `AddDatabaseDeveloperPageExceptionFilter` fornece informações de erro úteis no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="0437b-294">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0437b-295">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-295">Create the database</span></span>

<span data-ttu-id="0437b-296">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="0437b-296">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0437b-297">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="0437b-297">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="0437b-298">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="0437b-298">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="0437b-299">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="0437b-299">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="0437b-300">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-300">Delete the database.</span></span> <span data-ttu-id="0437b-301">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="0437b-301">Any existing data is lost.</span></span>
* <span data-ttu-id="0437b-302">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-302">Change the data model.</span></span> <span data-ttu-id="0437b-303">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="0437b-303">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="0437b-304">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-304">Run the app.</span></span>
* <span data-ttu-id="0437b-305">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="0437b-305">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="0437b-306">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-306">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="0437b-307">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="0437b-307">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="0437b-308">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="0437b-308">When that is the case, use migrations.</span></span>

<span data-ttu-id="0437b-309">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="0437b-309">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="0437b-310">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="0437b-310">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0437b-311">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0437b-311">Test the app</span></span>

* <span data-ttu-id="0437b-312">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-312">Run the app.</span></span>
* <span data-ttu-id="0437b-313">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="0437b-313">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0437b-314">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="0437b-314">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0437b-315">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-315">Seed the database</span></span>

<span data-ttu-id="0437b-316">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="0437b-316">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="0437b-317">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="0437b-317">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="0437b-318">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-318">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="0437b-319">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-319">The code checks if there are any students in the database.</span></span> <span data-ttu-id="0437b-320">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-320">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="0437b-321">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="0437b-321">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0437b-322">Em *Program.cs*, substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="0437b-322">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-323">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-323">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0437b-324">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)**:</span><span class="sxs-lookup"><span data-stu-id="0437b-324">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="0437b-325">Responda com `Y` para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-325">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-326">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-326">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0437b-327">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="0437b-327">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="0437b-328">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-328">Restart the app.</span></span>
* <span data-ttu-id="0437b-329">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="0437b-329">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="0437b-330">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-330">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-331">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0437b-332">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0437b-332">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0437b-333">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="0437b-333">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="0437b-334">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="0437b-334">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="0437b-335">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="0437b-335">Expand the **Tables** node.</span></span>
* <span data-ttu-id="0437b-336">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="0437b-336">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="0437b-337">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-337">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-338">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-338">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0437b-339">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="0437b-339">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="0437b-340">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="0437b-340">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="0437b-341">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="0437b-341">Asynchronous code</span></span>

<span data-ttu-id="0437b-342">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="0437b-342">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0437b-343">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="0437b-343">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0437b-344">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="0437b-344">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0437b-345">Com o código síncrono, muitos threads podem ser ligados enquanto não estão trabalhando porque estão aguardando a conclusão de e/s.</span><span class="sxs-lookup"><span data-stu-id="0437b-345">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0437b-346">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="0437b-346">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0437b-347">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="0437b-347">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="0437b-348">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="0437b-348">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0437b-349">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="0437b-349">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0437b-350">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-350">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="0437b-351">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="0437b-351">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0437b-352">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="0437b-352">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0437b-353">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="0437b-353">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="0437b-354">O tipo retornado `Task` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="0437b-354">The `Task` return type represents ongoing work.</span></span>
* <span data-ttu-id="0437b-355">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="0437b-355">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0437b-356">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-356">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0437b-357">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="0437b-357">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0437b-358">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="0437b-358">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0437b-359">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="0437b-359">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0437b-360">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-360">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0437b-361">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="0437b-361">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0437b-362">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="0437b-362">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0437b-363">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="0437b-363">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0437b-364">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-364">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="0437b-365">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0437b-365">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="0437b-366">Considerações sobre o desempenho</span><span class="sxs-lookup"><span data-stu-id="0437b-366">Performance considerations</span></span>

<span data-ttu-id="0437b-367">Em geral, uma página da Web não deve carregar um número arbitrário de linhas.</span><span class="sxs-lookup"><span data-stu-id="0437b-367">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="0437b-368">Uma consulta deve usar a paginação ou uma abordagem de limitação.</span><span class="sxs-lookup"><span data-stu-id="0437b-368">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="0437b-369">Por exemplo, a consulta anterior poderia usar `Take` para limitar as linhas retornadas:</span><span class="sxs-lookup"><span data-stu-id="0437b-369">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0437b-370">Enumerar uma tabela grande em uma exibição pode retornar uma resposta HTTP 200 parcialmente construída se uma exceção de banco de dados ocorrer parcialmente por meio da enumeração.</span><span class="sxs-lookup"><span data-stu-id="0437b-370">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="0437b-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> o padrão é 1024.</span><span class="sxs-lookup"><span data-stu-id="0437b-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="0437b-372">Os seguintes conjuntos de código `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="0437b-372">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0437b-373">A paginação será abordada posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0437b-373">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0437b-374">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="0437b-374">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0437b-375">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="0437b-375">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0437b-376">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core Razor pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="0437b-376">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="0437b-377">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="0437b-377">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0437b-378">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="0437b-378">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0437b-379">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="0437b-379">The tutorial uses the code first approach.</span></span> <span data-ttu-id="0437b-380">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="0437b-380">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="0437b-381">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="0437b-381">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0437b-382">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0437b-382">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0437b-383">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0437b-383">Prerequisites</span></span>

* <span data-ttu-id="0437b-384">Se você for novo em Razor páginas, consulte a série de tutoriais [introdução às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta.</span><span class="sxs-lookup"><span data-stu-id="0437b-384">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-385">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-385">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="0437b-387">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-387">Database engines</span></span>

<span data-ttu-id="0437b-388">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="0437b-388">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="0437b-389">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="0437b-389">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="0437b-390">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="0437b-390">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0437b-391">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="0437b-391">Troubleshooting</span></span>

<span data-ttu-id="0437b-392">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0437b-392">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0437b-393">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0437b-393">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="0437b-394">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="0437b-394">The sample app</span></span>

<span data-ttu-id="0437b-395">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="0437b-395">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="0437b-396">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="0437b-396">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0437b-397">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0437b-397">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="0437b-400">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="0437b-400">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="0437b-401">O foco do tutorial está em como usar o EF Core, não em como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0437b-401">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="0437b-402">Siga o link na parte superior da página para obter o código-fonte do projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="0437b-402">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="0437b-403">A pasta *cu30* tem o código para a versão ASP.NET Core 3.0 do tutorial.</span><span class="sxs-lookup"><span data-stu-id="0437b-403">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="0437b-404">Os arquivos que refletem o estado do código para os tutoriais 1-7 podem ser encontrados na pasta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="0437b-404">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-405">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0437b-406">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="0437b-406">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0437b-407">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="0437b-407">Build the project.</span></span>
* <span data-ttu-id="0437b-408">No PMC (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0437b-408">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="0437b-409">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-409">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0437b-411">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="0437b-411">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0437b-412">Exclua *ContosoUniversity.csproj* e altere o nome de *ContosoUniversitySQLite.csproj* para *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0437b-412">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="0437b-413">No *Program.cs*, comente `#define Startup` que `StartupSQLite` é usado.</span><span class="sxs-lookup"><span data-stu-id="0437b-413">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="0437b-414">Exclua *appSettings.json* e altere o nome de *appSettingsSQLite.json* para *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0437b-414">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="0437b-415">Exclua a pasta *Migrations* e altere o nome de *MigrationsSQL* para *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="0437b-415">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="0437b-416">Faça uma pesquisa global para `#if SQLiteVersion` e remova `#if SQLiteVersion` e a `#endif` instrução associada.</span><span class="sxs-lookup"><span data-stu-id="0437b-416">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="0437b-417">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="0437b-417">Build the project.</span></span>
* <span data-ttu-id="0437b-418">Em um prompt de comando na pasta do projeto, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0437b-418">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="0437b-419">Em sua ferramenta do SQLite, execute a seguinte instrução SQL:</span><span class="sxs-lookup"><span data-stu-id="0437b-419">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="0437b-420">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-420">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="0437b-421">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="0437b-421">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0437b-423">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0437b-423">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0437b-424">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0437b-424">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0437b-425">Nomeie o projeto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="0437b-425">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="0437b-426">É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código for copiado e colado.</span><span class="sxs-lookup"><span data-stu-id="0437b-426">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="0437b-427">Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="0437b-427">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-428">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-428">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0437b-429">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="0437b-429">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="0437b-430">Execute os seguintes comandos para criar um Razor projeto de páginas e `cd` para a nova pasta de projeto:</span><span class="sxs-lookup"><span data-stu-id="0437b-430">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0437b-431">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="0437b-431">Set up the site style</span></span>

<span data-ttu-id="0437b-432">Configure o cabeçalho, o rodapé e o menu do site atualizando *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0437b-432">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="0437b-433">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="0437b-433">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0437b-434">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="0437b-434">There are three occurrences.</span></span>

* <span data-ttu-id="0437b-435">Exclua as entradas de menu **Início** e **Privacidade**, então adicione as entradas para **Sobre**, **Alunos**, **Cursos**, **Instrutores** e **Departamentos**.</span><span class="sxs-lookup"><span data-stu-id="0437b-435">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="0437b-436">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="0437b-436">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="0437b-437">Em *Pages/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET Core pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0437b-437">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="0437b-438">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="0437b-438">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="0437b-439">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-439">The data model</span></span>

<span data-ttu-id="0437b-440">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="0437b-440">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="0437b-442">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="0437b-442">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="0437b-443">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="0437b-443">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="0437b-445">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="0437b-445">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="0437b-446">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-446">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="0437b-447">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="0437b-447">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0437b-448">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0437b-448">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0437b-449">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-449">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="0437b-450">Para obter mais informações, consulte [chaves de EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="0437b-450">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="0437b-451">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0437b-451">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0437b-452">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="0437b-452">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0437b-453">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-453">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="0437b-454">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="0437b-454">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="0437b-455">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-455">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="0437b-456">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="0437b-456">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="0437b-457">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="0437b-457">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="0437b-458">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="0437b-458">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="0437b-459">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="0437b-459">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="0437b-460">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-460">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="0437b-461">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="0437b-461">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="0437b-462">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="0437b-462">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="0437b-464">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-464">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="0437b-465">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="0437b-465">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0437b-466">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="0437b-466">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="0437b-467">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="0437b-467">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="0437b-468">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-468">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="0437b-469">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="0437b-469">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0437b-470">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="0437b-470">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="0437b-471">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="0437b-471">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0437b-472">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-472">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0437b-473">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-473">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="0437b-474">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="0437b-474">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0437b-475">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="0437b-475">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0437b-476">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-476">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0437b-477">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-477">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0437b-478">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-478">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0437b-479">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-479">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0437b-480">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="0437b-480">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="0437b-482">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-482">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="0437b-483">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="0437b-483">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0437b-484">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="0437b-484">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0437b-485">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-485">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="0437b-486">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="0437b-486">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="0437b-487">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="0437b-487">Scaffold Student pages</span></span>

<span data-ttu-id="0437b-488">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="0437b-488">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="0437b-489">Uma classe *contexto* do EF Core.</span><span class="sxs-lookup"><span data-stu-id="0437b-489">An EF Core *context* class.</span></span> <span data-ttu-id="0437b-490">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-490">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="0437b-491">Ele deriva da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="0437b-491">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="0437b-492">Razor páginas que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a `Student` entidade.</span><span class="sxs-lookup"><span data-stu-id="0437b-492">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-493">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0437b-494">Crie uma pasta *Alunos* na pasta *Páginas*.</span><span class="sxs-lookup"><span data-stu-id="0437b-494">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="0437b-495">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Páginas/Alunos* e selecione **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="0437b-495">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0437b-496">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0437b-496">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="0437b-497">Na caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0437b-497">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="0437b-498">Na lista suspensa **classe Modelo**, selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="0437b-498">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="0437b-499">Na linha **Classe de contexto de dados**, selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="0437b-499">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="0437b-500">Altere o nome do contexto de dados de *ContosoUniversity.Models.ContosoUniversityContext* para *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="0437b-500">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="0437b-501">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0437b-501">Select **Add**.</span></span>

<span data-ttu-id="0437b-502">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="0437b-502">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-503">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-503">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0437b-504">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="0437b-504">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="0437b-505">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0437b-505">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="0437b-506">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0437b-506">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="0437b-507">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="0437b-507">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="0437b-508">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="0437b-508">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="0437b-509">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-509">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="0437b-510">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="0437b-510">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="0437b-511">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="0437b-511">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="0437b-512">Se você tiver um problema com a etapa anterior, compile o projeto e repita a etapa de scaffold.</span><span class="sxs-lookup"><span data-stu-id="0437b-512">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="0437b-513">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="0437b-513">The scaffolding process:</span></span>

* <span data-ttu-id="0437b-514">Cria Razor páginas na pasta *páginas/alunos* :</span><span class="sxs-lookup"><span data-stu-id="0437b-514">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="0437b-515">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-515">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="0437b-516">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-516">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="0437b-517">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-517">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="0437b-518">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-518">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="0437b-519">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-519">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="0437b-520">Cria *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="0437b-520">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="0437b-521">Adiciona o contexto à injeção de dependência em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0437b-521">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="0437b-522">Adiciona uma cadeia de conexão de banco de dados a *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="0437b-522">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="0437b-523">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-523">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-524">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-524">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0437b-525">O *appsettings.json* arquivo especifica a cadeia de conexão [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="0437b-525">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="0437b-526">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="0437b-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0437b-527">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-527">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-528">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-528">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0437b-529">Altere a cadeia de conexão para apontar para um arquivo de banco de dados SQLite chamado *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="0437b-529">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="0437b-530">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-530">Update the database context class</span></span>

<span data-ttu-id="0437b-531">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-531">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0437b-532">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0437b-532">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0437b-533">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-533">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0437b-534">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0437b-534">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0437b-535">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-535">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="0437b-536">O código realçado cria uma propriedade [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0437b-536">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0437b-537">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="0437b-537">In EF Core terminology:</span></span>

* <span data-ttu-id="0437b-538">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-538">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="0437b-539">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="0437b-539">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0437b-540">Como um conjunto de entidades contém várias entidades, as propriedades DBSet devem ser nomes no plural.</span><span class="sxs-lookup"><span data-stu-id="0437b-540">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="0437b-541">Como a ferramenta scaffolding criou um`Student` DBSet, essa etapa o altera para o `Students` no plural.</span><span class="sxs-lookup"><span data-stu-id="0437b-541">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="0437b-542">Para fazer com que o Razor código das páginas corresponda ao novo nome de DBSet, faça uma alteração global em todo o projeto de `_context.Student` para `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="0437b-542">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="0437b-543">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="0437b-543">There are 8 occurrences.</span></span>

<span data-ttu-id="0437b-544">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="0437b-544">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="0437b-545">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0437b-545">Startup.cs</span></span>

<span data-ttu-id="0437b-546">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0437b-546">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0437b-547">Serviços (como o contexto de banco de dados do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-547">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0437b-548">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="0437b-548">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0437b-549">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0437b-549">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0437b-550">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0437b-550">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-551">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-551">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0437b-552">Em `ConfigureServices`, as linhas destacadas foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0437b-552">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-553">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-553">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0437b-554">Em `ConfigureServices`, verifique se o código adicionado pelo scaffolder chama `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="0437b-554">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="0437b-555">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="0437b-555">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0437b-556">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0437b-556">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0437b-557">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-557">Create the database</span></span>

<span data-ttu-id="0437b-558">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="0437b-558">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0437b-559">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="0437b-559">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="0437b-560">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="0437b-560">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="0437b-561">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="0437b-561">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="0437b-562">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-562">Delete the database.</span></span> <span data-ttu-id="0437b-563">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="0437b-563">Any existing data is lost.</span></span>
* <span data-ttu-id="0437b-564">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-564">Change the data model.</span></span> <span data-ttu-id="0437b-565">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="0437b-565">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="0437b-566">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-566">Run the app.</span></span>
* <span data-ttu-id="0437b-567">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="0437b-567">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="0437b-568">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-568">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="0437b-569">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="0437b-569">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="0437b-570">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="0437b-570">When that is the case, use migrations.</span></span>

<span data-ttu-id="0437b-571">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="0437b-571">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="0437b-572">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="0437b-572">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0437b-573">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0437b-573">Test the app</span></span>

* <span data-ttu-id="0437b-574">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-574">Run the app.</span></span>
* <span data-ttu-id="0437b-575">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="0437b-575">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0437b-576">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="0437b-576">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0437b-577">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-577">Seed the database</span></span>

<span data-ttu-id="0437b-578">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="0437b-578">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="0437b-579">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="0437b-579">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="0437b-580">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-580">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="0437b-581">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-581">The code checks if there are any students in the database.</span></span> <span data-ttu-id="0437b-582">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-582">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="0437b-583">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="0437b-583">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="0437b-584">Em *Program.cs*, substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="0437b-584">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-585">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-585">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0437b-586">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)**:</span><span class="sxs-lookup"><span data-stu-id="0437b-586">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-587">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-587">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0437b-588">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="0437b-588">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="0437b-589">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-589">Restart the app.</span></span>

* <span data-ttu-id="0437b-590">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="0437b-590">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="0437b-591">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-591">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-592">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-592">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0437b-593">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0437b-593">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0437b-594">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="0437b-594">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="0437b-595">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="0437b-595">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="0437b-596">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="0437b-596">Expand the **Tables** node.</span></span>
* <span data-ttu-id="0437b-597">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="0437b-597">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="0437b-598">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-598">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-599">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-599">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0437b-600">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="0437b-600">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="0437b-601">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="0437b-601">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="0437b-602">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="0437b-602">Asynchronous code</span></span>

<span data-ttu-id="0437b-603">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="0437b-603">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0437b-604">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="0437b-604">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0437b-605">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="0437b-605">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0437b-606">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="0437b-606">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0437b-607">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="0437b-607">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0437b-608">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="0437b-608">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="0437b-609">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="0437b-609">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0437b-610">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="0437b-610">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0437b-611">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-611">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="0437b-612">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="0437b-612">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0437b-613">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="0437b-613">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0437b-614">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="0437b-614">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="0437b-615">O tipo retornado `Task<T>` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="0437b-615">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="0437b-616">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="0437b-616">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0437b-617">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-617">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0437b-618">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="0437b-618">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0437b-619">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="0437b-619">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0437b-620">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="0437b-620">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0437b-621">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-621">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0437b-622">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="0437b-622">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0437b-623">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="0437b-623">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0437b-624">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="0437b-624">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0437b-625">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-625">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="0437b-626">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0437b-626">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0437b-627">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="0437b-627">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0437b-628">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="0437b-628">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0437b-629">O aplicativo Web de exemplo Contoso University demonstra como criar um Razor aplicativo de páginas ASP.NET Core usando Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="0437b-629">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="0437b-630">O aplicativo de exemplo é um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="0437b-630">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0437b-631">Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="0437b-631">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0437b-632">Esta página é a primeira de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0437b-632">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="0437b-633">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="0437b-633">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0437b-634">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0437b-634">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0437b-635">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0437b-635">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-636">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-636">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-637">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-637">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="0437b-638">Familiaridade com [ Razor páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="0437b-638">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="0437b-639">Os novos programadores devem concluir a [introdução às Razor páginas](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar esta série.</span><span class="sxs-lookup"><span data-stu-id="0437b-639">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0437b-640">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="0437b-640">Troubleshooting</span></span>

<span data-ttu-id="0437b-641">Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="0437b-641">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0437b-642">Uma boa maneira de obter ajuda é postando uma pergunta no [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="0437b-642">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="0437b-643">O aplicativo Web Contoso University</span><span class="sxs-lookup"><span data-stu-id="0437b-643">The Contoso University web app</span></span>

<span data-ttu-id="0437b-644">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="0437b-644">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="0437b-645">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="0437b-645">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0437b-646">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0437b-646">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

<span data-ttu-id="0437b-649">O estilo de interface do usuário deste site é próximo ao que é gerado pelos modelos internos.</span><span class="sxs-lookup"><span data-stu-id="0437b-649">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="0437b-650">O foco do tutorial está em EF Core com Razor páginas, não na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0437b-650">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="0437b-651">Criar o Razor aplicativo Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="0437b-651">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-652">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-652">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0437b-653">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0437b-653">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0437b-654">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0437b-654">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="0437b-655">Nomeie o projeto **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="0437b-655">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="0437b-656">É importante nomear o projeto *ContosoUniversity* para que os namespaces sejam correspondentes quando o código for copiado/colado.</span><span class="sxs-lookup"><span data-stu-id="0437b-656">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="0437b-657">Selecione **ASP.NET Core 2.1** na lista suspensa e selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="0437b-657">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="0437b-658">Para imagens das etapas anteriores, consulte [criar um Razor aplicativo Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="0437b-658">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="0437b-659">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-659">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-660">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-660">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0437b-661">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="0437b-661">Set up the site style</span></span>

<span data-ttu-id="0437b-662">Algumas alterações configuram o menu do site, o layout e a home page.</span><span class="sxs-lookup"><span data-stu-id="0437b-662">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="0437b-663">Atualize *Pages/Shared/_Layout.cshtml* com as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="0437b-663">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="0437b-664">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="0437b-664">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0437b-665">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="0437b-665">There are three occurrences.</span></span>

* <span data-ttu-id="0437b-666">Adicione entradas de menu para **Alunos**, **Cursos**, **Instrutores** e **Departamentos** e exclua a entrada de menu **Contato**.</span><span class="sxs-lookup"><span data-stu-id="0437b-666">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="0437b-667">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="0437b-667">The changes are highlighted.</span></span> <span data-ttu-id="0437b-668">(Toda a marcação *não* é exibida.)</span><span class="sxs-lookup"><span data-stu-id="0437b-668">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="0437b-669">Em *Pages/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0437b-669">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="0437b-670">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="0437b-670">Create the data model</span></span>

<span data-ttu-id="0437b-671">Crie classes de entidade para o aplicativo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="0437b-671">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="0437b-672">Comece com as três seguintes entidades:</span><span class="sxs-lookup"><span data-stu-id="0437b-672">Start with the following three entities:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="0437b-674">Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="0437b-674">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="0437b-675">Há uma relação um-para-muitos entre as entidades `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="0437b-675">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="0437b-676">Um aluno pode se registrar em qualquer quantidade de cursos.</span><span class="sxs-lookup"><span data-stu-id="0437b-676">A student can enroll in any number of courses.</span></span> <span data-ttu-id="0437b-677">Um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="0437b-677">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="0437b-678">Nas seções a seguir, é criada uma classe para cada uma dessas entidades.</span><span class="sxs-lookup"><span data-stu-id="0437b-678">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="0437b-679">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="0437b-679">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

<span data-ttu-id="0437b-681">Crie uma pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="0437b-681">Create a *Models* folder.</span></span> <span data-ttu-id="0437b-682">Na pasta *Models*, crie um arquivo de classe chamado *Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-682">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="0437b-683">A propriedade `ID` se torna a coluna de chave primária da tabela de BD (banco de dados) que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="0437b-683">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="0437b-684">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0437b-684">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0437b-685">Em `classnameID`, `classname` é o nome da classe.</span><span class="sxs-lookup"><span data-stu-id="0437b-685">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="0437b-686">A chave primária alternativa reconhecida automaticamente é `StudentID` no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="0437b-686">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="0437b-687">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="0437b-687">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0437b-688">As propriedades de navegação vinculam-se a outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="0437b-688">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="0437b-689">Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazena todas as entidades `Enrollment` relacionadas a essa `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-689">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="0437b-690">Por exemplo, se uma linha Aluno no BD tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="0437b-690">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="0437b-691">Uma linha `Enrollment` relacionada é uma linha que contém o valor de chave primária do aluno na coluna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-691">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="0437b-692">Por exemplo, suponha que o aluno com ID=1 tenha duas linhas na tabela `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="0437b-692">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="0437b-693">A tabela `Enrollment` tem duas linhas com `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="0437b-693">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="0437b-694">`StudentID` é uma chave estrangeira na tabela `Enrollment` que especifica o aluno na tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-694">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="0437b-695">Se uma propriedade de navegação puder armazenar várias entidades, a propriedade de navegação deverá ser um tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-695">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="0437b-696">`ICollection<T>` pode ser especificado ou um tipo como `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-696">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="0437b-697">Quando `ICollection<T>` é usado, o EF Core cria uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="0437b-697">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="0437b-698">As propriedades de navegação que armazenam várias entidades são provenientes de relações muitos para muitos e um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="0437b-698">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="0437b-699">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="0437b-699">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="0437b-701">Na pasta *Models*, crie *Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-701">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="0437b-702">A propriedade `EnrollmentID` é a chave primária.</span><span class="sxs-lookup"><span data-stu-id="0437b-702">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="0437b-703">Essa entidade usa o padrão `classnameID` em vez de `ID` como a entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-703">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="0437b-704">Normalmente, os desenvolvedores escolhem um padrão e o usam em todo o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-704">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="0437b-705">Em um tutorial posterior, o uso de uma ID sem nome de classe é mostrado para facilitar a implementação da herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-705">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="0437b-706">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="0437b-706">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0437b-707">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="0437b-707">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="0437b-708">Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="0437b-708">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0437b-709">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-709">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0437b-710">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="0437b-710">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="0437b-711">A entidade `Student` é distinta da propriedade de navegação `Student.Enrollments`, que contém várias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="0437b-711">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="0437b-712">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="0437b-712">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0437b-713">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="0437b-713">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0437b-714">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-714">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0437b-715">Por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-715">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0437b-716">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-716">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0437b-717">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="0437b-717">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="0437b-718">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="0437b-718">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="0437b-720">Na pasta *Models*, crie *Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-720">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="0437b-721">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="0437b-721">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0437b-722">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="0437b-722">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0437b-723">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo BD.</span><span class="sxs-lookup"><span data-stu-id="0437b-723">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="0437b-724">Gere um modelo de aluno por scaffold</span><span class="sxs-lookup"><span data-stu-id="0437b-724">Scaffold the student model</span></span>

<span data-ttu-id="0437b-725">Nesta seção, é feito o scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-725">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="0437b-726">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-726">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="0437b-727">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="0437b-727">Build the project.</span></span>
* <span data-ttu-id="0437b-728">Crie a pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="0437b-728">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-729">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-729">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0437b-730">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Pages/Students* > **Adicionar** > **Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="0437b-730">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0437b-731">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor páginas usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0437b-731">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="0437b-732">Conclua a caixa de diálogo **Adicionar Razor páginas usando Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="0437b-732">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0437b-733">Na lista suspensa **classe Modelo**, selecione **Aluno (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="0437b-733">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="0437b-734">Na linha **Classe de contexto de dados**, selecione o sinal de (mais) **+** e altere o nome gerado para **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="0437b-734">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="0437b-735">Na lista suspensa **Classe de contexto de dados**, selecione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="0437b-735">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="0437b-736">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0437b-736">Select **Add**.</span></span>

![Caixa de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="0437b-738">Confira [Fazer scaffold do modelo de filme](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se tiver problemas na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="0437b-738">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-739">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-739">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0437b-740">Execute os comandos a seguir para fazer scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="0437b-740">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="0437b-741">O processo de scaffold criou e alterou os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="0437b-741">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0437b-742">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="0437b-742">Files created</span></span>

* <span data-ttu-id="0437b-743">*Pages/Students* Criar, Excluir, Detalhes, Editar, Índice.</span><span class="sxs-lookup"><span data-stu-id="0437b-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="0437b-744">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0437b-744">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="0437b-745">Atualizações de arquivo</span><span class="sxs-lookup"><span data-stu-id="0437b-745">File updates</span></span>

* <span data-ttu-id="0437b-746">*Startup.cs*: alterações a esse arquivo serão detalhadas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="0437b-746">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="0437b-747">*appsettings.json* : A cadeia de conexão usada para se conectar a um banco de dados local é adicionada.</span><span class="sxs-lookup"><span data-stu-id="0437b-747">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0437b-748">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="0437b-748">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0437b-749">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0437b-749">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0437b-750">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-750">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0437b-751">Os componentes que exigem esses serviços (como Razor páginas) são fornecidos a esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="0437b-751">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0437b-752">O código de construtor que obtém uma instância de contexto do BD é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0437b-752">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0437b-753">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0437b-753">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0437b-754">Examine o método `ConfigureServices` em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0437b-754">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="0437b-755">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0437b-755">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="0437b-756">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="0437b-756">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0437b-757">Para o desenvolvimento local, o [sistema de configuração ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do *appsettings.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0437b-757">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="0437b-758">Atualizar o principal</span><span class="sxs-lookup"><span data-stu-id="0437b-758">Update main</span></span>

<span data-ttu-id="0437b-759">Em *Program.cs*, modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="0437b-759">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="0437b-760">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0437b-760">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="0437b-761">Chame o [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="0437b-761">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="0437b-762">Descarte o contexto quando o método `EnsureCreated` for concluído.</span><span class="sxs-lookup"><span data-stu-id="0437b-762">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="0437b-763">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="0437b-763">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="0437b-764">`EnsureCreated` garante que o banco de dados do contexto exista.</span><span class="sxs-lookup"><span data-stu-id="0437b-764">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="0437b-765">Se ele existir, nenhuma ação será realizada.</span><span class="sxs-lookup"><span data-stu-id="0437b-765">If it exists, no action is taken.</span></span> <span data-ttu-id="0437b-766">Se ele não existir, o banco de dados e todos os seus esquemas serão criados.</span><span class="sxs-lookup"><span data-stu-id="0437b-766">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="0437b-767">`EnsureCreated` não usa migrações para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-767">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="0437b-768">Um banco de dados criado com `EnsureCreated` não pode ser atualizado posteriormente usando migrações.</span><span class="sxs-lookup"><span data-stu-id="0437b-768">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="0437b-769">`EnsureCreated` é chamado na inicialização do aplicativo, que permite que o seguinte fluxo de trabalho:</span><span class="sxs-lookup"><span data-stu-id="0437b-769">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="0437b-770">Exclua o BD.</span><span class="sxs-lookup"><span data-stu-id="0437b-770">Delete the DB.</span></span>
* <span data-ttu-id="0437b-771">Altere o esquema de BD (por exemplo, adicione um campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="0437b-771">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="0437b-772">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0437b-772">Run the app.</span></span>
* <span data-ttu-id="0437b-773">`EnsureCreated` cria um BD com a coluna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="0437b-773">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="0437b-774">`EnsureCreated` é conveniente no início do desenvolvimento quando o esquema está evoluindo rapidamente.</span><span class="sxs-lookup"><span data-stu-id="0437b-774">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="0437b-775">Mais tarde, no tutorial do banco de dados, é excluído e as migrações são usadas.</span><span class="sxs-lookup"><span data-stu-id="0437b-775">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0437b-776">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0437b-776">Test the app</span></span>

<span data-ttu-id="0437b-777">Execute o aplicativo e aceite a cookie política.</span><span class="sxs-lookup"><span data-stu-id="0437b-777">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="0437b-778">Este aplicativo não armazena informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="0437b-778">This app doesn't keep personal information.</span></span> <span data-ttu-id="0437b-779">Você pode ler sobre a cookie política no [suporte de regulamento geral sobre a proteção de dados da UE (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="0437b-779">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="0437b-780">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="0437b-780">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0437b-781">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="0437b-781">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="0437b-782">Examine o contexto de BD SchoolContext</span><span class="sxs-lookup"><span data-stu-id="0437b-782">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="0437b-783">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="0437b-783">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="0437b-784">O contexto de dados deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0437b-784">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0437b-785">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="0437b-785">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0437b-786">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0437b-786">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0437b-787">Atualize *SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-787">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="0437b-788">O código realçado cria uma propriedade [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0437b-788">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0437b-789">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="0437b-789">In EF Core terminology:</span></span>

* <span data-ttu-id="0437b-790">Um conjunto de entidades normalmente corresponde a uma tabela de BD.</span><span class="sxs-lookup"><span data-stu-id="0437b-790">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="0437b-791">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="0437b-791">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0437b-792">`DbSet<Enrollment>` e `DbSet<Course>` podem ser omitidos.</span><span class="sxs-lookup"><span data-stu-id="0437b-792">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="0437b-793">O EF Core inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="0437b-793">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="0437b-794">Para este tutorial, mantenha `DbSet<Enrollment>` e `DbSet<Course>` no `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="0437b-794">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="0437b-795">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="0437b-795">SQL Server Express LocalDB</span></span>

<span data-ttu-id="0437b-796">A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="0437b-796">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="0437b-797">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="0437b-797">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0437b-798">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="0437b-798">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="0437b-799">Por padrão, o LocalDB cria arquivos *.mdf* de BD no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="0437b-799">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="0437b-800">Adicionar um código para inicializar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="0437b-800">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="0437b-801">O EF Core cria um BD vazio.</span><span class="sxs-lookup"><span data-stu-id="0437b-801">EF Core creates an empty DB.</span></span> <span data-ttu-id="0437b-802">Nesta seção, um método `Initialize` é escrito para populá-lo com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="0437b-802">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="0437b-803">Na pasta *Dados*, crie um novo arquivo de classe chamado *DbInitializer.cs* e adicione o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="0437b-803">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="0437b-804">Observação: o código anterior usa `Models` para o namespace ( `namespace ContosoUniversity.Models` ) em vez de `Data` .</span><span class="sxs-lookup"><span data-stu-id="0437b-804">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="0437b-805">`Models` é consistente com o código gerado pelo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="0437b-805">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="0437b-806">Para saber mais, confira [este problema de scaffolding do GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="0437b-806">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="0437b-807">O código verifica se há alunos no BD.</span><span class="sxs-lookup"><span data-stu-id="0437b-807">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="0437b-808">Se não houver nenhum aluno no BD, o BD será inicializado com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="0437b-808">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="0437b-809">Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="0437b-809">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0437b-810">O método `EnsureCreated` cria o BD automaticamente para o contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="0437b-810">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="0437b-811">Se o BD existir, `EnsureCreated` retornará sem modificar o BD.</span><span class="sxs-lookup"><span data-stu-id="0437b-811">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="0437b-812">Em *Program.cs*, modifique o método `Main` para chamar `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="0437b-812">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="0437b-813">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0437b-813">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0437b-814">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)**:</span><span class="sxs-lookup"><span data-stu-id="0437b-814">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0437b-815">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0437b-815">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0437b-816">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="0437b-816">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="0437b-817">Exibir o BD</span><span class="sxs-lookup"><span data-stu-id="0437b-817">View the DB</span></span>

<span data-ttu-id="0437b-818">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="0437b-818">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="0437b-819">Assim, o nome do banco de dados será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="0437b-819">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="0437b-820">O GUID será diferente para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="0437b-820">The GUID will be different for each user.</span></span>
<span data-ttu-id="0437b-821">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0437b-821">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="0437b-822">No SSOX, clique em **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="0437b-822">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="0437b-823">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="0437b-823">Expand the **Tables** node.</span></span>

<span data-ttu-id="0437b-824">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="0437b-824">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="0437b-825">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="0437b-825">Asynchronous code</span></span>

<span data-ttu-id="0437b-826">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="0437b-826">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0437b-827">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="0437b-827">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0437b-828">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="0437b-828">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0437b-829">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="0437b-829">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0437b-830">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="0437b-830">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0437b-831">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="0437b-831">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="0437b-832">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="0437b-832">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0437b-833">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="0437b-833">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0437b-834">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-834">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="0437b-835">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="0437b-835">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0437b-836">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="0437b-836">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0437b-837">Criar automaticamente o objeto [Task](/dotnet/api/system.threading.tasks.task) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="0437b-837">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="0437b-838">Para obter mais informações, consulte [Tipo de retorno de Tarefa](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="0437b-838">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="0437b-839">O tipo de retorno implícito `Task` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="0437b-839">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="0437b-840">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="0437b-840">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0437b-841">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-841">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0437b-842">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="0437b-842">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0437b-843">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="0437b-843">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0437b-844">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="0437b-844">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0437b-845">Somente instruções que fazem com que consultas ou comandos sejam enviados ao BD são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0437b-845">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="0437b-846">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="0437b-846">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0437b-847">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="0437b-847">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0437b-848">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="0437b-848">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0437b-849">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamam métodos do EF Core que enviam consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="0437b-849">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="0437b-850">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="0437b-850">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="0437b-851">No próximo tutorial, as operações CRUD (criar, ler, atualizar e excluir) básicas são examinadas.</span><span class="sxs-lookup"><span data-stu-id="0437b-851">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="0437b-852">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0437b-852">Additional resources</span></span>

* [<span data-ttu-id="0437b-853">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="0437b-853">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="0437b-854">Próximo</span><span class="sxs-lookup"><span data-stu-id="0437b-854">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
