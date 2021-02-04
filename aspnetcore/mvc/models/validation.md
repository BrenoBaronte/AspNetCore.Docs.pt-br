---
title: Validação de modelo no ASP.NET Core MVC
author: rick-anderson
description: Saiba mais sobre a validação de modelo em ASP.NET Core MVC e Razor páginas.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: d6fa7e4524a8afdc23d4ad46354d9d8b395656a3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530184"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="9212a-103">Validação de modelo no ASP.NET Core MVC e Razor páginas</span><span class="sxs-lookup"><span data-stu-id="9212a-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9212a-104">Por [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="9212a-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="9212a-105">Este artigo explica como validar a entrada do usuário em um aplicativo ASP.NET Core MVC ou Razor pages.</span><span class="sxs-lookup"><span data-stu-id="9212a-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="9212a-106">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9212a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="9212a-107">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="9212a-107">Model state</span></span>

<span data-ttu-id="9212a-108">O estado do modelo representa erros que vêm de dois subsistemas: model binding e validação de modelo.</span><span class="sxs-lookup"><span data-stu-id="9212a-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="9212a-109">Os erros originados na [Associação de modelo](model-binding.md) geralmente são erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="9212a-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="9212a-110">Por exemplo, um "x" é inserido em um campo de número inteiro.</span><span class="sxs-lookup"><span data-stu-id="9212a-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="9212a-111">A validação do modelo ocorre após a associação de modelo e relata erros em que os dados não estão em conformidade com as regras de negócio.</span><span class="sxs-lookup"><span data-stu-id="9212a-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="9212a-112">Por exemplo, um 0 é inserido em um campo que espera uma classificação entre 1 e 5.</span><span class="sxs-lookup"><span data-stu-id="9212a-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="9212a-113">A associação de modelo e a validação de modelo ocorrem antes da execução de uma ação de controlador ou um Razor método de manipulador de páginas.</span><span class="sxs-lookup"><span data-stu-id="9212a-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="9212a-114">Nos aplicativos Web, é responsabilidade do aplicativo inspecionar `ModelState.IsValid` e reagir adequadamente.</span><span class="sxs-lookup"><span data-stu-id="9212a-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="9212a-115">Geralmente, os aplicativos Web reexibem a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="9212a-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="9212a-116">Os controladores de API Web não precisarão verificar `ModelState.IsValid` se eles tiverem o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="9212a-117">Nesse caso, uma resposta HTTP 400 automática contendo detalhes do erro é retornada quando o estado do modelo é inválido.</span><span class="sxs-lookup"><span data-stu-id="9212a-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="9212a-118">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="9212a-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="9212a-119">Executar validação novamente</span><span class="sxs-lookup"><span data-stu-id="9212a-119">Rerun validation</span></span>

<span data-ttu-id="9212a-120">A validação é automática, mas talvez seja necessário repeti-la manualmente.</span><span class="sxs-lookup"><span data-stu-id="9212a-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="9212a-121">Por exemplo, você pode calcular um valor para uma propriedade e executar novamente a validação após a configuração da propriedade com o valor computado.</span><span class="sxs-lookup"><span data-stu-id="9212a-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="9212a-122">Para reexecutar a validação, chame o método `TryValidateModel`, conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="9212a-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="9212a-123">Atributos de validação</span><span class="sxs-lookup"><span data-stu-id="9212a-123">Validation attributes</span></span>

<span data-ttu-id="9212a-124">Os atributos de validação permitem que você especifique regras de validação para propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="9212a-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="9212a-125">O exemplo a seguir do aplicativo de exemplo mostra uma classe de modelo que é anotada com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="9212a-126">O atributo `[ClassicMovie]` é um atributo de validação personalizado e os outros são atributos internos.</span><span class="sxs-lookup"><span data-stu-id="9212a-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="9212a-127">Não mostrado é `[ClassicMovieWithClientValidator]` .</span><span class="sxs-lookup"><span data-stu-id="9212a-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="9212a-128">`[ClassicMovieWithClientValidator]` mostra uma maneira alternativa de implementar um atributo personalizado.</span><span class="sxs-lookup"><span data-stu-id="9212a-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="9212a-129">Atributos internos</span><span class="sxs-lookup"><span data-stu-id="9212a-129">Built-in attributes</span></span>

<span data-ttu-id="9212a-130">Aqui estão alguns dos atributos de validação internos:</span><span class="sxs-lookup"><span data-stu-id="9212a-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="9212a-131">`[CreditCard]`: Valida que a propriedade tem um formato de cartão de crédito.</span><span class="sxs-lookup"><span data-stu-id="9212a-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="9212a-132">Requer [métodos adicionais de validação jQuery](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span><span class="sxs-lookup"><span data-stu-id="9212a-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="9212a-133">`[Compare]`: Valida que duas propriedades em um modelo correspondem.</span><span class="sxs-lookup"><span data-stu-id="9212a-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="9212a-134">`[EmailAddress]`: Valida que a propriedade tem um formato de email.</span><span class="sxs-lookup"><span data-stu-id="9212a-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="9212a-135">`[Phone]`: Valida que a propriedade tem um formato de número de telefone.</span><span class="sxs-lookup"><span data-stu-id="9212a-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="9212a-136">`[Range]`: Valida que o valor da propriedade cai em um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="9212a-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="9212a-137">`[RegularExpression]`: Valida que o valor da propriedade corresponde a uma expressão regular especificada.</span><span class="sxs-lookup"><span data-stu-id="9212a-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="9212a-138">`[Required]`: Valida que o campo não é nulo.</span><span class="sxs-lookup"><span data-stu-id="9212a-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="9212a-139">Consulte o [ `[Required]` atributo](#required-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="9212a-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="9212a-140">`[StringLength]`: Valida que um valor de propriedade de cadeia de caracteres não excede um limite de comprimento especificado.</span><span class="sxs-lookup"><span data-stu-id="9212a-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="9212a-141">`[Url]`: Valida que a propriedade tem um formato de URL.</span><span class="sxs-lookup"><span data-stu-id="9212a-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="9212a-142">`[Remote]`: Valida a entrada no cliente chamando um método de ação no servidor.</span><span class="sxs-lookup"><span data-stu-id="9212a-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="9212a-143">Consulte o [ `[Remote]` atributo](#remote-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="9212a-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="9212a-144">Uma lista completa de atributos de validação pode ser encontrada no namespace [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9212a-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="9212a-145">Mensagens de erro</span><span class="sxs-lookup"><span data-stu-id="9212a-145">Error messages</span></span>

<span data-ttu-id="9212a-146">Os atributos de validação permitem que você especifique a mensagem de erro a ser exibido para uma entrada inválida.</span><span class="sxs-lookup"><span data-stu-id="9212a-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="9212a-147">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="9212a-148">Internamente, a chamada de atributos `String.Format` com um espaço reservado para o nome do campo e, às vezes, espaços reservados adicionais.</span><span class="sxs-lookup"><span data-stu-id="9212a-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="9212a-149">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="9212a-150">Quando aplicada a uma propriedade `Name`, a mensagem de erro criada pelo código anterior seria "Comprimento do nome ter entre 6 e 8.".</span><span class="sxs-lookup"><span data-stu-id="9212a-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="9212a-151">Para descobrir quais parâmetros são passados para `String.Format` no caso de uma mensagem de erro de um atributo específico, consulte o [código-fonte de DataAnnotations](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9212a-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="non-nullable-reference-types-and-required-attribute"></a><span data-ttu-id="9212a-152">Tipos de referência não anuláveis e atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="9212a-152">Non-nullable reference types and [Required] attribute</span></span>

<span data-ttu-id="9212a-153">O sistema de validação trata parâmetros não anuláveis ou propriedades associadas como se tivessem um `[Required]` atributo.</span><span class="sxs-lookup"><span data-stu-id="9212a-153">The validation system treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="9212a-154">Ao [habilitar `Nullable` contextos](/dotnet/csharp/nullable-references#nullable-contexts), o MVC inicia implicitamente a validação de propriedades ou parâmetros não anuláveis como se eles tivessem sido atribuídos com o `[Required]` atributo.</span><span class="sxs-lookup"><span data-stu-id="9212a-154">By [enabling `Nullable` contexts](/dotnet/csharp/nullable-references#nullable-contexts), MVC implicitly starts validating non-nullable properties or parameters as if they had been attributed with the `[Required]` attribute.</span></span> <span data-ttu-id="9212a-155">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="9212a-155">Consider the following code:</span></span>

```csharp
public class Person
{
    public string Name { get; set; }
}
```

<span data-ttu-id="9212a-156">Se o aplicativo foi criado com `<Nullable>enable</Nullable>` , um valor ausente para `Name` em uma postagem JSON ou de formulário resultará em um erro de validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-156">If the app was built with `<Nullable>enable</Nullable>`, a missing value for `Name` in a JSON or form post results in a validation error.</span></span> <span data-ttu-id="9212a-157">Use um tipo de referência anulável para permitir que valores nulos ou ausentes sejam especificados para a `Name` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="9212a-157">Use a nullable reference type to allow null or missing values to be specified for the `Name` property:</span></span>

```csharp
public class Person
{
    public string? Name { get; set; }
}
```

<span data-ttu-id="9212a-158">.</span><span class="sxs-lookup"><span data-stu-id="9212a-158">.</span></span> <span data-ttu-id="9212a-159">Esse comportamento pode ser desabilitado com a configuração <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9212a-159">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="9212a-160">Validação de [Required] no servidor</span><span class="sxs-lookup"><span data-stu-id="9212a-160">[Required] validation on the server</span></span>

<span data-ttu-id="9212a-161">No servidor, um valor obrigatório será considerado ausente se a propriedade for nula.</span><span class="sxs-lookup"><span data-stu-id="9212a-161">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="9212a-162">Um campo não anulável é sempre válido e a mensagem de `[Required]` erro do atributo nunca é exibida.</span><span class="sxs-lookup"><span data-stu-id="9212a-162">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="9212a-163">No entanto, o model binding para uma propriedade que não permite valor nulo pode falhar, resultando em uma mensagem de erro como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="9212a-163">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="9212a-164">Para especificar uma mensagem de erro personalizada para a validação de tipos não anuláveis do lado do servidor, você tem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="9212a-164">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="9212a-165">Tornar o campo anulável (por exemplo, `decimal?` em vez de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="9212a-165">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="9212a-166">[Permite \<T> valor nulo](/dotnet/csharp/programming-guide/nullable-types/) tipos de valor são tratados como tipos anuláveis padrão.</span><span class="sxs-lookup"><span data-stu-id="9212a-166">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="9212a-167">Especifique a mensagem de erro padrão a ser usada pelo model binding, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-167">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="9212a-168">Para obter mais informações sobre erros de model binding para os quais você pode definir mensagens padrão, consulte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="9212a-168">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="9212a-169">Validação de [Required] no cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-169">[Required] validation on the client</span></span>

<span data-ttu-id="9212a-170">Cadeias de caracteres e tipos que não permitem valor nulo são tratados de maneira diferente no cliente em comparação com servidor.</span><span class="sxs-lookup"><span data-stu-id="9212a-170">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="9212a-171">No cliente:</span><span class="sxs-lookup"><span data-stu-id="9212a-171">On the client:</span></span>

* <span data-ttu-id="9212a-172">Um valor será considerado presente apenas se a entrada for inserida para ele.</span><span class="sxs-lookup"><span data-stu-id="9212a-172">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="9212a-173">Portanto, a validação do lado do cliente lida com tipos que não permitem valor nulo da mesma maneira que com tipos que permitem valor nulo.</span><span class="sxs-lookup"><span data-stu-id="9212a-173">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="9212a-174">O espaço em branco em um campo de cadeia de caracteres é considerado uma entrada válida pelo método [required](https://jqueryvalidation.org/required-method/) do jQuery Validation.</span><span class="sxs-lookup"><span data-stu-id="9212a-174">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="9212a-175">A validação do lado do servidor considerará um campo de cadeia de caracteres necessário inválido somente se o espaço em branco for inserido.</span><span class="sxs-lookup"><span data-stu-id="9212a-175">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="9212a-176">Conforme observado anteriormente, os tipos que não permitem valor nulo são tratados como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-176">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="9212a-177">Isso significa que você obtém a validação do lado do cliente mesmo que não aplique o atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-177">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="9212a-178">Mas se não for usar o atributo, você obterá uma mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="9212a-178">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="9212a-179">Para especificar uma mensagem de erro personalizada, use o atributo.</span><span class="sxs-lookup"><span data-stu-id="9212a-179">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="9212a-180">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="9212a-180">[Remote] attribute</span></span>

<span data-ttu-id="9212a-181">O atributo `[Remote]` implementa a validação do lado do cliente, exigindo a chamada de um método no servidor para determinar se a entrada do campo é válida.</span><span class="sxs-lookup"><span data-stu-id="9212a-181">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="9212a-182">Por exemplo, o aplicativo pode precisar verificar se um nome de usuário já está em uso.</span><span class="sxs-lookup"><span data-stu-id="9212a-182">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="9212a-183">Para implementar a validação remota:</span><span class="sxs-lookup"><span data-stu-id="9212a-183">To implement remote validation:</span></span>

1. <span data-ttu-id="9212a-184">Crie um método de ação para JavaScript para chamar.</span><span class="sxs-lookup"><span data-stu-id="9212a-184">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="9212a-185">O método [remoto](https://jqueryvalidation.org/remote-method/) de validação do jQuery espera uma resposta JSON:</span><span class="sxs-lookup"><span data-stu-id="9212a-185">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="9212a-186">`true` significa que os dados de entrada são válidos.</span><span class="sxs-lookup"><span data-stu-id="9212a-186">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="9212a-187">`false`, `undefined` ou `null` significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="9212a-187">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="9212a-188">Exiba a mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="9212a-188">Display the default error message.</span></span>
   * <span data-ttu-id="9212a-189">Qualquer outra cadeia de caracteres significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="9212a-189">Any other string means the input is invalid.</span></span> <span data-ttu-id="9212a-190">Exiba a cadeia de caracteres como uma mensagem de erro personalizada.</span><span class="sxs-lookup"><span data-stu-id="9212a-190">Display the string as a custom error message.</span></span>

   <span data-ttu-id="9212a-191">Aqui está um exemplo de um método de ação que retorna uma mensagem de erro personalizada:</span><span class="sxs-lookup"><span data-stu-id="9212a-191">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="9212a-192">Na classe de modelo, anote a propriedade com um atributo `[Remote]` que aponta para o método de ação de validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-192">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="9212a-193">O atributo `[Remote]` está no namespace `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="9212a-193">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="9212a-194">Campos adicionais</span><span class="sxs-lookup"><span data-stu-id="9212a-194">Additional fields</span></span>

<span data-ttu-id="9212a-195">A propriedade `AdditionalFields` do atributo `[Remote]` permite validar combinações de campos em relação aos dados no servidor.</span><span class="sxs-lookup"><span data-stu-id="9212a-195">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="9212a-196">Por exemplo, se o modelo `User` tinha as propriedades `FirstName` e `LastName`, pode ser interessante verificar se nenhum usuário existente já tem esse par de nomes.</span><span class="sxs-lookup"><span data-stu-id="9212a-196">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="9212a-197">O exemplo a seguir mostra como usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="9212a-197">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="9212a-198">`AdditionalFields` pode ser definido explicitamente para as cadeias de caracteres "FirstName" e "LastName", mas o uso do operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) simplifica a refatoração posterior.</span><span class="sxs-lookup"><span data-stu-id="9212a-198">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="9212a-199">O método de ação para essa validação deve aceitar ambos os `firstName` `lastName` argumentos e:</span><span class="sxs-lookup"><span data-stu-id="9212a-199">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="9212a-200">Quando o usuário insere o primeiro nome ou o sobrenome, o JavaScript faz uma chamada remota para ver se esse par de nomes foi usado.</span><span class="sxs-lookup"><span data-stu-id="9212a-200">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="9212a-201">Para validar dois ou mais campos adicionais, forneça-os como uma lista delimitada por vírgulas.</span><span class="sxs-lookup"><span data-stu-id="9212a-201">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="9212a-202">Por exemplo, para adicionar uma propriedade `MiddleName` ao modelo, defina o atributo `[Remote]`, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-202">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="9212a-203">`AdditionalFields`, como todos os argumentos de atributo, deve ser uma expressão de constante.</span><span class="sxs-lookup"><span data-stu-id="9212a-203">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="9212a-204">Portanto, não use uma [cadeia de caracteres interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) nem chame <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="9212a-204">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="9212a-205">Alternativas aos atributos internos</span><span class="sxs-lookup"><span data-stu-id="9212a-205">Alternatives to built-in attributes</span></span>

<span data-ttu-id="9212a-206">Se precisar de validação não fornecida por atributos internos, você poderá:</span><span class="sxs-lookup"><span data-stu-id="9212a-206">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="9212a-207">[Criar atributos personalizados](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="9212a-207">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="9212a-208">[Implementar IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="9212a-208">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="9212a-209">Atributos personalizados</span><span class="sxs-lookup"><span data-stu-id="9212a-209">Custom attributes</span></span>

<span data-ttu-id="9212a-210">Para cenários não compatíveis com os atributos de validação internos, você pode criar atributos de validação personalizados.</span><span class="sxs-lookup"><span data-stu-id="9212a-210">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="9212a-211">Crie uma classe que herda de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> e substitua o método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="9212a-211">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="9212a-212">O método `IsValid` aceita um objeto chamado *valor*, que é a entrada a ser validada.</span><span class="sxs-lookup"><span data-stu-id="9212a-212">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="9212a-213">Uma sobrecarga também aceita um objeto `ValidationContext`, que fornece informações adicionais, como a instância do modelo criada pelo model binding.</span><span class="sxs-lookup"><span data-stu-id="9212a-213">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="9212a-214">O exemplo a seguir valida se a data de lançamento de um filme no gênero *Clássico* não é posterior a um ano especificado.</span><span class="sxs-lookup"><span data-stu-id="9212a-214">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="9212a-215">O `[ClassicMovie]` atributo:</span><span class="sxs-lookup"><span data-stu-id="9212a-215">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="9212a-216">Só é executado no servidor.</span><span class="sxs-lookup"><span data-stu-id="9212a-216">Is only run on the server.</span></span>
* <span data-ttu-id="9212a-217">Para filmes clássicos, o valida a data de lançamento:</span><span class="sxs-lookup"><span data-stu-id="9212a-217">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="9212a-218">A variável `movie` no exemplo anterior representa um objeto `Movie` que contém os dados do envio do formulário.</span><span class="sxs-lookup"><span data-stu-id="9212a-218">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="9212a-219">Quando a validação falha, um `ValidationResult` com uma mensagem erro será retornado.</span><span class="sxs-lookup"><span data-stu-id="9212a-219">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="9212a-220">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="9212a-220">IValidatableObject</span></span>

<span data-ttu-id="9212a-221">O exemplo anterior funciona apenas com tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="9212a-221">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="9212a-222">Outra opção para validação de nível de classe é implementar `IValidatableObject` na classe de modelo, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-222">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="9212a-223">Validação de nó de nível superior</span><span class="sxs-lookup"><span data-stu-id="9212a-223">Top-level node validation</span></span>

<span data-ttu-id="9212a-224">Os nós de nível superior incluem:</span><span class="sxs-lookup"><span data-stu-id="9212a-224">Top-level nodes include:</span></span>

* <span data-ttu-id="9212a-225">Parâmetros de ação</span><span class="sxs-lookup"><span data-stu-id="9212a-225">Action parameters</span></span>
* <span data-ttu-id="9212a-226">Propriedades do controlador</span><span class="sxs-lookup"><span data-stu-id="9212a-226">Controller properties</span></span>
* <span data-ttu-id="9212a-227">Parâmetros do manipulador de página</span><span class="sxs-lookup"><span data-stu-id="9212a-227">Page handler parameters</span></span>
* <span data-ttu-id="9212a-228">Propriedades do modelo de página</span><span class="sxs-lookup"><span data-stu-id="9212a-228">Page model properties</span></span>

<span data-ttu-id="9212a-229">Os nós de nível superior associados ao modelo são validados além de validar as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="9212a-229">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="9212a-230">No exemplo a seguir do aplicativo de exemplo, o método `VerifyPhone` usa o <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar o parâmetro de ação `phone`:</span><span class="sxs-lookup"><span data-stu-id="9212a-230">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="9212a-231">Os nós de nível superior podem usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-231">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="9212a-232">No exemplo a seguir do aplicativo de exemplo, o método `CheckAge` especifica que o parâmetro `age` deve ser associado da cadeia de caracteres de consulta quando o formulário é enviado:</span><span class="sxs-lookup"><span data-stu-id="9212a-232">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="9212a-233">Na página Verificar Idade (*CheckAge.cshtml*), há dois formulários.</span><span class="sxs-lookup"><span data-stu-id="9212a-233">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="9212a-234">O primeiro formulário envia um `Age` valor de `99` como um parâmetro de cadeia de caracteres de consulta: `https://localhost:5001/Users/CheckAge?Age=99` .</span><span class="sxs-lookup"><span data-stu-id="9212a-234">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="9212a-235">Quando um parâmetro `age` formatado corretamente da cadeia de caracteres de consulta é enviado, o formulário é validado.</span><span class="sxs-lookup"><span data-stu-id="9212a-235">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="9212a-236">O segundo formulário na página Verificar Idade envia o valor `Age` no corpo da solicitação e a validação falha.</span><span class="sxs-lookup"><span data-stu-id="9212a-236">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="9212a-237">A associação falha porque o parâmetro `age` deve vir de uma cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9212a-237">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="9212a-238">Máximo de erros</span><span class="sxs-lookup"><span data-stu-id="9212a-238">Maximum errors</span></span>

<span data-ttu-id="9212a-239">A validação para quando o número máximo de erros é atingido (200 por padrão).</span><span class="sxs-lookup"><span data-stu-id="9212a-239">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="9212a-240">Você pode configurar esse número com o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9212a-240">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="9212a-241">Recursão máxima</span><span class="sxs-lookup"><span data-stu-id="9212a-241">Maximum recursion</span></span>

<span data-ttu-id="9212a-242"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> percorre o grafo de objeto do modelo que está sendo validado.</span><span class="sxs-lookup"><span data-stu-id="9212a-242"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="9212a-243">Para modelos que são de profundidade ou são recursivos infinitamente, a validação pode resultar em estouro de pilha.</span><span class="sxs-lookup"><span data-stu-id="9212a-243">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="9212a-244">O [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fornece uma maneira de interromper a validação antes que a recursão visitante exceda uma profundidade configurada.</span><span class="sxs-lookup"><span data-stu-id="9212a-244">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="9212a-245">O valor padrão de `MvcOptions.MaxValidationDepth` é 32.</span><span class="sxs-lookup"><span data-stu-id="9212a-245">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="9212a-246">Curto-circuito automático</span><span class="sxs-lookup"><span data-stu-id="9212a-246">Automatic short-circuit</span></span>

<span data-ttu-id="9212a-247">A validação sofrerá curto-circuito (será ignorada) automaticamente se o grafo do modelo não exigir validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-247">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="9212a-248">Objetos em que o runtime ignora a validação para inclusão de coleções de primitivos (como `byte[]`, `string[]` e `Dictionary<string, string>`) e grafos de objetos complexos que não têm um validador.</span><span class="sxs-lookup"><span data-stu-id="9212a-248">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="9212a-249">Desabilitar validação</span><span class="sxs-lookup"><span data-stu-id="9212a-249">Disable validation</span></span>

<span data-ttu-id="9212a-250">Para desabilitar a validação:</span><span class="sxs-lookup"><span data-stu-id="9212a-250">To disable validation:</span></span>

1. <span data-ttu-id="9212a-251">Crie uma implementação de `IObjectModelValidator` que não marque nenhum campo como inválido.</span><span class="sxs-lookup"><span data-stu-id="9212a-251">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="9212a-252">Adicione o seguinte código ao `Startup.ConfigureServices` para substituir a implementação `IObjectModelValidator` padrão no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="9212a-252">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="9212a-253">Talvez você ainda veja erros de estado de modelo que se originam do model binding.</span><span class="sxs-lookup"><span data-stu-id="9212a-253">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="9212a-254">Validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-254">Client-side validation</span></span>

<span data-ttu-id="9212a-255">A validação do lado do cliente impede o envio até que o formulário seja válido.</span><span class="sxs-lookup"><span data-stu-id="9212a-255">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="9212a-256">O botão Enviar executa o JavaScript que envia o formulário ou exibe mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="9212a-256">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="9212a-257">A validação do lado do cliente evita uma viagem de ida e volta desnecessária ao servidor quando há erros de entrada em um formulário.</span><span class="sxs-lookup"><span data-stu-id="9212a-257">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="9212a-258">O script a seguir faz referência nas validações de suporte *_Layout.cshtml* e *_ValidationScriptsPartial.cshtml* no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="9212a-258">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="9212a-259">O script de [validação não invasiva da jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) é uma biblioteca de front-end da Microsoft personalizada que se baseia no conhecido plug-in de [validação do jQuery](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="9212a-259">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="9212a-260">Sem o jQuery Unobtrusive Validation, você teria que codificar a mesma lógica de validação em dois locais: uma vez nos atributos de validação do lado do servidor nas propriedades do modelo e, em seguida, novamente nos scripts do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="9212a-260">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="9212a-261">Em vez disso, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e os [Auxiliares HTML](xref:mvc/views/overview) usam os atributos de validação e os metadados de tipo das propriedades do modelo para renderizar atributos `data-` de HTML 5 para os elementos de formulário que precisam de validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-261">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="9212a-262">a validação não invasiva do jQuery analisa os `data-` atributos e passa a lógica para a validação do jQuery, efetivamente "copiando" a lógica de validação do lado do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="9212a-262">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="9212a-263">Você pode exibir erros de validação no cliente usando os auxiliares de marca conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="9212a-263">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="9212a-264">Os auxiliares de marcação anteriores renderizam o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="9212a-264">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="9212a-265">Observe que os atributos `data-` na saída HTML correspondem aos atributos de validação da propriedade `Movie.ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="9212a-265">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="9212a-266">O atributo `data-val-required` conterá uma mensagem de erro a ser exibida se o usuário não preencher o campo de data de lançamento.</span><span class="sxs-lookup"><span data-stu-id="9212a-266">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="9212a-267">a validação não invasiva do jQuery passa esse valor para o método de validação do jQuery [necessário ()](https://jqueryvalidation.org/required-method/) , que, em seguida, exibe essa mensagem no elemento que o acompanha **\<span>** .</span><span class="sxs-lookup"><span data-stu-id="9212a-267">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="9212a-268">A validação de tipo de dados é baseada no tipo .NET de uma propriedade, a menos que seja substituída por um atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-268">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="9212a-269">Os navegadores têm suas próprias mensagens de erro padrão, mas o pacote de validação do jQuery Validation Unobtrusive pode substituir essas mensagens.</span><span class="sxs-lookup"><span data-stu-id="9212a-269">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="9212a-270">Os atributos `[DataType]` e as subclasses como `[EmailAddress]` permitem que você especifique a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="9212a-270">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="9212a-271">Validação não invasiva</span><span class="sxs-lookup"><span data-stu-id="9212a-271">Unobtrusive validation</span></span>

<span data-ttu-id="9212a-272">Para obter informações sobre a validação não invasiva, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="9212a-272">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="9212a-273">Adicionar validação a formulários dinâmicos</span><span class="sxs-lookup"><span data-stu-id="9212a-273">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="9212a-274">a validação não invasiva do jQuery passa a lógica de validação e parâmetros para validação do jQuery quando a página é carregada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="9212a-274">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="9212a-275">Portanto, a validação não funciona automaticamente em formulários gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="9212a-275">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="9212a-276">Para habilitar a validação é necessário instruir o jQuery Unobtrusive Validation a analisar o formulário dinâmico imediatamente depois de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="9212a-276">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="9212a-277">Por exemplo, o código a seguir define a validação do lado do cliente em um formulário adicionado por meio do AJAX.</span><span class="sxs-lookup"><span data-stu-id="9212a-277">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="9212a-278">O método `$.validator.unobtrusive.parse()` aceita um seletor do jQuery para um argumento seu.</span><span class="sxs-lookup"><span data-stu-id="9212a-278">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="9212a-279">Esse método instrui o jQuery Unobtrusive Validation a analisar os atributos `data-` de formulários nesse seletor.</span><span class="sxs-lookup"><span data-stu-id="9212a-279">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="9212a-280">Os valores desses atributos são passados para o plug-in de validação jQuery.</span><span class="sxs-lookup"><span data-stu-id="9212a-280">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="9212a-281">Adicionar validação a controles dinâmicos</span><span class="sxs-lookup"><span data-stu-id="9212a-281">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="9212a-282">O método `$.validator.unobtrusive.parse()` funciona em um formulário inteiro, não em controles individuais gerados dinamicamente, como `<input>` e `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="9212a-282">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="9212a-283">Para uma nova análise do formulário, remova os dados de validação que foram adicionados ao formulário mesmo quando foi analisado anteriormente, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-283">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="9212a-284">Validação personalizada do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-284">Custom client-side validation</span></span>

<span data-ttu-id="9212a-285">A validação personalizada do lado do cliente é feita pela geração de `data-` atributos HTML que funcionam com um adaptador de validação do jQuery personalizado.</span><span class="sxs-lookup"><span data-stu-id="9212a-285">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="9212a-286">O seguinte código do adaptador de exemplo foi escrito para os atributos `[ClassicMovie]` e `[ClassicMovieWithClientValidator]` que foram apresentados no início deste artigo:</span><span class="sxs-lookup"><span data-stu-id="9212a-286">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="9212a-287">Para obter informações sobre como escrever adaptadores, consulte a [documentação de validação do jQuery](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="9212a-287">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="9212a-288">O uso de um adaptador para um determinado campo é disparado por atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="9212a-288">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="9212a-289">Marcam o campo como sujeito à validação do sinalizador (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="9212a-289">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="9212a-290">Identifique o nome de uma regra de validação e o texto da mensagem de erro (por exemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="9212a-290">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="9212a-291">Forneça os parâmetros adicionais que o validador precisa (por exemplo, `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="9212a-291">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="9212a-292">A exemplo a seguir mostra os atributos `data-` para o atributo `ClassicMovie` do aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-292">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="9212a-293">Conforme observado anteriormente, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e [Auxiliares de HTML](xref:mvc/views/overview) usam informações de atributos de validação para renderizar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="9212a-293">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="9212a-294">Há duas opções para escrever código que resulte na criação de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="9212a-294">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="9212a-295">Criar uma classe que deriva de `AttributeAdapterBase<TAttribute>` e uma classe que implementa `IValidationAttributeAdapterProvider` e registrar o atributo e o adaptador na DI.</span><span class="sxs-lookup"><span data-stu-id="9212a-295">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="9212a-296">Este método segue a [entidade de segurança de responsabilidade única](https://wikipedia.org/wiki/Single_responsibility_principle) em que o código de validação relacionado ao cliente e ao servidor fica em classes separadas.</span><span class="sxs-lookup"><span data-stu-id="9212a-296">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="9212a-297">O adaptador também tem a vantagem de que, uma vez que ele é registrado na DI, outros serviços da DI ficam disponíveis para ele, se necessário.</span><span class="sxs-lookup"><span data-stu-id="9212a-297">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="9212a-298">Implementar `IClientModelValidator` em na classe `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="9212a-298">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="9212a-299">Esse método poderá ser adequado se o atributo não fizer nenhuma validação do lado do servidor e não precisar de um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="9212a-299">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="9212a-300">AttributeAdapter para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-300">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="9212a-301">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9212a-301">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="9212a-302">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="9212a-302">To add client validation by using this method:</span></span>

1. <span data-ttu-id="9212a-303">Crie uma classe de adaptador de atributo para o atributo de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="9212a-303">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="9212a-304">Derive a classe de <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601> .</span><span class="sxs-lookup"><span data-stu-id="9212a-304">Derive the class from <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>.</span></span> <span data-ttu-id="9212a-305">Criar um método `AddValidation` que adiciona atributos `data-` à saída renderizada, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-305">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="9212a-306">Crie uma classe de provedor de adaptador que implementa <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="9212a-306">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="9212a-307">No método `GetAttributeAdapter`, passe o atributo personalizado para o construtor do adaptador, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-307">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="9212a-308">Registre o provedor de adaptador para DI em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9212a-308">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="9212a-309">IClientModelValidator para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-309">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="9212a-310">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovieWithClientValidator` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9212a-310">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="9212a-311">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="9212a-311">To add client validation by using this method:</span></span>

* <span data-ttu-id="9212a-312">No atributo de validação personalizado, implemente a interface `IClientModelValidator` e crie um método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="9212a-312">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="9212a-313">No método `AddValidation`, adicione atributos `data-` para validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-313">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="9212a-314">Desabilitar validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-314">Disable client-side validation</span></span>

<span data-ttu-id="9212a-315">O código a seguir desabilita a validação do cliente em Razor páginas:</span><span class="sxs-lookup"><span data-stu-id="9212a-315">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="9212a-316">Outras opções para desabilitar a validação do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="9212a-316">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="9212a-317">Comente a referência para `_ValidationScriptsPartial` em todos os arquivos *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9212a-317">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="9212a-318">Remova o conteúdo do arquivo *Pages\Shared \_ ValidationScriptsPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9212a-318">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="9212a-319">A abordagem anterior não impedirá a validação do lado do cliente da ASP.NET Core Identity Razor biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="9212a-319">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="9212a-320">Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="9212a-320">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9212a-321">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9212a-321">Additional resources</span></span>

* [<span data-ttu-id="9212a-322">Namespace System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="9212a-322">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="9212a-323">Associação de modelo</span><span class="sxs-lookup"><span data-stu-id="9212a-323">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9212a-324">Este artigo explica como validar a entrada do usuário em um aplicativo ASP.NET Core MVC ou Razor pages.</span><span class="sxs-lookup"><span data-stu-id="9212a-324">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="9212a-325">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9212a-325">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="9212a-326">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="9212a-326">Model state</span></span>

<span data-ttu-id="9212a-327">O estado do modelo representa erros que vêm de dois subsistemas: model binding e validação de modelo.</span><span class="sxs-lookup"><span data-stu-id="9212a-327">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="9212a-328">Erros que se originam de [model binding](model-binding.md) geralmente são erros de conversão de dados (por exemplo, um "x" é inserido em um campo que espera um inteiro).</span><span class="sxs-lookup"><span data-stu-id="9212a-328">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="9212a-329">A validação do modelo ocorre após o model binding e relata os erros em que os dados não estão em conformidade com as regras de negócio (por exemplo, um 0 é inserido em um campo que espera uma classificação entre 1 e 5).</span><span class="sxs-lookup"><span data-stu-id="9212a-329">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="9212a-330">A associação de modelo e a validação ocorrem antes da execução de uma ação de controlador ou um Razor método de manipulador de páginas.</span><span class="sxs-lookup"><span data-stu-id="9212a-330">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="9212a-331">Nos aplicativos Web, é responsabilidade do aplicativo inspecionar `ModelState.IsValid` e reagir adequadamente.</span><span class="sxs-lookup"><span data-stu-id="9212a-331">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="9212a-332">Geralmente, os aplicativos Web reexibem a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="9212a-332">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="9212a-333">Os controladores de API Web não precisarão verificar `ModelState.IsValid` se eles tiverem o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-333">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="9212a-334">Nesse caso, uma resposta HTTP 400 automática contendo detalhes do erro é retornada quando o estado do modelo é inválido.</span><span class="sxs-lookup"><span data-stu-id="9212a-334">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="9212a-335">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="9212a-335">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="9212a-336">Executar validação novamente</span><span class="sxs-lookup"><span data-stu-id="9212a-336">Rerun validation</span></span>

<span data-ttu-id="9212a-337">A validação é automática, mas talvez seja necessário repeti-la manualmente.</span><span class="sxs-lookup"><span data-stu-id="9212a-337">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="9212a-338">Por exemplo, você pode calcular um valor para uma propriedade e executar novamente a validação após a configuração da propriedade com o valor computado.</span><span class="sxs-lookup"><span data-stu-id="9212a-338">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="9212a-339">Para reexecutar a validação, chame o método `TryValidateModel`, conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="9212a-339">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="9212a-340">Atributos de validação</span><span class="sxs-lookup"><span data-stu-id="9212a-340">Validation attributes</span></span>

<span data-ttu-id="9212a-341">Os atributos de validação permitem que você especifique regras de validação para propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="9212a-341">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="9212a-342">O exemplo a seguir do [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) mostra uma classe de modelo que é anotada com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-342">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="9212a-343">O atributo `[ClassicMovie]` é um atributo de validação personalizado e os outros são atributos internos.</span><span class="sxs-lookup"><span data-stu-id="9212a-343">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="9212a-344">Não mostrado é `[ClassicMovie2]` , que mostra uma maneira alternativa de implementar um atributo personalizado.</span><span class="sxs-lookup"><span data-stu-id="9212a-344">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="9212a-345">Atributos internos</span><span class="sxs-lookup"><span data-stu-id="9212a-345">Built-in attributes</span></span>

<span data-ttu-id="9212a-346">Os atributos de validação internos incluem:</span><span class="sxs-lookup"><span data-stu-id="9212a-346">Built-in validation attributes include:</span></span>

* <span data-ttu-id="9212a-347">`[CreditCard]`: Valida que a propriedade tem um formato de cartão de crédito.</span><span class="sxs-lookup"><span data-stu-id="9212a-347">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="9212a-348">`[Compare]`: Valida que duas propriedades em um modelo correspondem.</span><span class="sxs-lookup"><span data-stu-id="9212a-348">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="9212a-349">Por exemplo, o arquivo *Register.cshtml.cs* usa `[Compare]` para validar a correspondência de duas senhas inseridas.</span><span class="sxs-lookup"><span data-stu-id="9212a-349">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="9212a-350">[Scaffold Identity ](xref:security/authentication/scaffold-identity) para ver o código de registro.</span><span class="sxs-lookup"><span data-stu-id="9212a-350">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="9212a-351">`[EmailAddress]`: Valida que a propriedade tem um formato de email.</span><span class="sxs-lookup"><span data-stu-id="9212a-351">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="9212a-352">`[Phone]`: Valida que a propriedade tem um formato de número de telefone.</span><span class="sxs-lookup"><span data-stu-id="9212a-352">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="9212a-353">`[Range]`: Valida que o valor da propriedade cai em um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="9212a-353">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="9212a-354">`[RegularExpression]`: Valida que o valor da propriedade corresponde a uma expressão regular especificada.</span><span class="sxs-lookup"><span data-stu-id="9212a-354">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="9212a-355">`[Required]`: Valida que o campo não é nulo.</span><span class="sxs-lookup"><span data-stu-id="9212a-355">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="9212a-356">Consulte o [ `[Required]` atributo](#required-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="9212a-356">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="9212a-357">`[StringLength]`: Valida que um valor de propriedade de cadeia de caracteres não excede um limite de comprimento especificado.</span><span class="sxs-lookup"><span data-stu-id="9212a-357">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="9212a-358">`[Url]`: Valida que a propriedade tem um formato de URL.</span><span class="sxs-lookup"><span data-stu-id="9212a-358">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="9212a-359">`[Remote]`: Valida a entrada no cliente chamando um método de ação no servidor.</span><span class="sxs-lookup"><span data-stu-id="9212a-359">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="9212a-360">Consulte o [ `[Remote]` atributo](#remote-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="9212a-360">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="9212a-361">Ao usar o `[RegularExpression]` atributo com validação do lado do cliente, o Regex é executado em JavaScript no cliente.</span><span class="sxs-lookup"><span data-stu-id="9212a-361">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="9212a-362">Isso significa que o comportamento de correspondência [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) será usado.</span><span class="sxs-lookup"><span data-stu-id="9212a-362">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="9212a-363">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="9212a-363">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="9212a-364">Uma lista completa de atributos de validação pode ser encontrada no namespace [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9212a-364">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="9212a-365">Mensagens de erro</span><span class="sxs-lookup"><span data-stu-id="9212a-365">Error messages</span></span>

<span data-ttu-id="9212a-366">Os atributos de validação permitem que você especifique a mensagem de erro a ser exibido para uma entrada inválida.</span><span class="sxs-lookup"><span data-stu-id="9212a-366">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="9212a-367">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-367">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="9212a-368">Internamente, a chamada de atributos `String.Format` com um espaço reservado para o nome do campo e, às vezes, espaços reservados adicionais.</span><span class="sxs-lookup"><span data-stu-id="9212a-368">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="9212a-369">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-369">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="9212a-370">Quando aplicada a uma propriedade `Name`, a mensagem de erro criada pelo código anterior seria "Comprimento do nome ter entre 6 e 8.".</span><span class="sxs-lookup"><span data-stu-id="9212a-370">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="9212a-371">Para descobrir quais parâmetros são passados para `String.Format` no caso de uma mensagem de erro de um atributo específico, consulte o [código-fonte de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9212a-371">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="9212a-372">Atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="9212a-372">[Required] attribute</span></span>

<span data-ttu-id="9212a-373">Por padrão, o sistema de validação trata parâmetros não anuláveis ou propriedades como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-373">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="9212a-374">[Tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` e `int` são não anuláveis.</span><span class="sxs-lookup"><span data-stu-id="9212a-374">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="9212a-375">Validação de [Required] no servidor</span><span class="sxs-lookup"><span data-stu-id="9212a-375">[Required] validation on the server</span></span>

<span data-ttu-id="9212a-376">No servidor, um valor obrigatório será considerado ausente se a propriedade for nula.</span><span class="sxs-lookup"><span data-stu-id="9212a-376">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="9212a-377">Um campo não anulável é sempre válido e a mensagem de erro do atributo [Required] nunca é exibida.</span><span class="sxs-lookup"><span data-stu-id="9212a-377">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="9212a-378">No entanto, o model binding para uma propriedade que não permite valor nulo pode falhar, resultando em uma mensagem de erro como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="9212a-378">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="9212a-379">Para especificar uma mensagem de erro personalizada para a validação de tipos não anuláveis do lado do servidor, você tem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="9212a-379">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="9212a-380">Tornar o campo anulável (por exemplo, `decimal?` em vez de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="9212a-380">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="9212a-381">[Permite \<T> valor nulo](/dotnet/csharp/programming-guide/nullable-types/) tipos de valor são tratados como tipos anuláveis padrão.</span><span class="sxs-lookup"><span data-stu-id="9212a-381">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="9212a-382">Especifique a mensagem de erro padrão a ser usada pelo model binding, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-382">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="9212a-383">Para obter mais informações sobre erros de model binding para os quais você pode definir mensagens padrão, consulte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="9212a-383">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="9212a-384">Validação de [Required] no cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-384">[Required] validation on the client</span></span>

<span data-ttu-id="9212a-385">Cadeias de caracteres e tipos que não permitem valor nulo são tratados de maneira diferente no cliente em comparação com servidor.</span><span class="sxs-lookup"><span data-stu-id="9212a-385">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="9212a-386">No cliente:</span><span class="sxs-lookup"><span data-stu-id="9212a-386">On the client:</span></span>

* <span data-ttu-id="9212a-387">Um valor será considerado presente apenas se a entrada for inserida para ele.</span><span class="sxs-lookup"><span data-stu-id="9212a-387">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="9212a-388">Portanto, a validação do lado do cliente lida com tipos que não permitem valor nulo da mesma maneira que com tipos que permitem valor nulo.</span><span class="sxs-lookup"><span data-stu-id="9212a-388">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="9212a-389">O espaço em branco em um campo de cadeia de caracteres é considerado uma entrada válida pelo método [required](https://jqueryvalidation.org/required-method/) do jQuery Validation.</span><span class="sxs-lookup"><span data-stu-id="9212a-389">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="9212a-390">A validação do lado do servidor considerará um campo de cadeia de caracteres necessário inválido somente se o espaço em branco for inserido.</span><span class="sxs-lookup"><span data-stu-id="9212a-390">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="9212a-391">Conforme observado anteriormente, os tipos que não permitem valor nulo são tratados como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-391">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="9212a-392">Isso significa que você obtém a validação do lado do cliente mesmo que não aplique o atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-392">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="9212a-393">Mas se não for usar o atributo, você obterá uma mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="9212a-393">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="9212a-394">Para especificar uma mensagem de erro personalizada, use o atributo.</span><span class="sxs-lookup"><span data-stu-id="9212a-394">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="9212a-395">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="9212a-395">[Remote] attribute</span></span>

<span data-ttu-id="9212a-396">O atributo `[Remote]` implementa a validação do lado do cliente, exigindo a chamada de um método no servidor para determinar se a entrada do campo é válida.</span><span class="sxs-lookup"><span data-stu-id="9212a-396">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="9212a-397">Por exemplo, o aplicativo pode precisar verificar se um nome de usuário já está em uso.</span><span class="sxs-lookup"><span data-stu-id="9212a-397">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="9212a-398">Para implementar a validação remota:</span><span class="sxs-lookup"><span data-stu-id="9212a-398">To implement remote validation:</span></span>

1. <span data-ttu-id="9212a-399">Crie um método de ação para JavaScript para chamar.</span><span class="sxs-lookup"><span data-stu-id="9212a-399">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="9212a-400">O método [remoto](https://jqueryvalidation.org/remote-method/) do jQuery Validate espera uma resposta JSON:</span><span class="sxs-lookup"><span data-stu-id="9212a-400">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="9212a-401">`"true"` significa que os dados de entrada são válidos.</span><span class="sxs-lookup"><span data-stu-id="9212a-401">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="9212a-402">`"false"`, `undefined` ou `null` significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="9212a-402">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="9212a-403">Exiba a mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="9212a-403">Display the default error message.</span></span>
   * <span data-ttu-id="9212a-404">Qualquer outra cadeia de caracteres significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="9212a-404">Any other string means the input is invalid.</span></span> <span data-ttu-id="9212a-405">Exiba a cadeia de caracteres como uma mensagem de erro personalizada.</span><span class="sxs-lookup"><span data-stu-id="9212a-405">Display the string as a custom error message.</span></span>

   <span data-ttu-id="9212a-406">Aqui está um exemplo de um método de ação que retorna uma mensagem de erro personalizada:</span><span class="sxs-lookup"><span data-stu-id="9212a-406">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="9212a-407">Na classe de modelo, anote a propriedade com um atributo `[Remote]` que aponta para o método de ação de validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-407">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="9212a-408">O atributo `[Remote]` está no namespace `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="9212a-408">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="9212a-409">Instale o pacote NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) se você não estiver usando o metapacote `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="9212a-409">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="9212a-410">Campos adicionais</span><span class="sxs-lookup"><span data-stu-id="9212a-410">Additional fields</span></span>

<span data-ttu-id="9212a-411">A propriedade `AdditionalFields` do atributo `[Remote]` permite validar combinações de campos em relação aos dados no servidor.</span><span class="sxs-lookup"><span data-stu-id="9212a-411">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="9212a-412">Por exemplo, se o modelo `User` tinha as propriedades `FirstName` e `LastName`, pode ser interessante verificar se nenhum usuário existente já tem esse par de nomes.</span><span class="sxs-lookup"><span data-stu-id="9212a-412">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="9212a-413">O exemplo a seguir mostra como usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="9212a-413">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="9212a-414">`AdditionalFields` pode ser definido explicitamente para as cadeias de caracteres `"FirstName"` e `"LastName"` , mas o uso do operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) simplifica a refatoração posterior.</span><span class="sxs-lookup"><span data-stu-id="9212a-414">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="9212a-415">O método de ação para essa validação deve aceitar os argumentos de primeiro nome e de sobrenome:</span><span class="sxs-lookup"><span data-stu-id="9212a-415">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="9212a-416">Quando o usuário insere o primeiro nome ou o sobrenome, o JavaScript faz uma chamada remota para ver se esse par de nomes foi usado.</span><span class="sxs-lookup"><span data-stu-id="9212a-416">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="9212a-417">Para validar dois ou mais campos adicionais, forneça-os como uma lista delimitada por vírgulas.</span><span class="sxs-lookup"><span data-stu-id="9212a-417">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="9212a-418">Por exemplo, para adicionar uma propriedade `MiddleName` ao modelo, defina o atributo `[Remote]`, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-418">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="9212a-419">`AdditionalFields`, como todos os argumentos de atributo, deve ser uma expressão de constante.</span><span class="sxs-lookup"><span data-stu-id="9212a-419">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="9212a-420">Portanto, não use uma [cadeia de caracteres interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) nem chame <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="9212a-420">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="9212a-421">Alternativas aos atributos internos</span><span class="sxs-lookup"><span data-stu-id="9212a-421">Alternatives to built-in attributes</span></span>

<span data-ttu-id="9212a-422">Se precisar de validação não fornecida por atributos internos, você poderá:</span><span class="sxs-lookup"><span data-stu-id="9212a-422">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="9212a-423">[Criar atributos personalizados](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="9212a-423">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="9212a-424">[Implementar IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="9212a-424">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="9212a-425">Atributos personalizados</span><span class="sxs-lookup"><span data-stu-id="9212a-425">Custom attributes</span></span>

<span data-ttu-id="9212a-426">Para cenários não compatíveis com os atributos de validação internos, você pode criar atributos de validação personalizados.</span><span class="sxs-lookup"><span data-stu-id="9212a-426">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="9212a-427">Crie uma classe que herda de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> e substitua o método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="9212a-427">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="9212a-428">O método `IsValid` aceita um objeto chamado *valor*, que é a entrada a ser validada.</span><span class="sxs-lookup"><span data-stu-id="9212a-428">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="9212a-429">Uma sobrecarga também aceita um objeto `ValidationContext`, que fornece informações adicionais, como a instância do modelo criada pelo model binding.</span><span class="sxs-lookup"><span data-stu-id="9212a-429">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="9212a-430">O exemplo a seguir valida se a data de lançamento de um filme no gênero *Clássico* não é posterior a um ano especificado.</span><span class="sxs-lookup"><span data-stu-id="9212a-430">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="9212a-431">O atributo `[ClassicMovie2]` verificará o gênero primeiro e continuará apenas se for *Clássico*.</span><span class="sxs-lookup"><span data-stu-id="9212a-431">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="9212a-432">Para filmes identificados como clássicos, ele verifica a data de lançamento para garantir que não seja posterior ao limite passado ao construtor de atributo).</span><span class="sxs-lookup"><span data-stu-id="9212a-432">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="9212a-433">A variável `movie` no exemplo anterior representa um objeto `Movie` que contém os dados do envio do formulário.</span><span class="sxs-lookup"><span data-stu-id="9212a-433">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="9212a-434">O método `IsValid` verifica a data e o gênero.</span><span class="sxs-lookup"><span data-stu-id="9212a-434">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="9212a-435">Após a validação bem-sucedida, o `IsValid` retorna um código `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="9212a-435">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="9212a-436">Quando a validação falha, um `ValidationResult` com uma mensagem erro será retornado.</span><span class="sxs-lookup"><span data-stu-id="9212a-436">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="9212a-437">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="9212a-437">IValidatableObject</span></span>

<span data-ttu-id="9212a-438">O exemplo anterior funciona apenas com tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="9212a-438">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="9212a-439">Outra opção para validação de nível de classe é implementar `IValidatableObject` na classe de modelo, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-439">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="9212a-440">Validação de nó de nível superior</span><span class="sxs-lookup"><span data-stu-id="9212a-440">Top-level node validation</span></span>

<span data-ttu-id="9212a-441">Os nós de nível superior incluem:</span><span class="sxs-lookup"><span data-stu-id="9212a-441">Top-level nodes include:</span></span>

* <span data-ttu-id="9212a-442">Parâmetros de ação</span><span class="sxs-lookup"><span data-stu-id="9212a-442">Action parameters</span></span>
* <span data-ttu-id="9212a-443">Propriedades do controlador</span><span class="sxs-lookup"><span data-stu-id="9212a-443">Controller properties</span></span>
* <span data-ttu-id="9212a-444">Parâmetros do manipulador de página</span><span class="sxs-lookup"><span data-stu-id="9212a-444">Page handler parameters</span></span>
* <span data-ttu-id="9212a-445">Propriedades do modelo de página</span><span class="sxs-lookup"><span data-stu-id="9212a-445">Page model properties</span></span>

<span data-ttu-id="9212a-446">Os nós de nível superior associados ao modelo são validados além de validar as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="9212a-446">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="9212a-447">No exemplo a seguir do aplicativo de exemplo, o método `VerifyPhone` usa o <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar o parâmetro de ação `phone`:</span><span class="sxs-lookup"><span data-stu-id="9212a-447">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="9212a-448">Os nós de nível superior podem usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-448">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="9212a-449">No exemplo a seguir do aplicativo de exemplo, o método `CheckAge` especifica que o parâmetro `age` deve ser associado da cadeia de caracteres de consulta quando o formulário é enviado:</span><span class="sxs-lookup"><span data-stu-id="9212a-449">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="9212a-450">Na página Verificar Idade (*CheckAge.cshtml*), há dois formulários.</span><span class="sxs-lookup"><span data-stu-id="9212a-450">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="9212a-451">O primeiro formulário envia um valor `Age` de `99` como uma cadeia de caracteres de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="9212a-451">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="9212a-452">Quando um parâmetro `age` formatado corretamente da cadeia de caracteres de consulta é enviado, o formulário é validado.</span><span class="sxs-lookup"><span data-stu-id="9212a-452">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="9212a-453">O segundo formulário na página Verificar Idade envia o valor `Age` no corpo da solicitação e a validação falha.</span><span class="sxs-lookup"><span data-stu-id="9212a-453">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="9212a-454">A associação falha porque o parâmetro `age` deve vir de uma cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9212a-454">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="9212a-455">Ao executar com `CompatibilityVersion.Version_2_1` ou posterior, a validação do nó de nível superior é habilitada por padrão.</span><span class="sxs-lookup"><span data-stu-id="9212a-455">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="9212a-456">Caso contrário, a validação do nó de nível superior ficará desabilitada.</span><span class="sxs-lookup"><span data-stu-id="9212a-456">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="9212a-457">A opção padrão pode ser substituída pela configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> em (`Startup.ConfigureServices`), conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="9212a-457">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="9212a-458">Máximo de erros</span><span class="sxs-lookup"><span data-stu-id="9212a-458">Maximum errors</span></span>

<span data-ttu-id="9212a-459">A validação para quando o número máximo de erros é atingido (200 por padrão).</span><span class="sxs-lookup"><span data-stu-id="9212a-459">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="9212a-460">Você pode configurar esse número com o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9212a-460">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="9212a-461">Recursão máxima</span><span class="sxs-lookup"><span data-stu-id="9212a-461">Maximum recursion</span></span>

<span data-ttu-id="9212a-462"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> percorre o grafo de objeto do modelo que está sendo validado.</span><span class="sxs-lookup"><span data-stu-id="9212a-462"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="9212a-463">Para modelos que são muito profundos ou que são infinitamente recursivos, a validação pode resultar em estouro de pilha.</span><span class="sxs-lookup"><span data-stu-id="9212a-463">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="9212a-464">O [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fornece uma maneira de interromper a validação antes que a recursão visitante exceda uma profundidade configurada.</span><span class="sxs-lookup"><span data-stu-id="9212a-464">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="9212a-465">O valor padrão de `MvcOptions.MaxValidationDepth` é 32 ao executar com `CompatibilityVersion.Version_2_2` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="9212a-465">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="9212a-466">Para versões anteriores, o valor é nulo, o que significa que não há nenhuma restrição de profundidade.</span><span class="sxs-lookup"><span data-stu-id="9212a-466">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="9212a-467">Curto-circuito automático</span><span class="sxs-lookup"><span data-stu-id="9212a-467">Automatic short-circuit</span></span>

<span data-ttu-id="9212a-468">A validação sofrerá curto-circuito (será ignorada) automaticamente se o grafo do modelo não exigir validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-468">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="9212a-469">Objetos em que o runtime ignora a validação para inclusão de coleções de primitivos (como `byte[]`, `string[]` e `Dictionary<string, string>`) e grafos de objetos complexos que não têm um validador.</span><span class="sxs-lookup"><span data-stu-id="9212a-469">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="9212a-470">Desabilitar validação</span><span class="sxs-lookup"><span data-stu-id="9212a-470">Disable validation</span></span>

<span data-ttu-id="9212a-471">Para desabilitar a validação:</span><span class="sxs-lookup"><span data-stu-id="9212a-471">To disable validation:</span></span>

1. <span data-ttu-id="9212a-472">Crie uma implementação de `IObjectModelValidator` que não marque nenhum campo como inválido.</span><span class="sxs-lookup"><span data-stu-id="9212a-472">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="9212a-473">Adicione o seguinte código ao `Startup.ConfigureServices` para substituir a implementação `IObjectModelValidator` padrão no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="9212a-473">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="9212a-474">Talvez você ainda veja erros de estado de modelo que se originam do model binding.</span><span class="sxs-lookup"><span data-stu-id="9212a-474">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="9212a-475">Validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-475">Client-side validation</span></span>

<span data-ttu-id="9212a-476">A validação do lado do cliente impede o envio até que o formulário seja válido.</span><span class="sxs-lookup"><span data-stu-id="9212a-476">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="9212a-477">O botão Enviar executa o JavaScript que envia o formulário ou exibe mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="9212a-477">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="9212a-478">A validação do lado do cliente evita uma viagem de ida e volta desnecessária ao servidor quando há erros de entrada em um formulário.</span><span class="sxs-lookup"><span data-stu-id="9212a-478">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="9212a-479">O script a seguir faz referência nas validações de suporte *_Layout.cshtml* e *_ValidationScriptsPartial.cshtml* no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="9212a-479">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="9212a-480">O script do [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) é uma biblioteca de front-end personalizada da Microsoft que se baseia no popular plug-in [jQuery Validate](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="9212a-480">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="9212a-481">Sem o jQuery Unobtrusive Validation, você teria que codificar a mesma lógica de validação em dois locais: uma vez nos atributos de validação do lado do servidor nas propriedades do modelo e, em seguida, novamente nos scripts do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="9212a-481">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="9212a-482">Em vez disso, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e os [Auxiliares HTML](xref:mvc/views/overview) usam os atributos de validação e os metadados de tipo das propriedades do modelo para renderizar atributos `data-` de HTML 5 para os elementos de formulário que precisam de validação.</span><span class="sxs-lookup"><span data-stu-id="9212a-482">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="9212a-483">O jQuery Unobtrusive Validation analisa os atributos `data-` e passa a lógica para o jQuery Validate, "copiando" efetivamente a lógica de validação do lado do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="9212a-483">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="9212a-484">Você pode exibir erros de validação no cliente usando os auxiliares de marca conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="9212a-484">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="9212a-485">Os auxiliares de marca acima renderizam o HTML a seguir.</span><span class="sxs-lookup"><span data-stu-id="9212a-485">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="9212a-486">Observe que os atributos `data-` na saída HTML correspondem aos atributos de validação da propriedade `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="9212a-486">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="9212a-487">O atributo `data-val-required` conterá uma mensagem de erro a ser exibida se o usuário não preencher o campo de data de lançamento.</span><span class="sxs-lookup"><span data-stu-id="9212a-487">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="9212a-488">a validação não invasiva do jQuery passa esse valor para o método de validação do jQuery [requerido ()](https://jqueryvalidation.org/required-method/) , que, em seguida, exibe essa mensagem no elemento que o acompanha **\<span>** .</span><span class="sxs-lookup"><span data-stu-id="9212a-488">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="9212a-489">A validação de tipo de dados é baseada no tipo .NET de uma propriedade, a menos que seja substituída por um atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="9212a-489">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="9212a-490">Os navegadores têm suas próprias mensagens de erro padrão, mas o pacote de validação do jQuery Validation Unobtrusive pode substituir essas mensagens.</span><span class="sxs-lookup"><span data-stu-id="9212a-490">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="9212a-491">Os atributos `[DataType]` e as subclasses como `[EmailAddress]` permitem que você especifique a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="9212a-491">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="9212a-492">Adicionar validação a formulários dinâmicos</span><span class="sxs-lookup"><span data-stu-id="9212a-492">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="9212a-493">O jQuery Unobtrusive Validation passa parâmetros e a lógica de validação para o jQuery Validate quando a página é carregada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="9212a-493">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="9212a-494">Portanto, a validação não funciona automaticamente em formulários gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="9212a-494">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="9212a-495">Para habilitar a validação é necessário instruir o jQuery Unobtrusive Validation a analisar o formulário dinâmico imediatamente depois de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="9212a-495">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="9212a-496">Por exemplo, o código a seguir define a validação do lado do cliente em um formulário adicionado por meio do AJAX.</span><span class="sxs-lookup"><span data-stu-id="9212a-496">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="9212a-497">O método `$.validator.unobtrusive.parse()` aceita um seletor do jQuery para um argumento seu.</span><span class="sxs-lookup"><span data-stu-id="9212a-497">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="9212a-498">Esse método instrui o jQuery Unobtrusive Validation a analisar os atributos `data-` de formulários nesse seletor.</span><span class="sxs-lookup"><span data-stu-id="9212a-498">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="9212a-499">Depois, os valores desses atributos são passados para o plug-in do jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="9212a-499">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="9212a-500">Adicionar validação a controles dinâmicos</span><span class="sxs-lookup"><span data-stu-id="9212a-500">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="9212a-501">O método `$.validator.unobtrusive.parse()` funciona em um formulário inteiro, não em controles individuais gerados dinamicamente, como `<input>` e `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="9212a-501">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="9212a-502">Para uma nova análise do formulário, remova os dados de validação que foram adicionados ao formulário mesmo quando foi analisado anteriormente, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-502">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="9212a-503">Validação personalizada do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-503">Custom client-side validation</span></span>

<span data-ttu-id="9212a-504">A validação personalizada do lado do cliente é feita por meio da geração de atributos de HTML `data-` que funcionam com um adaptador do jQuery Validate personalizado.</span><span class="sxs-lookup"><span data-stu-id="9212a-504">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="9212a-505">O seguinte código do adaptador de exemplo foi escrito para os atributos `ClassicMovie` e `ClassicMovie2` que foram apresentados no início deste artigo:</span><span class="sxs-lookup"><span data-stu-id="9212a-505">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="9212a-506">Para obter informações sobre como escrever adaptadores, consulte a [documentação do jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="9212a-506">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="9212a-507">O uso de um adaptador para um determinado campo é disparado por atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="9212a-507">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="9212a-508">Marcam o campo como sujeito à validação do sinalizador (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="9212a-508">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="9212a-509">Identifique o nome de uma regra de validação e o texto da mensagem de erro (por exemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="9212a-509">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="9212a-510">Forneça os parâmetros adicionais que o validador precisa (por exemplo, `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="9212a-510">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="9212a-511">A exemplo a seguir mostra os atributos `data-` para o atributo `ClassicMovie` do aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-511">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="9212a-512">Conforme observado anteriormente, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e [Auxiliares de HTML](xref:mvc/views/overview) usam informações de atributos de validação para renderizar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="9212a-512">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="9212a-513">Há duas opções para escrever código que resulte na criação de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="9212a-513">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="9212a-514">Criar uma classe que deriva de `AttributeAdapterBase<TAttribute>` e uma classe que implementa `IValidationAttributeAdapterProvider` e registrar o atributo e o adaptador na DI.</span><span class="sxs-lookup"><span data-stu-id="9212a-514">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="9212a-515">Este método segue a [entidade de segurança de responsabilidade única](https://wikipedia.org/wiki/Single_responsibility_principle) em que o código de validação relacionado ao cliente e ao servidor fica em classes separadas.</span><span class="sxs-lookup"><span data-stu-id="9212a-515">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="9212a-516">O adaptador também tem a vantagem de que, uma vez que ele é registrado na DI, outros serviços da DI ficam disponíveis para ele, se necessário.</span><span class="sxs-lookup"><span data-stu-id="9212a-516">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="9212a-517">Implementar `IClientModelValidator` em na classe `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="9212a-517">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="9212a-518">Esse método poderá ser adequado se o atributo não fizer nenhuma validação do lado do servidor e não precisar de um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="9212a-518">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="9212a-519">AttributeAdapter para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-519">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="9212a-520">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9212a-520">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="9212a-521">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="9212a-521">To add client validation by using this method:</span></span>

1. <span data-ttu-id="9212a-522">Crie uma classe de adaptador de atributo para o atributo de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="9212a-522">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="9212a-523">Derive a classe de <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601> .</span><span class="sxs-lookup"><span data-stu-id="9212a-523">Derive the class from <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>.</span></span> <span data-ttu-id="9212a-524">Criar um método `AddValidation` que adiciona atributos `data-` à saída renderizada, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-524">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="9212a-525">Crie uma classe de provedor de adaptador que implementa <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="9212a-525">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="9212a-526">No método `GetAttributeAdapter`, passe o atributo personalizado para o construtor do adaptador, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="9212a-526">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="9212a-527">Registre o provedor de adaptador para DI em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9212a-527">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="9212a-528">IClientModelValidator para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-528">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="9212a-529">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie2` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9212a-529">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="9212a-530">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="9212a-530">To add client validation by using this method:</span></span>

* <span data-ttu-id="9212a-531">No atributo de validação personalizado, implemente a interface `IClientModelValidator` e crie um método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="9212a-531">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="9212a-532">No método `AddValidation`, adicione atributos `data-` para validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9212a-532">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="9212a-533">Desabilitar validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="9212a-533">Disable client-side validation</span></span>

<span data-ttu-id="9212a-534">O código a seguir desabilita a validação de cliente nas exibições do MVC:</span><span class="sxs-lookup"><span data-stu-id="9212a-534">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="9212a-535">E em Razor páginas:</span><span class="sxs-lookup"><span data-stu-id="9212a-535">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="9212a-536">Outra opção para desabilitar a validação do cliente é comentar a referência ao `_ValidationScriptsPartial` em seu arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9212a-536">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9212a-537">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9212a-537">Additional resources</span></span>

* [<span data-ttu-id="9212a-538">Namespace System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="9212a-538">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="9212a-539">Associação de modelo</span><span class="sxs-lookup"><span data-stu-id="9212a-539">Model Binding</span></span>](model-binding.md)

::: moniker-end
