---
title: ASP.NET Core MVC com EF Core – modelo de dados – 5 de 10
author: rick-anderson
description: Neste tutorial, você adiciona mais entidades e relações e personaliza o modelo de dados especificando formatação, validação e regras de mapeamento.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 87212edbfe34af6de938cf95314501e56e64a8be
ms.sourcegitcommit: 4d74644f11e0dac52b4510048490ae731c691496
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091035"
---
# <a name="aspnet-core-mvc-with-ef-core---data-model---5-of-10"></a><span data-ttu-id="b8aff-103">ASP.NET Core MVC com EF Core – modelo de dados – 5 de 10</span><span class="sxs-lookup"><span data-stu-id="b8aff-103">ASP.NET Core MVC with EF Core - Data Model - 5 of 10</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc-21.md)]

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b8aff-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b8aff-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b8aff-105">O aplicativo web de exemplo Contoso University demonstra como criar aplicativos web do ASP.NET Core MVC usando o Entity Framework Core e o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b8aff-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="b8aff-106">Para obter informações sobre a série de tutoriais, consulte [o primeiro tutorial da série](intro.md).</span><span class="sxs-lookup"><span data-stu-id="b8aff-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="b8aff-107">Nos tutoriais anteriores, você trabalhou com um modelo de dados simples composto por três entidades.</span><span class="sxs-lookup"><span data-stu-id="b8aff-107">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="b8aff-108">Neste tutorial, você adicionará mais entidades e relações e personalizará o modelo de dados especificando formatação, validação e regras de mapeamento de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-108">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="b8aff-109">Quando terminar, as classes de entidade formarão o modelo de dados concluído mostrado na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="b8aff-109">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

## <a name="customize-the-data-model-by-using-attributes"></a><span data-ttu-id="b8aff-111">Personalizar o modelo de dados usando atributos</span><span class="sxs-lookup"><span data-stu-id="b8aff-111">Customize the Data Model by Using Attributes</span></span>

<span data-ttu-id="b8aff-112">Nesta seção, você verá como personalizar o modelo de dados usando atributos que especificam formatação, validação e regras de mapeamento de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-112">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="b8aff-113">Em seguida, em várias seções a seguir, você criará o modelo de dados Escola completo com a adição de atributos às classes já criadas e criação de novas classes para os demais tipos de entidade no modelo.</span><span class="sxs-lookup"><span data-stu-id="b8aff-113">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="b8aff-114">O atributo DataType</span><span class="sxs-lookup"><span data-stu-id="b8aff-114">The DataType attribute</span></span>

<span data-ttu-id="b8aff-115">Para datas de registro de alunos, todas as páginas da Web atualmente exibem a hora junto com a data, embora tudo o que você deseje exibir nesse campo seja a data.</span><span class="sxs-lookup"><span data-stu-id="b8aff-115">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="b8aff-116">Usando atributos de anotação de dados, você pode fazer uma alteração de código que corrigirá o formato de exibição em cada exibição que mostra os dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-116">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="b8aff-117">Para ver um exemplo de como fazer isso, você adicionará um atributo à propriedade `EnrollmentDate` na classe `Student`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-117">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="b8aff-118">Em *Models/Student.cs*, adicione uma instrução `using` ao namespace `System.ComponentModel.DataAnnotations` e adicione os atributos `DataType` e `DisplayFormat` à `EnrollmentDate` propriedade, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="b8aff-118">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="b8aff-119">O atributo `DataType` é usado para especificar um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-119">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="b8aff-120">Nesse caso, apenas desejamos acompanhar a data, não a data e a hora.</span><span class="sxs-lookup"><span data-stu-id="b8aff-120">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="b8aff-121">A Enumeração `DataType` fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress e muito mais.</span><span class="sxs-lookup"><span data-stu-id="b8aff-121">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="b8aff-122">O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo.</span><span class="sxs-lookup"><span data-stu-id="b8aff-122">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="b8aff-123">Por exemplo, um link `mailto:` pode ser criado para `DataType.EmailAddress` e um seletor de data pode ser fornecido para `DataType.Date` em navegadores que dão suporte a HTML5.</span><span class="sxs-lookup"><span data-stu-id="b8aff-123">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="b8aff-124">O atributo `DataType` emite atributos `data-` HTML 5 (pronunciados “data dash”) que são reconhecidos pelos navegadores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="b8aff-124">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="b8aff-125">Os atributos `DataType` não fornecem nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="b8aff-125">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="b8aff-126">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="b8aff-126">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="b8aff-127">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas CultureInfo do servidor.</span><span class="sxs-lookup"><span data-stu-id="b8aff-127">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="b8aff-128">O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:</span><span class="sxs-lookup"><span data-stu-id="b8aff-128">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="b8aff-129">A configuração `ApplyFormatInEditMode` especifica que a formatação também deve ser aplicada quando o valor é exibido em uma caixa de texto para edição.</span><span class="sxs-lookup"><span data-stu-id="b8aff-129">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="b8aff-130">(Talvez você não deseje ter isso em alguns campos – por exemplo, para valores de moeda, provavelmente, você não deseja exibir o símbolo de moeda na caixa de texto para edição.)</span><span class="sxs-lookup"><span data-stu-id="b8aff-130">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="b8aff-131">Use Você pode usar o atributo `DisplayFormat` por si só, mas geralmente é uma boa ideia usar o atributo `DataType` também.</span><span class="sxs-lookup"><span data-stu-id="b8aff-131">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="b8aff-132">O atributo `DataType` transmite a semântica dos dados, ao invés de apresentar como renderizá-lo em uma tela, e oferece os seguintes benefícios que você não obtém com `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="b8aff-132">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="b8aff-133">O navegador pode habilitar os recursos do HTML5 (por exemplo, mostrar um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, uma validação de entrada do lado do cliente, etc.).</span><span class="sxs-lookup"><span data-stu-id="b8aff-133">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="b8aff-134">Por padrão, o navegador renderizará os dados usando o formato correto de acordo com a localidade.</span><span class="sxs-lookup"><span data-stu-id="b8aff-134">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="b8aff-135">Para obter mais informações, consulte a [documentação do auxiliar de marcação \<input>](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b8aff-135">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="b8aff-136">Execute o aplicativo, acesse a página Índice de Alunos e observe que as horas não são mais exibidas nas datas de registro.</span><span class="sxs-lookup"><span data-stu-id="b8aff-136">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="b8aff-137">O mesmo será verdadeiro para qualquer exibição que usa o modelo Aluno.</span><span class="sxs-lookup"><span data-stu-id="b8aff-137">The same will be true for any view that uses the Student model.</span></span>

![Página Índice de Alunos mostrando datas sem horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="b8aff-139">O atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="b8aff-139">The StringLength attribute</span></span>

<span data-ttu-id="b8aff-140">Você também pode especificar regras de validação de dados e mensagens de erro de validação usando atributos.</span><span class="sxs-lookup"><span data-stu-id="b8aff-140">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="b8aff-141">O atributo `StringLength` define o tamanho máximo do banco de dados e fornece validação do lado do cliente e do lado do servidor para o ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="b8aff-141">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="b8aff-142">Você também pode especificar o tamanho mínimo da cadeia de caracteres nesse atributo, mas o valor mínimo não tem nenhum impacto sobre o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-142">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="b8aff-143">Suponha que você deseje garantir que os usuários não insiram mais de 50 caracteres em um nome.</span><span class="sxs-lookup"><span data-stu-id="b8aff-143">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="b8aff-144">Para adicionar essa limitação, adicione atributos `StringLength` às propriedades `LastName` e `FirstMidName`, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="b8aff-144">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="b8aff-145">O atributo `StringLength` não impedirá que um usuário insira um espaço em branco em um nome.</span><span class="sxs-lookup"><span data-stu-id="b8aff-145">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="b8aff-146">Use o atributo `RegularExpression` para aplicar restrições à entrada.</span><span class="sxs-lookup"><span data-stu-id="b8aff-146">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="b8aff-147">Por exemplo, o seguinte código exige que o primeiro caractere esteja em maiúscula e os caracteres restantes estejam em ordem alfabética:</span><span class="sxs-lookup"><span data-stu-id="b8aff-147">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="b8aff-148">O atributo `MaxLength` fornece uma funcionalidade semelhante ao atributo `StringLength`, mas não fornece a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b8aff-148">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="b8aff-149">Agora, o modelo de banco de dados foi alterado de uma forma que exige uma alteração no esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-149">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="b8aff-150">Você usará migrações para atualizar o esquema sem perda dos dados que podem ter sido adicionados ao banco de dados usando a interface do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b8aff-150">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="b8aff-151">Salve as alterações e compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="b8aff-151">Save your changes and build the project.</span></span> <span data-ttu-id="b8aff-152">Em seguida, abra a janela Comando na pasta do projeto e insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="b8aff-152">Then open the command window in the project folder and enter the following commands:</span></span>

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

<span data-ttu-id="b8aff-153">O comando `migrations add` alerta que pode ocorrer perda de dados, pois a alteração torna o tamanho máximo mais curto para duas colunas.</span><span class="sxs-lookup"><span data-stu-id="b8aff-153">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="b8aff-154">As migrações criam um arquivo chamado *\<timeStamp>_MaxLengthOnNames.cs*.</span><span class="sxs-lookup"><span data-stu-id="b8aff-154">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="b8aff-155">Esse arquivo contém o código no método `Up` que atualizará o banco de dados para que ele corresponda ao modelo de dados atual.</span><span class="sxs-lookup"><span data-stu-id="b8aff-155">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="b8aff-156">O comando `database update` executou esse código.</span><span class="sxs-lookup"><span data-stu-id="b8aff-156">The `database update` command ran that code.</span></span>

<span data-ttu-id="b8aff-157">O carimbo de data/hora prefixado ao nome do arquivo de migrações é usado pelo Entity Framework para ordenar as migrações.</span><span class="sxs-lookup"><span data-stu-id="b8aff-157">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="b8aff-158">Crie várias migrações antes de executar o comando de atualização de banco de dados e, em seguida, todas as migrações são aplicadas na ordem em que foram criadas.</span><span class="sxs-lookup"><span data-stu-id="b8aff-158">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="b8aff-159">Execute o aplicativo, selecione a guia **Alunos**, clique em **Criar Novo** e insira um nome com mais de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="b8aff-159">Run the app, select the **Students** tab, click **Create New**, and enter either name longer than 50 characters.</span></span> <span data-ttu-id="b8aff-160">Quando você clica em **Criar**, a validação do lado do cliente mostra uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="b8aff-160">When you click **Create**, client side validation shows an error message.</span></span>

![Página Índice de Alunos mostrando erros de tamanho de cadeia de caracteres](complex-data-model/_static/string-length-errors.png)

### <a name="the-column-attribute"></a><span data-ttu-id="b8aff-162">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="b8aff-162">The Column attribute</span></span>

<span data-ttu-id="b8aff-163">Você também pode usar atributos para controlar como as classes e propriedades são mapeadas para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-163">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="b8aff-164">Suponha que você tenha usado o nome `FirstMidName` para o campo de nome porque o campo também pode conter um sobrenome.</span><span class="sxs-lookup"><span data-stu-id="b8aff-164">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="b8aff-165">Mas você deseja que a coluna do banco de dados seja nomeada `FirstName`, pois os usuários que escreverão consultas ad hoc no banco de dados estão acostumados com esse nome.</span><span class="sxs-lookup"><span data-stu-id="b8aff-165">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="b8aff-166">Para fazer esse mapeamento, use o atributo `Column`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-166">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="b8aff-167">O atributo `Column` especifica que quando o banco de dados for criado, a coluna da tabela `Student` que é mapeada para a propriedade `FirstMidName` será nomeada `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-167">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="b8aff-168">Em outras palavras, quando o código se referir a `Student.FirstMidName`, os dados serão obtidos ou atualizados na coluna `FirstName` da tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-168">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="b8aff-169">Se você não especificar nomes de coluna, elas receberão o mesmo nome da propriedade.</span><span class="sxs-lookup"><span data-stu-id="b8aff-169">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="b8aff-170">No arquivo *Student.cs*, adicione uma instrução `using` a `System.ComponentModel.DataAnnotations.Schema` e adicione o atributo de nome de coluna à propriedade `FirstMidName`, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="b8aff-170">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="b8aff-171">A adição do atributo `Column` altera o modelo que dá suporte ao `SchoolContext` e, portanto, ele não corresponde ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-171">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="b8aff-172">Salve as alterações e compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="b8aff-172">Save your changes and build the project.</span></span> <span data-ttu-id="b8aff-173">Em seguida, abra a janela Comando na pasta do projeto e insira os seguintes comandos para criar outra migração:</span><span class="sxs-lookup"><span data-stu-id="b8aff-173">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

<span data-ttu-id="b8aff-174">No **Pesquisador de Objetos do SQL Server**, abra o designer de tabela Aluno clicando duas vezes na tabela **Aluno**.</span><span class="sxs-lookup"><span data-stu-id="b8aff-174">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela Alunos no SSOX após as migrações](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="b8aff-176">Antes de você aplicar as duas primeiras migrações, as colunas de nome eram do tipo nvarchar(MAX).</span><span class="sxs-lookup"><span data-stu-id="b8aff-176">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="b8aff-177">Agora elas são nvarchar(50) e o nome da coluna foi alterado de FirstMidName para FirstName.</span><span class="sxs-lookup"><span data-stu-id="b8aff-177">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="b8aff-178">Se você tentar compilar antes de concluir a criação de todas as classes de entidade nas seções a seguir, poderá receber erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="b8aff-178">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="final-changes-to-the-student-entity"></a><span data-ttu-id="b8aff-179">Alterações finais na entidade Student</span><span class="sxs-lookup"><span data-stu-id="b8aff-179">Final changes to the Student entity</span></span>

![Entidade Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="b8aff-181">Em *Models/Student.cs*, substitua o código que você adicionou anteriormente pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="b8aff-181">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="b8aff-182">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="b8aff-182">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="b8aff-183">O atributo Required</span><span class="sxs-lookup"><span data-stu-id="b8aff-183">The Required attribute</span></span>

<span data-ttu-id="b8aff-184">O atributo `Required` torna as propriedades de nome campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="b8aff-184">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="b8aff-185">O atributo `Required` não é necessário para tipos que permitem valor nulo como tipos de valor (DateTime, int, double, float, etc.).</span><span class="sxs-lookup"><span data-stu-id="b8aff-185">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="b8aff-186">Tipos que não podem ser nulos são tratados automaticamente como campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="b8aff-186">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="b8aff-187">Remova o atributo `Required` e substitua-o por um parâmetro de tamanho mínimo para o atributo `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="b8aff-187">You could remove the `Required` attribute and replace it with a minimum length parameter for the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="b8aff-188">O atributo Display</span><span class="sxs-lookup"><span data-stu-id="b8aff-188">The Display attribute</span></span>

<span data-ttu-id="b8aff-189">O atributo `Display` especifica que a legenda para as caixas de texto deve ser "Nome", "Sobrenome", "Nome Completo" e "Data de Registro", em vez do nome de a propriedade em cada instância (que não tem nenhum espaço entre as palavras).</span><span class="sxs-lookup"><span data-stu-id="b8aff-189">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="b8aff-190">A propriedade calculada FullName</span><span class="sxs-lookup"><span data-stu-id="b8aff-190">The FullName calculated property</span></span>

<span data-ttu-id="b8aff-191">`FullName` é uma propriedade calculada que retorna um valor criado pela concatenação de duas outras propriedades.</span><span class="sxs-lookup"><span data-stu-id="b8aff-191">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="b8aff-192">Portanto, ela tem apenas um acessador get e nenhuma coluna `FullName` será gerada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-192">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="b8aff-193">Criar a entidade Instructor</span><span class="sxs-lookup"><span data-stu-id="b8aff-193">Create the Instructor Entity</span></span>

![Entidade Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="b8aff-195">Crie *Models/Instructor.cs*, substituindo o código de modelo com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="b8aff-195">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="b8aff-196">Observe que várias propriedades são as mesmas nas entidades Student e Instructor.</span><span class="sxs-lookup"><span data-stu-id="b8aff-196">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="b8aff-197">No tutorial [Implementando a herança](inheritance.md) mais adiante nesta série, você refatorará esse código para eliminar a redundância.</span><span class="sxs-lookup"><span data-stu-id="b8aff-197">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="b8aff-198">Coloque vários atributos em uma linha, de modo que você também possa escrever os atributos `HireDate` da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="b8aff-198">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="b8aff-199">As propriedades de navegação CourseAssignments e OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="b8aff-199">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="b8aff-200">As propriedades `CourseAssignments` e `OfficeAssignment` são propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="b8aff-200">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="b8aff-201">Um instrutor pode ministrar qualquer quantidade de cursos e, portanto, `CourseAssignments` é definido como uma coleção.</span><span class="sxs-lookup"><span data-stu-id="b8aff-201">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="b8aff-202">Se uma propriedade de navegação pode conter várias entidades, o tipo precisa ser uma lista na qual as entradas podem ser adicionadas, excluídas e atualizadas.</span><span class="sxs-lookup"><span data-stu-id="b8aff-202">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="b8aff-203">Especifique `ICollection<T>` ou um tipo, como `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-203">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="b8aff-204">Se você especificar `ICollection<T>`, o EF criará uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="b8aff-204">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="b8aff-205">O motivo pelo qual elas são entidades `CourseAssignment` é explicado abaixo na seção sobre relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="b8aff-205">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="b8aff-206">As regras de negócio do Contoso Universidade indicam que um instrutor pode ter apenas, no máximo, um escritório; portanto, a propriedade `OfficeAssignment` contém uma única entidade OfficeAssignment (que pode ser nulo se o escritório não está atribuído).</span><span class="sxs-lookup"><span data-stu-id="b8aff-206">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="b8aff-207">Criar a entidade OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="b8aff-207">Create the OfficeAssignment entity</span></span>

![Entidade OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="b8aff-209">Crie *Models/OfficeAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="b8aff-209">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="b8aff-210">O atributo Key</span><span class="sxs-lookup"><span data-stu-id="b8aff-210">The Key attribute</span></span>

<span data-ttu-id="b8aff-211">Há uma relação um para zero ou um entre as entidades Instructor e OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b8aff-211">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="b8aff-212">Uma atribuição de escritório existe apenas em relação ao instrutor ao qual ela é atribuída e, portanto, sua chave primária também é a chave estrangeira da entidade Instructor.</span><span class="sxs-lookup"><span data-stu-id="b8aff-212">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="b8aff-213">Mas o Entity Framework não pode reconhecer InstructorID automaticamente como a chave primária dessa entidade porque o nome não segue a convenção de nomenclatura ID ou classnameID.</span><span class="sxs-lookup"><span data-stu-id="b8aff-213">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="b8aff-214">Portanto, o atributo `Key` é usado para identificá-la como a chave:</span><span class="sxs-lookup"><span data-stu-id="b8aff-214">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="b8aff-215">Você também pode usar o atributo `Key` se a entidade tem sua própria chave primária, mas você deseja atribuir um nome de propriedade que não seja classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="b8aff-215">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="b8aff-216">Por padrão, o EF trata a chave como não gerada pelo banco de dados porque a coluna destina-se a uma relação de identificação.</span><span class="sxs-lookup"><span data-stu-id="b8aff-216">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="b8aff-217">A propriedade de navegação Instructor</span><span class="sxs-lookup"><span data-stu-id="b8aff-217">The Instructor navigation property</span></span>

<span data-ttu-id="b8aff-218">A entidade Instructor tem uma propriedade de navegação `OfficeAssignment` que permite valor nulo (porque um instrutor pode não ter uma atribuição de escritório), e a entidade OfficeAssignment tem uma propriedade de navegação `Instructor` que não permite valor nulo (porque uma atribuição de escritório não pode existir sem um instrutor – `InstructorID` não permite valor nulo).</span><span class="sxs-lookup"><span data-stu-id="b8aff-218">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="b8aff-219">Quando uma entidade Instructor tiver uma entidade OfficeAssignment relacionada, cada entidade terá uma referência à outra em sua propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="b8aff-219">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="b8aff-220">Você pode colocar um atributo `[Required]` na propriedade de navegação Instructor para especificar que deve haver um instrutor relacionado, mas não precisa fazer isso porque a chave estrangeira `InstructorID` (que também é a chave para esta tabela) não permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="b8aff-220">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="b8aff-221">Modificar a entidade Course</span><span class="sxs-lookup"><span data-stu-id="b8aff-221">Modify the Course Entity</span></span>

![Entidade Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="b8aff-223">Em *Models/Course.cs*, substitua o código que você adicionou anteriormente pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="b8aff-223">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="b8aff-224">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="b8aff-224">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="b8aff-225">A entidade de curso tem uma propriedade de chave estrangeira `DepartmentID` que aponta para a entidade Department relacionada e ela tem uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-225">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="b8aff-226">O Entity Framework não exige que você adicione uma propriedade de chave estrangeira ao modelo de dados quando você tem uma propriedade de navegação para uma entidade relacionada.</span><span class="sxs-lookup"><span data-stu-id="b8aff-226">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="b8aff-227">O EF cria chaves estrangeiras no banco de dados sempre que elas são necessárias e cria automaticamente [propriedades de sombra](/ef/core/modeling/shadow-properties) para elas.</span><span class="sxs-lookup"><span data-stu-id="b8aff-227">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="b8aff-228">No entanto, ter a chave estrangeira no modelo de dados pode tornar as atualizações mais simples e mais eficientes.</span><span class="sxs-lookup"><span data-stu-id="b8aff-228">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="b8aff-229">Por exemplo, quando você busca uma entidade de curso a ser editada, a entidade Department é nula se você não carregá-la; portanto, quando você atualiza a entidade de curso, você precisa primeiro buscar a entidade Department.</span><span class="sxs-lookup"><span data-stu-id="b8aff-229">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="b8aff-230">Quando a propriedade de chave estrangeira `DepartmentID` está incluída no modelo de dados, você não precisa buscar a entidade Department antes da atualização.</span><span class="sxs-lookup"><span data-stu-id="b8aff-230">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="b8aff-231">O atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="b8aff-231">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="b8aff-232">O atributo `DatabaseGenerated` com o parâmetro `None` na propriedade `CourseID` especifica que os valores de chave primária são fornecidos pelo usuário, em vez de serem gerados pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-232">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="b8aff-233">Por padrão, o Entity Framework pressupõe que os valores de chave primária sejam gerados pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-233">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="b8aff-234">É isso que você quer na maioria dos cenários.</span><span class="sxs-lookup"><span data-stu-id="b8aff-234">That's what you want in most scenarios.</span></span> <span data-ttu-id="b8aff-235">No entanto, para entidades Course, você usará um número de curso especificado pelo usuário como uma série 1000 de um departamento, uma série 2000 para outro departamento e assim por diante.</span><span class="sxs-lookup"><span data-stu-id="b8aff-235">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="b8aff-236">O atributo `DatabaseGenerated` também pode ser usado para gerar valores padrão, como no caso de colunas de banco de dados usadas para registrar a data em que uma linha foi criada ou atualizada.</span><span class="sxs-lookup"><span data-stu-id="b8aff-236">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="b8aff-237">Para obter mais informações, consulte [Propriedades geradas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="b8aff-237">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b8aff-238">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="b8aff-238">Foreign key and navigation properties</span></span>

<span data-ttu-id="b8aff-239">As propriedades de navegação e de chave estrangeira na entidade Course refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="b8aff-239">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="b8aff-240">Um curso é atribuído a um departamento e, portanto, há uma propriedade de chave estrangeira `DepartmentID` e de navegação `Department` pelas razões mencionadas acima.</span><span class="sxs-lookup"><span data-stu-id="b8aff-240">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="b8aff-241">Um curso pode ter qualquer quantidade de estudantes inscritos; portanto, a propriedade de navegação `Enrollments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="b8aff-241">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="b8aff-242">Um curso pode ser ministrado por vários instrutores; portanto, a propriedade de navegação `CourseAssignments` é uma coleção (o tipo `CourseAssignment` é explicado [posteriormente](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="b8aff-242">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-the-department-entity"></a><span data-ttu-id="b8aff-243">Criar a entidade Department</span><span class="sxs-lookup"><span data-stu-id="b8aff-243">Create the Department entity</span></span>

![Entidade Department](complex-data-model/_static/department-entity.png)


<span data-ttu-id="b8aff-245">Crie *Models/Department.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="b8aff-245">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="b8aff-246">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="b8aff-246">The Column attribute</span></span>

<span data-ttu-id="b8aff-247">Anteriormente, você usou o atributo `Column` para alterar o mapeamento de nome de coluna.</span><span class="sxs-lookup"><span data-stu-id="b8aff-247">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="b8aff-248">No código da entidade Department, o atributo `Column` está sendo usado para alterar o mapeamento de tipo de dados SQL, do modo que a coluna seja definida usando o tipo de dinheiro do SQL Server no banco de dados:</span><span class="sxs-lookup"><span data-stu-id="b8aff-248">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="b8aff-249">O mapeamento de coluna geralmente não é necessário, pois o Entity Framework escolhe o tipo de dados do SQL Server apropriado com base no tipo CLR definido para a propriedade.</span><span class="sxs-lookup"><span data-stu-id="b8aff-249">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="b8aff-250">O tipo `decimal` CLR é mapeado para um tipo `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b8aff-250">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="b8aff-251">Mas, nesse caso, você sabe que a coluna armazenará os valores de moeda e o tipo de dados dinheiro é mais apropriado para isso.</span><span class="sxs-lookup"><span data-stu-id="b8aff-251">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b8aff-252">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="b8aff-252">Foreign key and navigation properties</span></span>

<span data-ttu-id="b8aff-253">As propriedades de navegação e de chave estrangeira refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="b8aff-253">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="b8aff-254">Um departamento pode ou não ter um administrador, e um administrador é sempre um instrutor.</span><span class="sxs-lookup"><span data-stu-id="b8aff-254">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="b8aff-255">Portanto, a propriedade `InstructorID` é incluída como a chave estrangeira na entidade Instructor e um ponto de interrogação é adicionado após a designação de tipo `int` para marcar a propriedade como uma propriedade que permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="b8aff-255">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="b8aff-256">A propriedade de navegação é chamada `Administrator`, mas contém uma entidade Instructor:</span><span class="sxs-lookup"><span data-stu-id="b8aff-256">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="b8aff-257">Um departamento pode ter vários cursos e, portanto, há uma propriedade de navegação Courses:</span><span class="sxs-lookup"><span data-stu-id="b8aff-257">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="b8aff-258">Por convenção, o Entity Framework habilita a exclusão em cascata para chaves estrangeiras que não permitem valor nulo e em relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="b8aff-258">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="b8aff-259">Isso pode resultar em regras de exclusão em cascata circular, que causará uma exceção quando você tentar adicionar uma migração.</span><span class="sxs-lookup"><span data-stu-id="b8aff-259">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="b8aff-260">Por exemplo, se você não definiu a propriedade Department.InstructorID como uma propriedade que permite valor nulo, o EF configura uma regra de exclusão em cascata para excluir o instrutor quando você exclui o departamento, que não é o que você deseja que aconteça.</span><span class="sxs-lookup"><span data-stu-id="b8aff-260">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the instructor when you delete the department, which isn't what you want to have happen.</span></span> <span data-ttu-id="b8aff-261">Se as regras de negócio exigissem que a propriedade `InstructorID` não permitisse valor nulo, você precisaria usar a seguinte instrução de API fluente para desabilitar a exclusão em cascata na relação:</span><span class="sxs-lookup"><span data-stu-id="b8aff-261">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-the-enrollment-entity"></a><span data-ttu-id="b8aff-262">Modificar a entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="b8aff-262">Modify the Enrollment entity</span></span>

![Entidade Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="b8aff-264">Em *Models/Enrollment.cs*, substitua o código que você adicionou anteriormente pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="b8aff-264">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b8aff-265">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="b8aff-265">Foreign key and navigation properties</span></span>

<span data-ttu-id="b8aff-266">As propriedades de navegação e de chave estrangeira refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="b8aff-266">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="b8aff-267">Um registro destina-se a um único curso e, portanto, há uma propriedade de chave estrangeira `CourseID` e uma propriedade de navegação `Course`:</span><span class="sxs-lookup"><span data-stu-id="b8aff-267">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="b8aff-268">Um registro destina-se a um único aluno e, portanto, há uma propriedade de chave estrangeira `StudentID` e uma propriedade de navegação `Student`:</span><span class="sxs-lookup"><span data-stu-id="b8aff-268">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="b8aff-269">Relações muitos para muitos</span><span class="sxs-lookup"><span data-stu-id="b8aff-269">Many-to-Many Relationships</span></span>

<span data-ttu-id="b8aff-270">Há uma relação muitos para muitos entre as entidades Student e Course e a entidade Enrollment funciona como uma tabela de junção muitos para muitos *com conteúdo* no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-270">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="b8aff-271">"Com conteúdo" significa que a tabela Registro contém dados adicionais além das chaves estrangeiras para as tabelas unidas (nesse caso, uma chave primária e uma propriedade Grade).</span><span class="sxs-lookup"><span data-stu-id="b8aff-271">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="b8aff-272">A ilustração a seguir mostra a aparência dessas relações em um diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="b8aff-272">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="b8aff-273">(Esse diagrama foi gerado usando o Entity Framework Power Tools para o EF 6.x; a criação do diagrama não faz parte do tutorial, mas está apenas sendo usada aqui como uma ilustração.)</span><span class="sxs-lookup"><span data-stu-id="b8aff-273">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Relação muitos para muitos de Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="b8aff-275">Cada linha de relação tem um 1 em uma extremidade e um asterisco (\*) na outra, indicando uma relação um para muitos.</span><span class="sxs-lookup"><span data-stu-id="b8aff-275">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="b8aff-276">Se a tabela Registro não incluir informações de nota, ela apenas precisará conter as duas chaves estrangeiras CourseID e StudentID.</span><span class="sxs-lookup"><span data-stu-id="b8aff-276">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="b8aff-277">Nesse caso, ela será uma tabela de junção de muitos para muitos sem conteúdo (ou uma tabela de junção pura) no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-277">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="b8aff-278">As entidades Instructor e Course têm esse tipo de relação muitos para muitos e a próxima etapa é criar uma classe de entidade para funcionar como uma tabela de junção sem conteúdo.</span><span class="sxs-lookup"><span data-stu-id="b8aff-278">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="b8aff-279">(O EF 6.x é compatível com tabelas de junção implícita para relações muitos para muitos, ao contrário do EF Core.</span><span class="sxs-lookup"><span data-stu-id="b8aff-279">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="b8aff-280">Para obter mais informações, confira a [discussão no repositório GitHub do EF Core](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="b8aff-280">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="b8aff-281">A entidade CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="b8aff-281">The CourseAssignment entity</span></span>

![Entidade CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="b8aff-283">Crie *Models/CourseAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="b8aff-283">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="b8aff-284">Unir nomes de entidade</span><span class="sxs-lookup"><span data-stu-id="b8aff-284">Join entity names</span></span>

<span data-ttu-id="b8aff-285">Uma tabela de junção é necessária no banco de dados para a relação muitos para muitos de Instrutor para Cursos, e ela precisa ser representada por um conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b8aff-285">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="b8aff-286">É comum nomear uma entidade de junção `EntityName1EntityName2`, que, nesse caso, será `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-286">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="b8aff-287">No entanto, recomendamos que você escolha um nome que descreve a relação.</span><span class="sxs-lookup"><span data-stu-id="b8aff-287">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="b8aff-288">Modelos de dados começam simples e aumentam, com junções não referentes a conteúdo obtendo com frequência o conteúdo mais tarde.</span><span class="sxs-lookup"><span data-stu-id="b8aff-288">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="b8aff-289">Se você começar com um nome descritivo de entidade, não precisará alterar o nome posteriormente.</span><span class="sxs-lookup"><span data-stu-id="b8aff-289">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="b8aff-290">O ideal é que a entidade de junção tenha seu próprio nome natural (possivelmente, uma única palavra) no domínio de negócios.</span><span class="sxs-lookup"><span data-stu-id="b8aff-290">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="b8aff-291">Por exemplo, Manuais e Clientes podem ser vinculados por meio de Classificações.</span><span class="sxs-lookup"><span data-stu-id="b8aff-291">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="b8aff-292">Para essa relação, `CourseAssignment` é uma escolha melhor que `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-292">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="b8aff-293">Chave composta</span><span class="sxs-lookup"><span data-stu-id="b8aff-293">Composite key</span></span>

<span data-ttu-id="b8aff-294">Como as chaves estrangeiras não permitem valor nulo e, juntas, identificam exclusivamente cada linha da tabela, não é necessário ter uma chave primária.</span><span class="sxs-lookup"><span data-stu-id="b8aff-294">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="b8aff-295">As propriedades *InstructorID* e *CourseID* devem funcionar como uma chave primária composta.</span><span class="sxs-lookup"><span data-stu-id="b8aff-295">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="b8aff-296">A única maneira de identificar chaves primárias compostas no EF é usando a *API fluente* (isso não pode ser feito por meio de atributos).</span><span class="sxs-lookup"><span data-stu-id="b8aff-296">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="b8aff-297">Você verá como configurar a chave primária composta na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="b8aff-297">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="b8aff-298">A chave composta garante que, embora você possa ter várias linhas para um curso e várias linhas para um instrutor, não poderá ter várias linhas para o mesmo instrutor e curso.</span><span class="sxs-lookup"><span data-stu-id="b8aff-298">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="b8aff-299">A entidade de junção `Enrollment` define sua própria chave primária e, portanto, duplicatas desse tipo são possíveis.</span><span class="sxs-lookup"><span data-stu-id="b8aff-299">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="b8aff-300">Para evitar essas duplicatas, você pode adicionar um índice exclusivo nos campos de chave estrangeira ou configurar `Enrollment` com uma chave primária composta semelhante a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-300">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="b8aff-301">Para obter mais informações, consulte [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="b8aff-301">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="b8aff-302">Atualizar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="b8aff-302">Update the database context</span></span>

<span data-ttu-id="b8aff-303">Adicione o seguinte código realçado ao arquivo *Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8aff-303">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="b8aff-304">Esse código adiciona novas entidades e configura a chave primária composta da entidade CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="b8aff-304">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="b8aff-305">Alternativa de API fluente para atributos</span><span class="sxs-lookup"><span data-stu-id="b8aff-305">Fluent API alternative to attributes</span></span>

<span data-ttu-id="b8aff-306">O código no método `OnModelCreating` da classe `DbContext` usa a *API fluente* para configurar o comportamento do EF.</span><span class="sxs-lookup"><span data-stu-id="b8aff-306">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="b8aff-307">A API é chamada "fluente" porque costuma ser usada pelo encadeamento de uma série de chamadas de método em uma única instrução, como neste exemplo da [documentação do EF Core](/ef/core/modeling/#methods-of-configuration):</span><span class="sxs-lookup"><span data-stu-id="b8aff-307">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#methods-of-configuration):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="b8aff-308">Neste tutorial, você usa a API fluente somente para o mapeamento de banco de dados que não é possível fazer com atributos.</span><span class="sxs-lookup"><span data-stu-id="b8aff-308">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="b8aff-309">No entanto, você também pode usar a API fluente para especificar a maioria das regras de formatação, validação e mapeamento que pode ser feita por meio de atributos.</span><span class="sxs-lookup"><span data-stu-id="b8aff-309">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="b8aff-310">Alguns atributos como `MinimumLength` não podem ser aplicados com a API fluente.</span><span class="sxs-lookup"><span data-stu-id="b8aff-310">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="b8aff-311">Conforme mencionado anteriormente, `MinimumLength` não altera o esquema; apenas aplica uma regra de validação do lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="b8aff-311">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="b8aff-312">Alguns desenvolvedores preferem usar a API fluente exclusivamente para que possam manter suas classes de entidade "limpas".</span><span class="sxs-lookup"><span data-stu-id="b8aff-312">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="b8aff-313">Combine atributos e a API fluente se desejar. Além disso, há algumas personalizações que podem ser feitas apenas com a API fluente, mas em geral, a prática recomendada é escolher uma dessas duas abordagens e usar isso com o máximo de consistência possível.</span><span class="sxs-lookup"><span data-stu-id="b8aff-313">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="b8aff-314">Se usar as duas, observe que sempre que houver um conflito, a API fluente substituirá atributos.</span><span class="sxs-lookup"><span data-stu-id="b8aff-314">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="b8aff-315">Para obter mais informações sobre atributos vs. API fluente, consulte [Métodos de configuração](/ef/core/modeling/#methods-of-configuration).</span><span class="sxs-lookup"><span data-stu-id="b8aff-315">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="b8aff-316">Diagrama de entidade mostrando relações</span><span class="sxs-lookup"><span data-stu-id="b8aff-316">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="b8aff-317">A ilustração a seguir mostra o diagrama criado pelo Entity Framework Power Tools para o modelo Escola concluído.</span><span class="sxs-lookup"><span data-stu-id="b8aff-317">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="b8aff-319">Além das linhas de relação um-para-muitos (1 para \*), você pode ver a linha de relação um para zero ou um (1 para 0..1) entre as entidades Instructor e OfficeAssignment e a linha de relação zero-ou-um-para-muitos (0..1 para \*) entre as entidades Instructor e Department.</span><span class="sxs-lookup"><span data-stu-id="b8aff-319">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-the-database-with-test-data"></a><span data-ttu-id="b8aff-320">Propagar o banco de dados com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="b8aff-320">Seed the Database with Test Data</span></span>

<span data-ttu-id="b8aff-321">Substitua o código no arquivo *Data/DbInitializer.cs* pelo código a seguir para fornecer dados de semente para as novas entidades criadas.</span><span class="sxs-lookup"><span data-stu-id="b8aff-321">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="b8aff-322">Como você viu no primeiro tutorial, a maioria do código apenas cria novos objetos de entidade e carrega dados de exemplo em propriedades, conforme necessário, para teste.</span><span class="sxs-lookup"><span data-stu-id="b8aff-322">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="b8aff-323">Observe como as relações muitos para muitos são tratadas: o código cria relações com a criação de entidades nos conjuntos de entidades de junção `Enrollments` e `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-323">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="b8aff-324">Adicionar uma migração</span><span class="sxs-lookup"><span data-stu-id="b8aff-324">Add a migration</span></span>

<span data-ttu-id="b8aff-325">Salve as alterações e compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="b8aff-325">Save your changes and build the project.</span></span> <span data-ttu-id="b8aff-326">Em seguida, abra a janela Comando na pasta do projeto e insira o comando `migrations add` (não execute ainda o comando de atualização de banco de dados):</span><span class="sxs-lookup"><span data-stu-id="b8aff-326">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```console
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="b8aff-327">Você receberá um aviso sobre a possível perda de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-327">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="b8aff-328">Se você tiver tentado executar o comando `database update` neste ponto (não faça isso ainda), receberá o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="b8aff-328">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="b8aff-329">A instrução ALTER TABLE entrou em conflito com a restrição FOREIGN KEY "FK_dbo.Course_dbo.Department_DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="b8aff-329">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="b8aff-330">O conflito ocorreu no banco de dados "ContosoUniversity", tabela "dbo.Departamento", coluna 'DepartmentID'.</span><span class="sxs-lookup"><span data-stu-id="b8aff-330">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="b8aff-331">Às vezes, quando você executa migrações com os dados existentes, precisa inserir dados de stub no banco de dados para atender às restrições de chave estrangeira.</span><span class="sxs-lookup"><span data-stu-id="b8aff-331">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="b8aff-332">O código gerado no método `Up` adiciona uma chave estrangeira DepartmentID que não permite valor nulo para a tabela Curso.</span><span class="sxs-lookup"><span data-stu-id="b8aff-332">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="b8aff-333">Se já houver linhas na tabela Curso quando o código for executado, a operação `AddColumn` falhará, porque o SQL Server não saberá qual valor deve ser colocado na coluna que não pode ser nulo.</span><span class="sxs-lookup"><span data-stu-id="b8aff-333">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="b8aff-334">Para este tutorial, você executará a migração em um novo banco de dados, mas em um aplicativo de produção, você precisará fazer com que a migração manipule os dados existentes. Portanto, as instruções a seguir mostram um exemplo de como fazer isso.</span><span class="sxs-lookup"><span data-stu-id="b8aff-334">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="b8aff-335">Para fazer a migração funcionar com os dados existentes, você precisa alterar o código para fornecer à nova coluna um valor padrão e criar um departamento de stub chamado "Temp" para atuar como o departamento padrão.</span><span class="sxs-lookup"><span data-stu-id="b8aff-335">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="b8aff-336">Como resultado, as linhas Curso existentes serão todas relacionadas ao departamento "Temp" após a execução do método `Up`.</span><span class="sxs-lookup"><span data-stu-id="b8aff-336">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="b8aff-337">Abra o arquivo *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="b8aff-337">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="b8aff-338">Comente a linha de código que adiciona a coluna DepartmentID à tabela Curso.</span><span class="sxs-lookup"><span data-stu-id="b8aff-338">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="b8aff-339">Adicione o seguinte código realçado após o código que cria a tabela Departamento:</span><span class="sxs-lookup"><span data-stu-id="b8aff-339">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="b8aff-340">Em um aplicativo de produção, você escreverá código ou scripts para adicionar linhas Departamento e relacionar linhas Curso às novas linhas Departamento.</span><span class="sxs-lookup"><span data-stu-id="b8aff-340">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="b8aff-341">Em seguida, você não precisará mais do departamento "Temp" ou do valor padrão na coluna Course.DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="b8aff-341">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="b8aff-342">Salve as alterações e compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="b8aff-342">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string-and-update-the-database"></a><span data-ttu-id="b8aff-343">Alterar a cadeia de conexão e atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="b8aff-343">Change the connection string and update the database</span></span>

<span data-ttu-id="b8aff-344">Agora, você tem novo código na classe `DbInitializer` que adiciona dados de semente para as novas entidades a um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="b8aff-344">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="b8aff-345">Para fazer com que o EF crie um novo banco de dados vazio, altere o nome do banco de dados na cadeia de conexão em *appsettings.json* para ContosoUniversity3 ou para outro nome que você ainda não usou no computador que está sendo usado.</span><span class="sxs-lookup"><span data-stu-id="b8aff-345">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="b8aff-346">Salve as alterações em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b8aff-346">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="b8aff-347">Como alternativa à alteração do nome do banco de dados, você pode excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-347">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="b8aff-348">Use o **SSOX** (Pesquisador de Objetos do SQL Server) ou o comando `database drop` da CLI:</span><span class="sxs-lookup"><span data-stu-id="b8aff-348">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```

<span data-ttu-id="b8aff-349">Depois que você tiver alterado o nome do banco de dados ou excluído o banco de dados, execute o comando `database update` na janela Comando para executar as migrações.</span><span class="sxs-lookup"><span data-stu-id="b8aff-349">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="b8aff-350">Execute o aplicativo para fazer com que o método `DbInitializer.Initialize` execute e popule o novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-350">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="b8aff-351">Abra o banco de dados no SSOX, como você fez anteriormente, e expanda o nó **Tabelas** para ver se todas as tabelas foram criadas.</span><span class="sxs-lookup"><span data-stu-id="b8aff-351">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="b8aff-352">(Se você ainda tem o SSOX aberto do momento anterior, clique no botão **Atualizar**.)</span><span class="sxs-lookup"><span data-stu-id="b8aff-352">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Tabelas no SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="b8aff-354">Execute o aplicativo para disparar o código inicializador que propaga o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-354">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="b8aff-355">Clique com o botão direito do mouse na tabela **CourseAssignment** e selecione **Exibir Dados** para verificar se existem dados nela.</span><span class="sxs-lookup"><span data-stu-id="b8aff-355">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![Dados de CourseAssignment no SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="summary"></a><span data-ttu-id="b8aff-357">Resumo</span><span class="sxs-lookup"><span data-stu-id="b8aff-357">Summary</span></span>

<span data-ttu-id="b8aff-358">Agora você tem um modelo de dados mais complexo e um banco de dados correspondente.</span><span class="sxs-lookup"><span data-stu-id="b8aff-358">You now have a more complex data model and corresponding database.</span></span> <span data-ttu-id="b8aff-359">No tutorial a seguir, você aprenderá mais sobre como acessar dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="b8aff-359">In the following tutorial, you'll learn more about how to access related data.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="b8aff-360">[Anterior](migrations.md)
> [Próximo](read-related-data.md)</span><span class="sxs-lookup"><span data-stu-id="b8aff-360">[Previous](migrations.md)
[Next](read-related-data.md)</span></span>
