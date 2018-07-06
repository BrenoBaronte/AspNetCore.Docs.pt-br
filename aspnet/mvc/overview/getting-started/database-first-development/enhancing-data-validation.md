---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: 'Banco de dados do EF primeiro com o ASP.NET MVC: Aprimorando a validação de dados | Microsoft Docs'
author: tfitzmac
description: Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente. Esta série de tutoriais...
ms.author: aspnetcontent
ms.date: 12/29/2014
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: 9a7c6e200caa72aea61a80d6496ec1a1569e5e48
ms.sourcegitcommit: b28cd0313af316c051c2ff8549865bff67f2fbb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37816312"
---
<a name="ef-database-first-with-aspnet-mvc-enhancing-data-validation"></a><span data-ttu-id="ada76-104">Banco de dados do EF primeiro com o ASP.NET MVC: Aprimorando a validação de dados</span><span class="sxs-lookup"><span data-stu-id="ada76-104">EF Database First with ASP.NET MVC: Enhancing Data Validation</span></span>
====================
<span data-ttu-id="ada76-105">por [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="ada76-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="ada76-106">Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="ada76-106">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="ada76-107">Esta série de tutoriais mostra como automaticamente gerar um código que permite aos usuários exibir, editar, criar e excluir dados que residem em uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ada76-107">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="ada76-108">O código gerado corresponde às colunas na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ada76-108">The generated code corresponds to the columns in the database table.</span></span>
> 
> <span data-ttu-id="ada76-109">Esta parte da série se concentra em Adicionar anotações de dados para o modelo de dados para especificar os requisitos de validação e formatação de exibição.</span><span class="sxs-lookup"><span data-stu-id="ada76-109">This part of the series focuses on adding data annotations to the data model to specify validation requirements and display formatting.</span></span> <span data-ttu-id="ada76-110">Ele foi aprimorado com base nos comentários dos usuários na seção de comentários.</span><span class="sxs-lookup"><span data-stu-id="ada76-110">It was improved based on feedback from users in the comments section.</span></span>


## <a name="add-data-annotations"></a><span data-ttu-id="ada76-111">Adicionar anotações de dados</span><span class="sxs-lookup"><span data-stu-id="ada76-111">Add data annotations</span></span>

<span data-ttu-id="ada76-112">Como você viu em um tópico anterior, algumas regras de validação de dados são aplicadas automaticamente para a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="ada76-112">As you saw in an earlier topic, some data validation rules are automatically applied to the user input.</span></span> <span data-ttu-id="ada76-113">Por exemplo, você só pode fornecer um número para a propriedade de nível empresarial.</span><span class="sxs-lookup"><span data-stu-id="ada76-113">For example, you can only provide a number for the Grade property.</span></span> <span data-ttu-id="ada76-114">Para especificar mais regras de validação de dados, você pode adicionar anotações de dados à sua classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="ada76-114">To specify more data validation rules, you can add data annotations to your model class.</span></span> <span data-ttu-id="ada76-115">Essas anotações são aplicadas em todo o seu aplicativo web para a propriedade especificada.</span><span class="sxs-lookup"><span data-stu-id="ada76-115">These annotations are applied throughout your web application for the specified property.</span></span> <span data-ttu-id="ada76-116">Você também pode aplicar atributos de formatação que alterar como as propriedades são exibidas; Por exemplo, alterando o valor usado para rótulos de texto.</span><span class="sxs-lookup"><span data-stu-id="ada76-116">You can also apply formatting attributes that change how the properties are displayed; such as, changing the value used for text labels.</span></span>

<span data-ttu-id="ada76-117">Neste tutorial, você irá adicionar anotações de dados para restringir o tamanho dos valores fornecidos para as propriedades FirstName, LastName e MiddleName.</span><span class="sxs-lookup"><span data-stu-id="ada76-117">In this tutorial, you will add data annotations to restrict the length of the values provided for the FirstName, LastName, and MiddleName properties.</span></span> <span data-ttu-id="ada76-118">No banco de dados, esses valores são limitados a 50 caracteres. No entanto, em seu aplicativo web que limite de caracteres no momento, não é imposta.</span><span class="sxs-lookup"><span data-stu-id="ada76-118">In the database, these values are limited to 50 characters; however, in your web application that character limit is currently not enforced.</span></span> <span data-ttu-id="ada76-119">Se um usuário fornece mais de 50 caracteres para um desses valores, a página falhará ao tentar salvar o valor para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ada76-119">If a user provides more than 50 characters for one of those values, the page will crash when attempting to save the value to the database.</span></span> <span data-ttu-id="ada76-120">Também você restringirá a nível empresarial para valores entre 0 e 4.</span><span class="sxs-lookup"><span data-stu-id="ada76-120">You will also restrict Grade to values between 0 and 4.</span></span>

<span data-ttu-id="ada76-121">Abra o **Student.cs** arquivo na **modelos** pasta.</span><span class="sxs-lookup"><span data-stu-id="ada76-121">Open the **Student.cs** file in the **Models** folder.</span></span> <span data-ttu-id="ada76-122">Adicione o seguinte código realçado à classe.</span><span class="sxs-lookup"><span data-stu-id="ada76-122">Add the following highlighted code to the class.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

<span data-ttu-id="ada76-123">No Enrollment.cs, adicione o seguinte código realçado.</span><span class="sxs-lookup"><span data-stu-id="ada76-123">In Enrollment.cs, add the following highlighted code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

<span data-ttu-id="ada76-124">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="ada76-124">Build the solution.</span></span>

<span data-ttu-id="ada76-125">Navegue até uma página para editar ou criar um aluno.</span><span class="sxs-lookup"><span data-stu-id="ada76-125">Browse to a page for editing or creating a student.</span></span> <span data-ttu-id="ada76-126">Se você tentar inserir mais de 50 caracteres, uma mensagem de erro será exibida.</span><span class="sxs-lookup"><span data-stu-id="ada76-126">If you attempt to enter more than 50 characters, an error message is displayed.</span></span>

![Mostrar mensagem de erro](enhancing-data-validation/_static/image1.png)

<span data-ttu-id="ada76-128">Navegue até a página para edição de registros e tenta fornecer um nível superior a 4.</span><span class="sxs-lookup"><span data-stu-id="ada76-128">Browse to the page for editing enrollments, and attempt to provide a grade above 4.</span></span>

![Erro de intervalo de nível empresarial](enhancing-data-validation/_static/image2.png)

<span data-ttu-id="ada76-130">Para obter uma lista completa de anotações de validação de dados, você pode aplicar a classes e propriedades, consulte [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span><span class="sxs-lookup"><span data-stu-id="ada76-130">For a full list of data validation annotations you can apply to properties and classes, see [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span></span>

## <a name="add-metadata-classes"></a><span data-ttu-id="ada76-131">Adicionar classes de metadados</span><span class="sxs-lookup"><span data-stu-id="ada76-131">Add metadata classes</span></span>

<span data-ttu-id="ada76-132">Adicionar os atributos de validação diretamente para a classe de modelo funciona quando você não espera que o banco de dados a ser alterado; No entanto, se as alterações de banco de dados e você precisar regenerar a classe de modelo, você perderá todos os atributos que você tivesse aplicado à classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="ada76-132">Adding the validation attributes directly to the model class works when you do not expect the database to change; however, if your database changes and you need to regenerate the model class, you will lose all of the attributes you had applied to the model class.</span></span> <span data-ttu-id="ada76-133">Essa abordagem pode ser muito ineficiente e propenso a perder as regras de validação importantes.</span><span class="sxs-lookup"><span data-stu-id="ada76-133">This approach can be very inefficient and prone to losing important validation rules.</span></span>

<span data-ttu-id="ada76-134">Para evitar esse problema, você pode adicionar uma classe de metadados que contém os atributos.</span><span class="sxs-lookup"><span data-stu-id="ada76-134">To avoid this problem, you can add a metadata class that contains the attributes.</span></span> <span data-ttu-id="ada76-135">Quando você associa a classe de modelo para a classe de metadados, esses atributos são aplicados ao modelo.</span><span class="sxs-lookup"><span data-stu-id="ada76-135">When you associate the model class to the metadata class, those attributes are applied to the model.</span></span> <span data-ttu-id="ada76-136">Nessa abordagem, a classe de modelo pode ser regenerada sem perder todos os atributos que foram aplicados à classe de metadados.</span><span class="sxs-lookup"><span data-stu-id="ada76-136">In this approach, the model class can be regenerated without losing all of the attributes that have been applied to the metadata class.</span></span>

<span data-ttu-id="ada76-137">No **modelos** pasta, adicione uma classe chamada **Metadata.cs**.</span><span class="sxs-lookup"><span data-stu-id="ada76-137">In the **Models** folder, add a class named **Metadata.cs**.</span></span>

![Adicionar classe de metadados](enhancing-data-validation/_static/image3.png)

<span data-ttu-id="ada76-139">Substitua o código em Metadata.cs com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="ada76-139">Replace the code in Metadata.cs with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

<span data-ttu-id="ada76-140">Essas classes de metadados contêm todos os atributos de validação aplicados anteriormente para as classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="ada76-140">These metadata classes contain all of the validation attributes that you had previously applied to the model classes.</span></span> <span data-ttu-id="ada76-141">O **exibição** atributo é usado para alterar o valor usado para rótulos de texto.</span><span class="sxs-lookup"><span data-stu-id="ada76-141">The **Display** attribute is used to change the value used for text labels.</span></span>

<span data-ttu-id="ada76-142">Agora, você deve associar as classes de modelo com as classes de metadados.</span><span class="sxs-lookup"><span data-stu-id="ada76-142">Now, you must associate the model classes with the metadata classes.</span></span>

<span data-ttu-id="ada76-143">No **modelos** pasta, adicione uma classe chamada **PartialClasses.cs**.</span><span class="sxs-lookup"><span data-stu-id="ada76-143">In the **Models** folder, add a class named **PartialClasses.cs**.</span></span>

<span data-ttu-id="ada76-144">Substitua o conteúdo do arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="ada76-144">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

<span data-ttu-id="ada76-145">Observe que cada classe é marcada como um `partial` classe e cada uma corresponde ao nome e namespace como a classe que é gerada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="ada76-145">Notice that each class is marked as a `partial` class, and each matches the name and namespace as the class that is automatically generated.</span></span> <span data-ttu-id="ada76-146">Aplicando o atributo de metadados para a classe parcial, você certifique-se de que os atributos de validação de dados serão aplicados à classe gerada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="ada76-146">By applying the metadata attribute to the partial class, you ensure that the data validation attributes will be applied to the automatically-generated class.</span></span> <span data-ttu-id="ada76-147">Esses atributos não serão perdidos quando você regenerar as classes de modelo porque o atributo de metadados é aplicado em classes parciais que não serão regeneradas.</span><span class="sxs-lookup"><span data-stu-id="ada76-147">These attributes will not be lost when you regenerate the model classes because the metadata attribute is applied in partial classes that are not regenerated.</span></span>

<span data-ttu-id="ada76-148">Para regenerar as classes geradas automaticamente, abra o arquivo de ContosoModel.edmx.</span><span class="sxs-lookup"><span data-stu-id="ada76-148">To regenerate the automatically-generated classes, open the ContosoModel.edmx file.</span></span> <span data-ttu-id="ada76-149">Mais uma vez, com o botão direito na superfície do design e selecione **modelo de atualização do banco de dados**.</span><span class="sxs-lookup"><span data-stu-id="ada76-149">Once again, right-click on the design surface and select **Update Model from Database**.</span></span> <span data-ttu-id="ada76-150">Embora você não alterou o banco de dados, esse processo regenerará as classes.</span><span class="sxs-lookup"><span data-stu-id="ada76-150">Even though you have not changed the database, this process will regenerate the classes.</span></span> <span data-ttu-id="ada76-151">No **Refresh** guia, selecione **tabelas** e **concluir**.</span><span class="sxs-lookup"><span data-stu-id="ada76-151">In the **Refresh** tab, select **Tables** and **Finish**.</span></span>

![Atualizar tabelas](enhancing-data-validation/_static/image4.png)

<span data-ttu-id="ada76-153">Salve o arquivo ContosoModel.edmx para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="ada76-153">Save the ContosoModel.edmx file to apply the changes.</span></span>

<span data-ttu-id="ada76-154">Abra o arquivo de Student.cs ou o arquivo Enrollment.cs e observe que os atributos de validação de dados que você aplicou anteriormente não estão mais no arquivo.</span><span class="sxs-lookup"><span data-stu-id="ada76-154">Open the Student.cs file or the Enrollment.cs file, and notice that the data validation attributes you applied earlier are no longer in the file.</span></span> <span data-ttu-id="ada76-155">No entanto, executar o aplicativo e observe que as regras de validação ainda são aplicadas quando você insere dados.</span><span class="sxs-lookup"><span data-stu-id="ada76-155">However, run the application, and notice that the validation rules are still applied when you enter data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ada76-156">[Anterior](customizing-a-view.md)
> [Próximo](publish-to-azure.md)</span><span class="sxs-lookup"><span data-stu-id="ada76-156">[Previous](customizing-a-view.md)
[Next](publish-to-azure.md)</span></span>
