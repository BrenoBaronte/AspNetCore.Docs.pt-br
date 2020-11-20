---
title: Model binding no ASP.NET Core
author: rick-anderson
description: Saiba como funciona o model binding no ASP.NET Core e como personalizar seu comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 4de34a75da932b41190caa8434ac5be8cc0710fd
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981928"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="cd1e4-103">Model binding no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd1e4-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd1e4-104">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="cd1e4-105">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="cd1e4-106">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="cd1e4-106">What is Model binding</span></span>

<span data-ttu-id="cd1e4-107">Os controladores e Razor as páginas funcionam com dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="cd1e4-108">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="cd1e4-109">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="cd1e4-110">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-110">Model binding automates this process.</span></span> <span data-ttu-id="cd1e4-111">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-111">The model binding system:</span></span>

* <span data-ttu-id="cd1e4-112">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="cd1e4-113">Fornece os dados para controladores e Razor páginas em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="cd1e4-114">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="cd1e4-115">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="cd1e4-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="cd1e4-116">Example</span></span>

<span data-ttu-id="cd1e4-117">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="cd1e4-118">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="cd1e4-119">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="cd1e4-120">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="cd1e4-121">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="cd1e4-122">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="cd1e4-123">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="cd1e4-124">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="cd1e4-125">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="cd1e4-126">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="cd1e4-127">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="cd1e4-128">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="cd1e4-129">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="cd1e4-130">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="cd1e4-131">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="cd1e4-132">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="cd1e4-133">Destinos</span><span class="sxs-lookup"><span data-stu-id="cd1e4-133">Targets</span></span>

<span data-ttu-id="cd1e4-134">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="cd1e4-135">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="cd1e4-136">Parâmetros do Razor método de manipulador Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="cd1e4-137">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="cd1e4-138">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-138">[BindProperty] attribute</span></span>

<span data-ttu-id="cd1e4-139">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="cd1e4-140">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-140">[BindProperties] attribute</span></span>

<span data-ttu-id="cd1e4-141">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="cd1e4-142">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="cd1e4-143">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="cd1e4-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="cd1e4-144">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="cd1e4-145">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="cd1e4-146">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="cd1e4-147">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="cd1e4-148">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="cd1e4-149">Origens</span><span class="sxs-lookup"><span data-stu-id="cd1e4-149">Sources</span></span>

<span data-ttu-id="cd1e4-150">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="cd1e4-151">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="cd1e4-151">Form fields</span></span>
1. <span data-ttu-id="cd1e4-152">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="cd1e4-153">Rotear dados</span><span class="sxs-lookup"><span data-stu-id="cd1e4-153">Route data</span></span>
1. <span data-ttu-id="cd1e4-154">Parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="cd1e4-154">Query string parameters</span></span>
1. <span data-ttu-id="cd1e4-155">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="cd1e4-155">Uploaded files</span></span>

<span data-ttu-id="cd1e4-156">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="cd1e4-157">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-157">There are a few exceptions:</span></span>

* <span data-ttu-id="cd1e4-158">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="cd1e4-159">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="cd1e4-160">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="cd1e4-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="cd1e4-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="cd1e4-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="cd1e4-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="cd1e4-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="cd1e4-166">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-166">These attributes:</span></span>

* <span data-ttu-id="cd1e4-167">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="cd1e4-168">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="cd1e4-169">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="cd1e4-170">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="cd1e4-171">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-171">[FromBody] attribute</span></span>

<span data-ttu-id="cd1e4-172">Aplique o `[FromBody]` atributo a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="cd1e4-173">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="cd1e4-174">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="cd1e4-175">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="cd1e4-176">Por exemplo, a ação a seguir `Create` especifica que seu `pet` parâmetro é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="cd1e4-177">A `Pet` classe especifica que sua `Breed` propriedade é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="cd1e4-178">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-178">In the preceding example:</span></span>

* <span data-ttu-id="cd1e4-179">O `[FromQuery]` atributo é ignorado.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="cd1e4-180">A `Breed` propriedade não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="cd1e4-181">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="cd1e4-182">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a `Breed` propriedade.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="cd1e4-183">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="cd1e4-184">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para ligar outros `[FromBody]` parâmetros.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="cd1e4-185">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="cd1e4-185">Additional sources</span></span>

<span data-ttu-id="cd1e4-186">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="cd1e4-187">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="cd1e4-188">Por exemplo, talvez você queira dados do cookie estado da sessão ou do.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="cd1e4-189">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-189">To get data from a new source:</span></span>

* <span data-ttu-id="cd1e4-190">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="cd1e4-191">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="cd1e4-192">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="cd1e4-193">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [fábrica](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookie s.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="cd1e4-194">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="cd1e4-195">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="cd1e4-196">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="cd1e4-197">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="cd1e4-197">No source for a model property</span></span>

<span data-ttu-id="cd1e4-198">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="cd1e4-199">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="cd1e4-200">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="cd1e4-201">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="cd1e4-202">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="cd1e4-203">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="cd1e4-204">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="cd1e4-205">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="cd1e4-206">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="cd1e4-207">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="cd1e4-208">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="cd1e4-208">Type conversion errors</span></span>

<span data-ttu-id="cd1e4-209">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="cd1e4-210">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="cd1e4-211">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="cd1e4-212">Em uma Razor página, reexiba a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="cd1e4-213">A validação no lado do cliente captura dados mais inválidos que, de outra forma, seriam enviados a um Razor formulário de páginas.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="cd1e4-214">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="cd1e4-215">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="cd1e4-216">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="cd1e4-217">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="cd1e4-218">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="cd1e4-219">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="cd1e4-220">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="cd1e4-221">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="cd1e4-222">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="cd1e4-223">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="cd1e4-223">Simple types</span></span>

<span data-ttu-id="cd1e4-224">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="cd1e4-225">Booliano</span><span class="sxs-lookup"><span data-stu-id="cd1e4-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="cd1e4-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="cd1e4-227">º</span><span class="sxs-lookup"><span data-stu-id="cd1e4-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="cd1e4-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="cd1e4-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="cd1e4-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="cd1e4-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="cd1e4-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="cd1e4-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="cd1e4-231">Double</span><span class="sxs-lookup"><span data-stu-id="cd1e4-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="cd1e4-232">Enumera</span><span class="sxs-lookup"><span data-stu-id="cd1e4-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="cd1e4-233">Volume</span><span class="sxs-lookup"><span data-stu-id="cd1e4-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="cd1e4-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="cd1e4-235">Single</span><span class="sxs-lookup"><span data-stu-id="cd1e4-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="cd1e4-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="cd1e4-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="cd1e4-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="cd1e4-238">Uri</span><span class="sxs-lookup"><span data-stu-id="cd1e4-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="cd1e4-239">Versão</span><span class="sxs-lookup"><span data-stu-id="cd1e4-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="cd1e4-240">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="cd1e4-240">Complex types</span></span>

<span data-ttu-id="cd1e4-241">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="cd1e4-242">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="cd1e4-243">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="cd1e4-244">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="cd1e4-245">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="cd1e4-246">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="cd1e4-247">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="cd1e4-248">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="cd1e4-249">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="cd1e4-249">Prefix = parameter name</span></span>

<span data-ttu-id="cd1e4-250">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="cd1e4-251">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="cd1e4-252">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="cd1e4-253">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="cd1e4-253">Prefix = property name</span></span>

<span data-ttu-id="cd1e4-254">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="cd1e4-255">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="cd1e4-256">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="cd1e4-257">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="cd1e4-257">Custom prefix</span></span>

<span data-ttu-id="cd1e4-258">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="cd1e4-259">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="cd1e4-260">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="cd1e4-261">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="cd1e4-261">Attributes for complex type targets</span></span>

<span data-ttu-id="cd1e4-262">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="cd1e4-263">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="cd1e4-264">Eles \***não** afetam os formatadores de entrada, que processam os corpos de solicitação JSON e XML lançados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-264">They do \***not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="cd1e4-265">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="cd1e4-266">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-266">[Bind] attribute</span></span>

<span data-ttu-id="cd1e4-267">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="cd1e4-268">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="cd1e4-269">`[Bind]` Não _*_afeta os_*_ formatadores de entrada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="cd1e4-270">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="cd1e4-271">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="cd1e4-272">O `[Bind]` atributo pode ser usado para proteger contra sobrepostos em cenários _create \*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="cd1e4-273">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="cd1e4-274">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="cd1e4-275">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="cd1e4-276">Atributo [ModelBinder]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="cd1e4-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> pode ser aplicado a tipos, propriedades ou parâmetros.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="cd1e4-278">Ele permite especificar o tipo de associador de modelo usado para associar a instância ou o tipo específico.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="cd1e4-279">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="cd1e4-280">O `[ModelBinder]` atributo também pode ser usado para alterar o nome de uma propriedade ou parâmetro quando ele está sendo associado a um modelo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="cd1e4-281">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-281">[BindRequired] attribute</span></span>

<span data-ttu-id="cd1e4-282">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="cd1e4-283">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="cd1e4-284">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="cd1e4-285">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="cd1e4-286">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-286">[BindNever] attribute</span></span>

<span data-ttu-id="cd1e4-287">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="cd1e4-288">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="cd1e4-289">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="cd1e4-290">Coleções</span><span class="sxs-lookup"><span data-stu-id="cd1e4-290">Collections</span></span>

<span data-ttu-id="cd1e4-291">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="cd1e4-292">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="cd1e4-293">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-293">For example:</span></span>

* <span data-ttu-id="cd1e4-294">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="cd1e4-295">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-295">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="cd1e4-296">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="cd1e4-297">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="cd1e4-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="cd1e4-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="cd1e4-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="cd1e4-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="cd1e4-300">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="cd1e4-301">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="cd1e4-302">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="cd1e4-303">Dicionários</span><span class="sxs-lookup"><span data-stu-id="cd1e4-303">Dictionaries</span></span>

<span data-ttu-id="cd1e4-304">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="cd1e4-305">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="cd1e4-306">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-306">For example:</span></span>

* <span data-ttu-id="cd1e4-307">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="cd1e4-308">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-308">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="cd1e4-309">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="cd1e4-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="cd1e4-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="cd1e4-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="cd1e4-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="cd1e4-312">Associação de construtor e tipos de registro</span><span class="sxs-lookup"><span data-stu-id="cd1e4-312">Constructor binding and record types</span></span>

<span data-ttu-id="cd1e4-313">A associação de modelo requer que os tipos complexos tenham um construtor sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="cd1e4-314">Os `System.Text.Json` `Newtonsoft.Json` formatadores de entrada e com base dão suporte à desserialização de classes que não têm um construtor sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="cd1e4-315">O C# 9 apresenta tipos de registro, que são uma ótima maneira de representar de forma sucinta os dados pela rede.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="cd1e4-316">ASP.NET Core adiciona suporte para associação de modelo e validação de tipos de registro com um único Construtor:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="cd1e4-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="cd1e4-318">Ao validar tipos de registro, o tempo de execução pesquisa os metadados de validação especificamente em parâmetros em vez de em Propriedades.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="cd1e4-319">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="cd1e4-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="cd1e4-320">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="cd1e4-321">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="cd1e4-322">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="cd1e4-323">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="cd1e4-324">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="cd1e4-325">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="cd1e4-326">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="cd1e4-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="cd1e4-327">Copie o código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="cd1e4-328">Substitua o [valor de cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="cd1e4-329">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="cd1e4-330">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="cd1e4-330">Special data types</span></span>

<span data-ttu-id="cd1e4-331">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="cd1e4-332">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="cd1e4-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="cd1e4-333">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="cd1e4-334">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="cd1e4-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="cd1e4-335">CancellationToken</span></span>

<span data-ttu-id="cd1e4-336">As ações podem, opcionalmente, associar um `CancellationToken` como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-336">Actions can optionally bind a `CancellationToken` as a parameter.</span></span> <span data-ttu-id="cd1e4-337">Isso associa os <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> sinais quando a conexão subjacente à solicitação HTTP é anulada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-337">This binds <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> that signals when the connection underlying the HTTP request is aborted.</span></span> <span data-ttu-id="cd1e4-338">As ações podem usar esse parâmetro para cancelar operações assíncronas de execução longa que são executadas como parte das ações do controlador.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-338">Actions can use this parameter to cancel long running async operations that are executed as part of the controller actions.</span></span>

### <a name="formcollection"></a><span data-ttu-id="cd1e4-339">FormCollection</span><span class="sxs-lookup"><span data-stu-id="cd1e4-339">FormCollection</span></span>

<span data-ttu-id="cd1e4-340">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-340">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="cd1e4-341">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="cd1e4-341">Input formatters</span></span>

<span data-ttu-id="cd1e4-342">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-342">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="cd1e4-343">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-343">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="cd1e4-344">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-344">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="cd1e4-345">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-345">You can add other formatters for other content types.</span></span>

<span data-ttu-id="cd1e4-346">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-346">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="cd1e4-347">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-347">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="cd1e4-348">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-348">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="cd1e4-349">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-349">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="cd1e4-350">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-350">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="cd1e4-351">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-351">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="cd1e4-352">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-352">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="cd1e4-353">Personalizar a associação de modelo com formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="cd1e4-353">Customize model binding with input formatters</span></span>

<span data-ttu-id="cd1e4-354">Um formatador de entrada assume total responsabilidade pela leitura de dados do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-354">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="cd1e4-355">Para personalizar esse processo, configure as APIs usadas pelo formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-355">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="cd1e4-356">Esta seção descreve como personalizar o `System.Text.Json` formatador de entrada baseado em dados para entender um tipo personalizado chamado `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="cd1e4-356">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="cd1e4-357">Considere o seguinte modelo, que contém uma `ObjectId` propriedade personalizada chamada `Id` :</span><span class="sxs-lookup"><span data-stu-id="cd1e4-357">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="cd1e4-358">Para personalizar o processo de associação de modelo ao usar o `System.Text.Json` , crie uma classe derivada de <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="cd1e4-358">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="cd1e4-359">Para usar um conversor personalizado, aplique o <xref:System.Text.Json.Serialization.JsonConverterAttribute> atributo ao tipo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-359">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="cd1e4-360">No exemplo a seguir, o `ObjectId` tipo é configurado com `ObjectIdConverter` como seu conversor personalizado:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-360">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="cd1e4-361">Para obter mais informações, consulte [como escrever conversores personalizados](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-361">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="cd1e4-362">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="cd1e4-362">Exclude specified types from model binding</span></span>

<span data-ttu-id="cd1e4-363">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-363">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="cd1e4-364">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-364">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="cd1e4-365">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-365">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="cd1e4-366">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-366">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cd1e4-367">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-367">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="cd1e4-368">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-368">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cd1e4-369">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-369">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="cd1e4-370">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="cd1e4-370">Custom model binders</span></span>

<span data-ttu-id="cd1e4-371">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-371">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="cd1e4-372">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-372">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="cd1e4-373">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="cd1e4-373">Manual model binding</span></span> 

<span data-ttu-id="cd1e4-374">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-374">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="cd1e4-375">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-375">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="cd1e4-376">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-376">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="cd1e4-377">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-377">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="cd1e4-378">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-378">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="cd1e4-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  usa provedores de valor para obter dados do corpo do formulário, Cadeia de caracteres de consulta e dados de rota.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="cd1e4-380">`TryUpdateModelAsync` normalmente é:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-380">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="cd1e4-381">Usado com Razor páginas e aplicativos MVC usando controladores e exibições para evitar o excesso de postagens.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-381">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="cd1e4-382">Não usado com uma API da Web, a menos que consumido de dados de formulário, cadeias de caracteres de consulta e dados de rota.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-382">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="cd1e4-383">Os pontos de extremidade da API Web que consomem JSON usam [formatadores de entrada](#input-formatters) para desserializar o corpo da solicitação em um objeto.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-383">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="cd1e4-384">Para obter mais informações, consulte [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-384">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="cd1e4-385">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-385">[FromServices] attribute</span></span>

<span data-ttu-id="cd1e4-386">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-386">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="cd1e4-387">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-387">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="cd1e4-388">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-388">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="cd1e4-389">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-389">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd1e4-390">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cd1e4-390">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd1e4-391">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-391">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="cd1e4-392">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-392">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="cd1e4-393">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="cd1e4-393">What is Model binding</span></span>

<span data-ttu-id="cd1e4-394">Os controladores e Razor as páginas funcionam com dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-394">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="cd1e4-395">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-395">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="cd1e4-396">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-396">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="cd1e4-397">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-397">Model binding automates this process.</span></span> <span data-ttu-id="cd1e4-398">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-398">The model binding system:</span></span>

* <span data-ttu-id="cd1e4-399">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-399">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="cd1e4-400">Fornece os dados para controladores e Razor páginas em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-400">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="cd1e4-401">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-401">Converts string data to .NET types.</span></span>
* <span data-ttu-id="cd1e4-402">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-402">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="cd1e4-403">Exemplo</span><span class="sxs-lookup"><span data-stu-id="cd1e4-403">Example</span></span>

<span data-ttu-id="cd1e4-404">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-404">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="cd1e4-405">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-405">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="cd1e4-406">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-406">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="cd1e4-407">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-407">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="cd1e4-408">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-408">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="cd1e4-409">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-409">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="cd1e4-410">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-410">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="cd1e4-411">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-411">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="cd1e4-412">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-412">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="cd1e4-413">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-413">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="cd1e4-414">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-414">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="cd1e4-415">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-415">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="cd1e4-416">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-416">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="cd1e4-417">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-417">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="cd1e4-418">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-418">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="cd1e4-419">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-419">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="cd1e4-420">Destinos</span><span class="sxs-lookup"><span data-stu-id="cd1e4-420">Targets</span></span>

<span data-ttu-id="cd1e4-421">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-421">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="cd1e4-422">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-422">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="cd1e4-423">Parâmetros do Razor método de manipulador Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-423">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="cd1e4-424">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-424">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="cd1e4-425">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-425">[BindProperty] attribute</span></span>

<span data-ttu-id="cd1e4-426">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-426">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="cd1e4-427">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-427">[BindProperties] attribute</span></span>

<span data-ttu-id="cd1e4-428">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-428">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="cd1e4-429">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-429">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="cd1e4-430">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="cd1e4-430">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="cd1e4-431">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-431">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="cd1e4-432">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-432">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="cd1e4-433">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-433">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="cd1e4-434">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-434">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="cd1e4-435">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-435">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="cd1e4-436">Origens</span><span class="sxs-lookup"><span data-stu-id="cd1e4-436">Sources</span></span>

<span data-ttu-id="cd1e4-437">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-437">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="cd1e4-438">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="cd1e4-438">Form fields</span></span>
1. <span data-ttu-id="cd1e4-439">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-439">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="cd1e4-440">Rotear dados</span><span class="sxs-lookup"><span data-stu-id="cd1e4-440">Route data</span></span>
1. <span data-ttu-id="cd1e4-441">Parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="cd1e4-441">Query string parameters</span></span>
1. <span data-ttu-id="cd1e4-442">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="cd1e4-442">Uploaded files</span></span>

<span data-ttu-id="cd1e4-443">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-443">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="cd1e4-444">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-444">There are a few exceptions:</span></span>

* <span data-ttu-id="cd1e4-445">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-445">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="cd1e4-446">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-446">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="cd1e4-447">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-447">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="cd1e4-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="cd1e4-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="cd1e4-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="cd1e4-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="cd1e4-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="cd1e4-453">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-453">These attributes:</span></span>

* <span data-ttu-id="cd1e4-454">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-454">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="cd1e4-455">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-455">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="cd1e4-456">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-456">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="cd1e4-457">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-457">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="cd1e4-458">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-458">[FromBody] attribute</span></span>

<span data-ttu-id="cd1e4-459">Aplique o `[FromBody]` atributo a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-459">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="cd1e4-460">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-460">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="cd1e4-461">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-461">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="cd1e4-462">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-462">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="cd1e4-463">Por exemplo, a ação a seguir `Create` especifica que seu `pet` parâmetro é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-463">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="cd1e4-464">A `Pet` classe especifica que sua `Breed` propriedade é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-464">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="cd1e4-465">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-465">In the preceding example:</span></span>

* <span data-ttu-id="cd1e4-466">O `[FromQuery]` atributo é ignorado.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-466">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="cd1e4-467">A `Breed` propriedade não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-467">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="cd1e4-468">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-468">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="cd1e4-469">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a `Breed` propriedade.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-469">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="cd1e4-470">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-470">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="cd1e4-471">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para ligar outros `[FromBody]` parâmetros.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-471">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="cd1e4-472">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="cd1e4-472">Additional sources</span></span>

<span data-ttu-id="cd1e4-473">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-473">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="cd1e4-474">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-474">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="cd1e4-475">Por exemplo, talvez você queira dados do cookie estado da sessão ou do.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-475">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="cd1e4-476">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-476">To get data from a new source:</span></span>

* <span data-ttu-id="cd1e4-477">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-477">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="cd1e4-478">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-478">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="cd1e4-479">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-479">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="cd1e4-480">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [fábrica](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookie s.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-480">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="cd1e4-481">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-481">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="cd1e4-482">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-482">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="cd1e4-483">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-483">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="cd1e4-484">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="cd1e4-484">No source for a model property</span></span>

<span data-ttu-id="cd1e4-485">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-485">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="cd1e4-486">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-486">The property is set to null or a default value:</span></span>

* <span data-ttu-id="cd1e4-487">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-487">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="cd1e4-488">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-488">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="cd1e4-489">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-489">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="cd1e4-490">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-490">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="cd1e4-491">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-491">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="cd1e4-492">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-492">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="cd1e4-493">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-493">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="cd1e4-494">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-494">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="cd1e4-495">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="cd1e4-495">Type conversion errors</span></span>

<span data-ttu-id="cd1e4-496">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-496">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="cd1e4-497">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-497">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="cd1e4-498">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-498">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="cd1e4-499">Em uma Razor página, reexiba a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-499">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="cd1e4-500">A validação no lado do cliente captura dados mais inválidos que, de outra forma, seriam enviados a um Razor formulário de páginas.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-500">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="cd1e4-501">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-501">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="cd1e4-502">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-502">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="cd1e4-503">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-503">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="cd1e4-504">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-504">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="cd1e4-505">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-505">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="cd1e4-506">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-506">The invalid input does appear in an error message.</span></span> <span data-ttu-id="cd1e4-507">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-507">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="cd1e4-508">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-508">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="cd1e4-509">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-509">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="cd1e4-510">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="cd1e4-510">Simple types</span></span>

<span data-ttu-id="cd1e4-511">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-511">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="cd1e4-512">Booliano</span><span class="sxs-lookup"><span data-stu-id="cd1e4-512">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="cd1e4-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="cd1e4-514">º</span><span class="sxs-lookup"><span data-stu-id="cd1e4-514">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="cd1e4-515">DateTime</span><span class="sxs-lookup"><span data-stu-id="cd1e4-515">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="cd1e4-516">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="cd1e4-516">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="cd1e4-517">Decimal</span><span class="sxs-lookup"><span data-stu-id="cd1e4-517">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="cd1e4-518">Double</span><span class="sxs-lookup"><span data-stu-id="cd1e4-518">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="cd1e4-519">Enumera</span><span class="sxs-lookup"><span data-stu-id="cd1e4-519">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="cd1e4-520">Volume</span><span class="sxs-lookup"><span data-stu-id="cd1e4-520">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="cd1e4-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="cd1e4-522">Single</span><span class="sxs-lookup"><span data-stu-id="cd1e4-522">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="cd1e4-523">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="cd1e4-523">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="cd1e4-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="cd1e4-525">Uri</span><span class="sxs-lookup"><span data-stu-id="cd1e4-525">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="cd1e4-526">Versão</span><span class="sxs-lookup"><span data-stu-id="cd1e4-526">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="cd1e4-527">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="cd1e4-527">Complex types</span></span>

<span data-ttu-id="cd1e4-528">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-528">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="cd1e4-529">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-529">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="cd1e4-530">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-530">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="cd1e4-531">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-531">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="cd1e4-532">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-532">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="cd1e4-533">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-533">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="cd1e4-534">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-534">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="cd1e4-535">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-535">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="cd1e4-536">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="cd1e4-536">Prefix = parameter name</span></span>

<span data-ttu-id="cd1e4-537">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-537">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="cd1e4-538">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-538">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="cd1e4-539">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-539">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="cd1e4-540">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="cd1e4-540">Prefix = property name</span></span>

<span data-ttu-id="cd1e4-541">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-541">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="cd1e4-542">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-542">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="cd1e4-543">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-543">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="cd1e4-544">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="cd1e4-544">Custom prefix</span></span>

<span data-ttu-id="cd1e4-545">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-545">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="cd1e4-546">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-546">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="cd1e4-547">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-547">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="cd1e4-548">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="cd1e4-548">Attributes for complex type targets</span></span>

<span data-ttu-id="cd1e4-549">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-549">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="cd1e4-550">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-550">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="cd1e4-551">Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-551">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="cd1e4-552">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-552">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="cd1e4-553">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-553">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="cd1e4-554">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-554">[BindRequired] attribute</span></span>

<span data-ttu-id="cd1e4-555">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-555">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="cd1e4-556">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-556">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="cd1e4-557">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-557">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="cd1e4-558">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-558">[BindNever] attribute</span></span>

<span data-ttu-id="cd1e4-559">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-559">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="cd1e4-560">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-560">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="cd1e4-561">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-561">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="cd1e4-562">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-562">[Bind] attribute</span></span>

<span data-ttu-id="cd1e4-563">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-563">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="cd1e4-564">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-564">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="cd1e4-565">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-565">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="cd1e4-566">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-566">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="cd1e4-567">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-567">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="cd1e4-568">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-568">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="cd1e4-569">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-569">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="cd1e4-570">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-570">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="cd1e4-571">Coleções</span><span class="sxs-lookup"><span data-stu-id="cd1e4-571">Collections</span></span>

<span data-ttu-id="cd1e4-572">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-572">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="cd1e4-573">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-573">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="cd1e4-574">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-574">For example:</span></span>

* <span data-ttu-id="cd1e4-575">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-575">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="cd1e4-576">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-576">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="cd1e4-577">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-577">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="cd1e4-578">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-578">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="cd1e4-579">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="cd1e4-579">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="cd1e4-580">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="cd1e4-580">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="cd1e4-581">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-581">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="cd1e4-582">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-582">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="cd1e4-583">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-583">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="cd1e4-584">Dicionários</span><span class="sxs-lookup"><span data-stu-id="cd1e4-584">Dictionaries</span></span>

<span data-ttu-id="cd1e4-585">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-585">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="cd1e4-586">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-586">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="cd1e4-587">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-587">For example:</span></span>

* <span data-ttu-id="cd1e4-588">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-588">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="cd1e4-589">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-589">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="cd1e4-590">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-590">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="cd1e4-591">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="cd1e4-591">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="cd1e4-592">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="cd1e4-592">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="cd1e4-593">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="cd1e4-593">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="cd1e4-594">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-594">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="cd1e4-595">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-595">Treat values as invariant culture.</span></span>
* <span data-ttu-id="cd1e4-596">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-596">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="cd1e4-597">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-597">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="cd1e4-598">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-598">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="cd1e4-599">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-599">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="cd1e4-600">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="cd1e4-600">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="cd1e4-601">Copie o código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-601">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="cd1e4-602">Substitua o [valor de cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-602">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="cd1e4-603">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-603">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="cd1e4-604">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="cd1e4-604">Special data types</span></span>

<span data-ttu-id="cd1e4-605">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-605">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="cd1e4-606">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="cd1e4-606">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="cd1e4-607">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-607">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="cd1e4-608">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-608">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="cd1e4-609">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="cd1e4-609">CancellationToken</span></span>

<span data-ttu-id="cd1e4-610">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-610">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="cd1e4-611">FormCollection</span><span class="sxs-lookup"><span data-stu-id="cd1e4-611">FormCollection</span></span>

<span data-ttu-id="cd1e4-612">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-612">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="cd1e4-613">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="cd1e4-613">Input formatters</span></span>

<span data-ttu-id="cd1e4-614">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-614">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="cd1e4-615">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-615">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="cd1e4-616">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-616">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="cd1e4-617">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-617">You can add other formatters for other content types.</span></span>

<span data-ttu-id="cd1e4-618">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-618">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="cd1e4-619">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-619">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="cd1e4-620">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-620">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="cd1e4-621">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-621">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="cd1e4-622">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-622">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="cd1e4-623">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-623">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="cd1e4-624">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-624">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="cd1e4-625">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="cd1e4-625">Exclude specified types from model binding</span></span>

<span data-ttu-id="cd1e4-626">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-626">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="cd1e4-627">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-627">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="cd1e4-628">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-628">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="cd1e4-629">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-629">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cd1e4-630">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-630">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="cd1e4-631">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-631">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cd1e4-632">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-632">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="cd1e4-633">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="cd1e4-633">Custom model binders</span></span>

<span data-ttu-id="cd1e4-634">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-634">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="cd1e4-635">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-635">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="cd1e4-636">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="cd1e4-636">Manual model binding</span></span>

<span data-ttu-id="cd1e4-637">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-637">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="cd1e4-638">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-638">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="cd1e4-639">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-639">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="cd1e4-640">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-640">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="cd1e4-641">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-641">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="cd1e4-642">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="cd1e4-642">[FromServices] attribute</span></span>

<span data-ttu-id="cd1e4-643">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-643">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="cd1e4-644">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-644">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="cd1e4-645">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-645">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="cd1e4-646">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-646">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd1e4-647">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cd1e4-647">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
