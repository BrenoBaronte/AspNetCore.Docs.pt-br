---
title: Model binding personalizado no ASP.NET Core
author: ardalis
description: Saiba como o model binding permite que as ações do controlador trabalhem diretamente com os tipos de modelo no ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 7675e95c43b9ee428ee5fda86ea3ead9815ed645
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058461"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="583a6-103">Model binding personalizado no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="583a6-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="583a6-104">Por [Steve Smith](https://ardalis.com/) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="583a6-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="583a6-105">O model binding permite que as ações do controlador funcionem diretamente com tipos de modelo (passados como argumentos de método), em vez de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="583a6-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="583a6-106">O mapeamento entre os dados de solicitação de entrada e os modelos de aplicativo é manipulado por associadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="583a6-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="583a6-107">Os desenvolvedores podem estender a funcionalidade de model binding interna implementando associadores de modelos personalizados (embora, normalmente, você não precise escrever seu próprio provedor).</span><span class="sxs-lookup"><span data-stu-id="583a6-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="583a6-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="583a6-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="583a6-109">Limitações dos associadores de modelos padrão</span><span class="sxs-lookup"><span data-stu-id="583a6-109">Default model binder limitations</span></span>

<span data-ttu-id="583a6-110">Os associadores de modelos padrão dão suporte à maioria dos tipos de dados comuns do .NET Core e devem atender à maior parte das necessidades dos desenvolvedores.</span><span class="sxs-lookup"><span data-stu-id="583a6-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="583a6-111">Eles esperam associar a entrada baseada em texto da solicitação diretamente a tipos de modelo.</span><span class="sxs-lookup"><span data-stu-id="583a6-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="583a6-112">Talvez seja necessário transformar a entrada antes de associá-la.</span><span class="sxs-lookup"><span data-stu-id="583a6-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="583a6-113">Por exemplo, quando você tem uma chave que pode ser usada para pesquisar dados de modelo.</span><span class="sxs-lookup"><span data-stu-id="583a6-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="583a6-114">Use um associador de modelos personalizado para buscar dados com base na chave.</span><span class="sxs-lookup"><span data-stu-id="583a6-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="583a6-115">Análise do model binding</span><span class="sxs-lookup"><span data-stu-id="583a6-115">Model binding review</span></span>

<span data-ttu-id="583a6-116">O model binding usa definições específicas para os tipos nos quais opera.</span><span class="sxs-lookup"><span data-stu-id="583a6-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="583a6-117">Um *tipo simples* é convertido de uma única cadeia de caracteres na entrada.</span><span class="sxs-lookup"><span data-stu-id="583a6-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="583a6-118">Um *tipo complexo* é convertido de vários valores de entrada.</span><span class="sxs-lookup"><span data-stu-id="583a6-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="583a6-119">A estrutura determina a diferença de acordo com a existência de um `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="583a6-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="583a6-120">Recomendamos que você crie um conversor de tipo se tiver um mapeamento `string` -> `SomeType` simples que não exige recursos externos.</span><span class="sxs-lookup"><span data-stu-id="583a6-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="583a6-121">Antes de criar seu próprio associador de modelos personalizado, vale a pena analisar como os associadores de modelos existentes são implementados.</span><span class="sxs-lookup"><span data-stu-id="583a6-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="583a6-122">Considere o <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> que pode ser usado para converter cadeias de caracteres codificadas em base64 em matrizes de bytes.</span><span class="sxs-lookup"><span data-stu-id="583a6-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="583a6-123">As matrizes de bytes costumam ser armazenadas como arquivos ou campos BLOB do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="583a6-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="583a6-124">Trabalhando com o ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="583a6-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="583a6-125">Cadeias de caracteres codificadas em Base64 podem ser usadas para representar dados binários.</span><span class="sxs-lookup"><span data-stu-id="583a6-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="583a6-126">Por exemplo, uma imagem pode ser codificada como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="583a6-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="583a6-127">O exemplo inclui uma imagem como uma cadeia de caracteres codificada em base64 no [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="583a6-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="583a6-128">O ASP.NET Core MVC pode usar uma cadeia de caracteres codificada em Base64 e usar um `ByteArrayModelBinder` para convertê-la em uma matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="583a6-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="583a6-129">Os <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> argumentos de mapas `byte[]` para `ByteArrayModelBinder` :</span><span class="sxs-lookup"><span data-stu-id="583a6-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="583a6-130">Ao criar seu próprio associador de modelo personalizado, você pode implementar seu próprio `IModelBinderProvider` tipo ou usar o <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .</span><span class="sxs-lookup"><span data-stu-id="583a6-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="583a6-131">O seguinte exemplo mostra como usar `ByteArrayModelBinder` para converter uma cadeia de caracteres codificada em Base64 em um `byte[]` e salvar o resultado em um arquivo:</span><span class="sxs-lookup"><span data-stu-id="583a6-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="583a6-132">Execute POST em uma cadeia de caracteres codificada em Base64 para esse método de API usando uma ferramenta como o [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="583a6-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="583a6-133">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="583a6-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="583a6-134">Desde que o associador possa associar dados de solicitação a propriedades ou argumentos nomeados de forma adequada, o model binding terá êxito.</span><span class="sxs-lookup"><span data-stu-id="583a6-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="583a6-135">O seguinte exemplo mostra como usar `ByteArrayModelBinder` com um modelo de exibição:</span><span class="sxs-lookup"><span data-stu-id="583a6-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="583a6-136">Amostra de associador de modelos personalizado</span><span class="sxs-lookup"><span data-stu-id="583a6-136">Custom model binder sample</span></span>

<span data-ttu-id="583a6-137">Nesta seção, implementaremos um associador de modelos personalizado que:</span><span class="sxs-lookup"><span data-stu-id="583a6-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="583a6-138">Converte dados de solicitação de entrada em argumentos de chave fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="583a6-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="583a6-139">Usa o Entity Framework Core para buscar a entidade associada.</span><span class="sxs-lookup"><span data-stu-id="583a6-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="583a6-140">Passa a entidade associada como um argumento para o método de ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="583a6-141">A seguinte amostra usa o atributo `ModelBinder` no modelo `Author`:</span><span class="sxs-lookup"><span data-stu-id="583a6-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="583a6-142">No código anterior, o atributo `ModelBinder` especifica o tipo de `IModelBinder` que deve ser usado para associar parâmetros de ação `Author`.</span><span class="sxs-lookup"><span data-stu-id="583a6-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="583a6-143">A classe `AuthorEntityBinder` a seguir associa um parâmetro `Author` efetuando fetch da entidade de uma fonte de dados usando o Entity Framework Core e um `authorId`:</span><span class="sxs-lookup"><span data-stu-id="583a6-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="583a6-144">A classe `AuthorEntityBinder` precedente é destinada a ilustrar um associador de modelos personalizado.</span><span class="sxs-lookup"><span data-stu-id="583a6-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="583a6-145">A classe não é destinada a ilustrar as melhores práticas para um cenário de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="583a6-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="583a6-146">Para pesquisa, associe o `authorId` e consulte o banco de dados em um método de ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="583a6-147">Essa abordagem separa falhas de model binding de casos de `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="583a6-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="583a6-148">O seguinte código mostra como usar o `AuthorEntityBinder` em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="583a6-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="583a6-149">O atributo `ModelBinder` pode ser usado para aplicar o `AuthorEntityBinder` aos parâmetros que não usam convenções padrão:</span><span class="sxs-lookup"><span data-stu-id="583a6-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="583a6-150">Neste exemplo, como o nome do argumento não é o `authorId` padrão, ele é especificado no parâmetro com o atributo `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="583a6-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="583a6-151">O controlador e o método de ação são simplificados em comparação com a pesquisa da entidade no método de ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="583a6-152">A lógica para buscar o autor usando o Entity Framework Core é movida para o associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="583a6-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="583a6-153">Isso pode ser uma simplificação considerável quando há vários métodos associados ao modelo `Author`.</span><span class="sxs-lookup"><span data-stu-id="583a6-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="583a6-154">Aplique o atributo `ModelBinder` a propriedades de modelo individuais (como em um viewmodel) ou a parâmetros de método de ação para especificar um associador de modelos ou nome de modelo específico para apenas esse tipo ou essa ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="583a6-155">Implementando um ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="583a6-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="583a6-156">Em vez de aplicar um atributo, você pode implementar `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="583a6-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="583a6-157">É assim que os associadores de estrutura interna são implementados.</span><span class="sxs-lookup"><span data-stu-id="583a6-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="583a6-158">Quando você especifica o tipo no qual o associador opera, você especifica o tipo de argumento que ele produz, **não** a entrada aceita pelo associador.</span><span class="sxs-lookup"><span data-stu-id="583a6-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="583a6-159">O provedor de associador a seguir funciona com o `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="583a6-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="583a6-160">Quando ele for adicionado à coleção do MVC de provedores, não será necessário usar o atributo `ModelBinder` nos parâmetros `Author` ou de tipo `Author`.</span><span class="sxs-lookup"><span data-stu-id="583a6-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="583a6-161">Observação: o código anterior retorna um `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="583a6-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="583a6-162">O `BinderTypeModelBinder` atua como um alocador para associadores de modelos e fornece a DI (injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="583a6-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="583a6-163">O `AuthorEntityBinder` exige que a DI acesse o EF Core.</span><span class="sxs-lookup"><span data-stu-id="583a6-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="583a6-164">Use `BinderTypeModelBinder` se o associador de modelos exigir serviços da DI.</span><span class="sxs-lookup"><span data-stu-id="583a6-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="583a6-165">Para usar um provedor de associador de modelos personalizado, adicione-o a `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="583a6-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="583a6-166">Ao avaliar associadores de modelos, a coleção de provedores é examinada na ordem.</span><span class="sxs-lookup"><span data-stu-id="583a6-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="583a6-167">O primeiro provedor que retorna um fichário que corresponde ao modelo de entrada é usado.</span><span class="sxs-lookup"><span data-stu-id="583a6-167">The first provider that returns a binder that matches the input model is used.</span></span> <span data-ttu-id="583a6-168">A adição do seu provedor ao final da coleção pode resultar na chamada de um associador de modelo interno antes que o seu associador personalizado tenha uma chance.</span><span class="sxs-lookup"><span data-stu-id="583a6-168">Adding your provider to the end of the collection may thus result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="583a6-169">Neste exemplo, o provedor personalizado é adicionado ao início da coleção para garantir que ele sempre seja usado para argumentos de `Author` ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-169">In this example, the custom provider is added to the beginning of the collection to ensure it's always used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="583a6-170">Associação de modelo polimórfico</span><span class="sxs-lookup"><span data-stu-id="583a6-170">Polymorphic model binding</span></span>

<span data-ttu-id="583a6-171">A associação a diferentes modelos de tipos derivados é conhecida como associação de modelo polimórfico.</span><span class="sxs-lookup"><span data-stu-id="583a6-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="583a6-172">A associação de modelo personalizado polimórfico é necessária quando o valor da solicitação deve ser associado ao tipo de modelo derivado específico.</span><span class="sxs-lookup"><span data-stu-id="583a6-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="583a6-173">Associação de modelo polimórfico:</span><span class="sxs-lookup"><span data-stu-id="583a6-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="583a6-174">Não é comum para uma API REST projetada para interoperar com todos os idiomas.</span><span class="sxs-lookup"><span data-stu-id="583a6-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="583a6-175">Dificulta a razão dos modelos associados.</span><span class="sxs-lookup"><span data-stu-id="583a6-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="583a6-176">No entanto, se um aplicativo exigir Associação de modelo polimórfico, uma implementação poderá ser semelhante ao seguinte código:</span><span class="sxs-lookup"><span data-stu-id="583a6-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="583a6-177">Recomendações e melhores práticas</span><span class="sxs-lookup"><span data-stu-id="583a6-177">Recommendations and best practices</span></span>

<span data-ttu-id="583a6-178">Associadores de modelos personalizados:</span><span class="sxs-lookup"><span data-stu-id="583a6-178">Custom model binders:</span></span>

- <span data-ttu-id="583a6-179">Não devem tentar definir códigos de status ou retornar resultados (por exemplo, 404 Não Encontrado).</span><span class="sxs-lookup"><span data-stu-id="583a6-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="583a6-180">Se o model binding falhar, um [filtro de ação](xref:mvc/controllers/filters) ou uma lógica no próprio método de ação deverá resolver a falha.</span><span class="sxs-lookup"><span data-stu-id="583a6-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="583a6-181">São muito úteis para eliminar código repetitivo e interesses paralelos de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="583a6-182">Normalmente, não devem ser usados para converter uma cadeia de caracteres em um tipo personalizado; um <xref:System.ComponentModel.TypeConverter> geralmente é uma opção melhor.</span><span class="sxs-lookup"><span data-stu-id="583a6-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="583a6-183">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="583a6-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="583a6-184">O model binding permite que as ações do controlador funcionem diretamente com tipos de modelo (passados como argumentos de método), em vez de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="583a6-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="583a6-185">O mapeamento entre os dados de solicitação de entrada e os modelos de aplicativo é manipulado por associadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="583a6-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="583a6-186">Os desenvolvedores podem estender a funcionalidade de model binding interna implementando associadores de modelos personalizados (embora, normalmente, você não precise escrever seu próprio provedor).</span><span class="sxs-lookup"><span data-stu-id="583a6-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="583a6-187">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="583a6-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="583a6-188">Limitações dos associadores de modelos padrão</span><span class="sxs-lookup"><span data-stu-id="583a6-188">Default model binder limitations</span></span>

<span data-ttu-id="583a6-189">Os associadores de modelos padrão dão suporte à maioria dos tipos de dados comuns do .NET Core e devem atender à maior parte das necessidades dos desenvolvedores.</span><span class="sxs-lookup"><span data-stu-id="583a6-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="583a6-190">Eles esperam associar a entrada baseada em texto da solicitação diretamente a tipos de modelo.</span><span class="sxs-lookup"><span data-stu-id="583a6-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="583a6-191">Talvez seja necessário transformar a entrada antes de associá-la.</span><span class="sxs-lookup"><span data-stu-id="583a6-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="583a6-192">Por exemplo, quando você tem uma chave que pode ser usada para pesquisar dados de modelo.</span><span class="sxs-lookup"><span data-stu-id="583a6-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="583a6-193">Use um associador de modelos personalizado para buscar dados com base na chave.</span><span class="sxs-lookup"><span data-stu-id="583a6-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="583a6-194">Análise do model binding</span><span class="sxs-lookup"><span data-stu-id="583a6-194">Model binding review</span></span>

<span data-ttu-id="583a6-195">O model binding usa definições específicas para os tipos nos quais opera.</span><span class="sxs-lookup"><span data-stu-id="583a6-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="583a6-196">Um *tipo simples* é convertido de uma única cadeia de caracteres na entrada.</span><span class="sxs-lookup"><span data-stu-id="583a6-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="583a6-197">Um *tipo complexo* é convertido de vários valores de entrada.</span><span class="sxs-lookup"><span data-stu-id="583a6-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="583a6-198">A estrutura determina a diferença de acordo com a existência de um `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="583a6-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="583a6-199">Recomendamos que você crie um conversor de tipo se tiver um mapeamento `string` -> `SomeType` simples que não exige recursos externos.</span><span class="sxs-lookup"><span data-stu-id="583a6-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="583a6-200">Antes de criar seu próprio associador de modelos personalizado, vale a pena analisar como os associadores de modelos existentes são implementados.</span><span class="sxs-lookup"><span data-stu-id="583a6-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="583a6-201">Considere o <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> que pode ser usado para converter cadeias de caracteres codificadas em base64 em matrizes de bytes.</span><span class="sxs-lookup"><span data-stu-id="583a6-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="583a6-202">As matrizes de bytes costumam ser armazenadas como arquivos ou campos BLOB do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="583a6-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="583a6-203">Trabalhando com o ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="583a6-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="583a6-204">Cadeias de caracteres codificadas em Base64 podem ser usadas para representar dados binários.</span><span class="sxs-lookup"><span data-stu-id="583a6-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="583a6-205">Por exemplo, uma imagem pode ser codificada como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="583a6-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="583a6-206">O exemplo inclui uma imagem como uma cadeia de caracteres codificada em base64 no [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="583a6-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="583a6-207">O ASP.NET Core MVC pode usar uma cadeia de caracteres codificada em Base64 e usar um `ByteArrayModelBinder` para convertê-la em uma matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="583a6-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="583a6-208">Os <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> argumentos de mapas `byte[]` para `ByteArrayModelBinder` :</span><span class="sxs-lookup"><span data-stu-id="583a6-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="583a6-209">Ao criar seu próprio associador de modelo personalizado, você pode implementar seu próprio `IModelBinderProvider` tipo ou usar o <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .</span><span class="sxs-lookup"><span data-stu-id="583a6-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="583a6-210">O seguinte exemplo mostra como usar `ByteArrayModelBinder` para converter uma cadeia de caracteres codificada em Base64 em um `byte[]` e salvar o resultado em um arquivo:</span><span class="sxs-lookup"><span data-stu-id="583a6-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="583a6-211">Execute POST em uma cadeia de caracteres codificada em Base64 para esse método de API usando uma ferramenta como o [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="583a6-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="583a6-212">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="583a6-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="583a6-213">Desde que o associador possa associar dados de solicitação a propriedades ou argumentos nomeados de forma adequada, o model binding terá êxito.</span><span class="sxs-lookup"><span data-stu-id="583a6-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="583a6-214">O seguinte exemplo mostra como usar `ByteArrayModelBinder` com um modelo de exibição:</span><span class="sxs-lookup"><span data-stu-id="583a6-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="583a6-215">Amostra de associador de modelos personalizado</span><span class="sxs-lookup"><span data-stu-id="583a6-215">Custom model binder sample</span></span>

<span data-ttu-id="583a6-216">Nesta seção, implementaremos um associador de modelos personalizado que:</span><span class="sxs-lookup"><span data-stu-id="583a6-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="583a6-217">Converte dados de solicitação de entrada em argumentos de chave fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="583a6-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="583a6-218">Usa o Entity Framework Core para buscar a entidade associada.</span><span class="sxs-lookup"><span data-stu-id="583a6-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="583a6-219">Passa a entidade associada como um argumento para o método de ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="583a6-220">A seguinte amostra usa o atributo `ModelBinder` no modelo `Author`:</span><span class="sxs-lookup"><span data-stu-id="583a6-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="583a6-221">No código anterior, o atributo `ModelBinder` especifica o tipo de `IModelBinder` que deve ser usado para associar parâmetros de ação `Author`.</span><span class="sxs-lookup"><span data-stu-id="583a6-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="583a6-222">A classe `AuthorEntityBinder` a seguir associa um parâmetro `Author` efetuando fetch da entidade de uma fonte de dados usando o Entity Framework Core e um `authorId`:</span><span class="sxs-lookup"><span data-stu-id="583a6-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="583a6-223">A classe `AuthorEntityBinder` precedente é destinada a ilustrar um associador de modelos personalizado.</span><span class="sxs-lookup"><span data-stu-id="583a6-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="583a6-224">A classe não é destinada a ilustrar as melhores práticas para um cenário de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="583a6-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="583a6-225">Para pesquisa, associe o `authorId` e consulte o banco de dados em um método de ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="583a6-226">Essa abordagem separa falhas de model binding de casos de `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="583a6-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="583a6-227">O seguinte código mostra como usar o `AuthorEntityBinder` em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="583a6-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="583a6-228">O atributo `ModelBinder` pode ser usado para aplicar o `AuthorEntityBinder` aos parâmetros que não usam convenções padrão:</span><span class="sxs-lookup"><span data-stu-id="583a6-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="583a6-229">Neste exemplo, como o nome do argumento não é o `authorId` padrão, ele é especificado no parâmetro com o atributo `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="583a6-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="583a6-230">O controlador e o método de ação são simplificados em comparação com a pesquisa da entidade no método de ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="583a6-231">A lógica para buscar o autor usando o Entity Framework Core é movida para o associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="583a6-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="583a6-232">Isso pode ser uma simplificação considerável quando há vários métodos associados ao modelo `Author`.</span><span class="sxs-lookup"><span data-stu-id="583a6-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="583a6-233">Aplique o atributo `ModelBinder` a propriedades de modelo individuais (como em um viewmodel) ou a parâmetros de método de ação para especificar um associador de modelos ou nome de modelo específico para apenas esse tipo ou essa ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="583a6-234">Implementando um ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="583a6-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="583a6-235">Em vez de aplicar um atributo, você pode implementar `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="583a6-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="583a6-236">É assim que os associadores de estrutura interna são implementados.</span><span class="sxs-lookup"><span data-stu-id="583a6-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="583a6-237">Quando você especifica o tipo no qual o associador opera, você especifica o tipo de argumento que ele produz, **não** a entrada aceita pelo associador.</span><span class="sxs-lookup"><span data-stu-id="583a6-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="583a6-238">O provedor de associador a seguir funciona com o `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="583a6-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="583a6-239">Quando ele for adicionado à coleção do MVC de provedores, não será necessário usar o atributo `ModelBinder` nos parâmetros `Author` ou de tipo `Author`.</span><span class="sxs-lookup"><span data-stu-id="583a6-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="583a6-240">Observação: o código anterior retorna um `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="583a6-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="583a6-241">O `BinderTypeModelBinder` atua como um alocador para associadores de modelos e fornece a DI (injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="583a6-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="583a6-242">O `AuthorEntityBinder` exige que a DI acesse o EF Core.</span><span class="sxs-lookup"><span data-stu-id="583a6-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="583a6-243">Use `BinderTypeModelBinder` se o associador de modelos exigir serviços da DI.</span><span class="sxs-lookup"><span data-stu-id="583a6-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="583a6-244">Para usar um provedor de associador de modelos personalizado, adicione-o a `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="583a6-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="583a6-245">Ao avaliar associadores de modelos, a coleção de provedores é examinada na ordem.</span><span class="sxs-lookup"><span data-stu-id="583a6-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="583a6-246">O primeiro provedor que retorna um associador é usado.</span><span class="sxs-lookup"><span data-stu-id="583a6-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="583a6-247">A adição do provedor ao final da coleção pode resultar na chamada a um associador de modelos interno antes que o associador personalizado tenha uma oportunidade.</span><span class="sxs-lookup"><span data-stu-id="583a6-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="583a6-248">Neste exemplo, o provedor personalizado é adicionado ao início da coleção para garantir que ele é usado para argumentos de ação `Author`.</span><span class="sxs-lookup"><span data-stu-id="583a6-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="583a6-249">Associação de modelo polimórfico</span><span class="sxs-lookup"><span data-stu-id="583a6-249">Polymorphic model binding</span></span>

<span data-ttu-id="583a6-250">A associação a diferentes modelos de tipos derivados é conhecida como associação de modelo polimórfico.</span><span class="sxs-lookup"><span data-stu-id="583a6-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="583a6-251">A associação de modelo personalizado polimórfico é necessária quando o valor da solicitação deve ser associado ao tipo de modelo derivado específico.</span><span class="sxs-lookup"><span data-stu-id="583a6-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="583a6-252">Associação de modelo polimórfico:</span><span class="sxs-lookup"><span data-stu-id="583a6-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="583a6-253">Não é comum para uma API REST projetada para interoperar com todos os idiomas.</span><span class="sxs-lookup"><span data-stu-id="583a6-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="583a6-254">Dificulta a razão dos modelos associados.</span><span class="sxs-lookup"><span data-stu-id="583a6-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="583a6-255">No entanto, se um aplicativo exigir Associação de modelo polimórfico, uma implementação poderá ser semelhante ao seguinte código:</span><span class="sxs-lookup"><span data-stu-id="583a6-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="583a6-256">Recomendações e melhores práticas</span><span class="sxs-lookup"><span data-stu-id="583a6-256">Recommendations and best practices</span></span>

<span data-ttu-id="583a6-257">Associadores de modelos personalizados:</span><span class="sxs-lookup"><span data-stu-id="583a6-257">Custom model binders:</span></span>

- <span data-ttu-id="583a6-258">Não devem tentar definir códigos de status ou retornar resultados (por exemplo, 404 Não Encontrado).</span><span class="sxs-lookup"><span data-stu-id="583a6-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="583a6-259">Se o model binding falhar, um [filtro de ação](xref:mvc/controllers/filters) ou uma lógica no próprio método de ação deverá resolver a falha.</span><span class="sxs-lookup"><span data-stu-id="583a6-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="583a6-260">São muito úteis para eliminar código repetitivo e interesses paralelos de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="583a6-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="583a6-261">Normalmente, não devem ser usados para converter uma cadeia de caracteres em um tipo personalizado; um <xref:System.ComponentModel.TypeConverter> geralmente é uma opção melhor.</span><span class="sxs-lookup"><span data-stu-id="583a6-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
