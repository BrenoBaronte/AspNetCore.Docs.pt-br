---
title: Model binding personalizado no ASP.NET Core
author: ardalis
description: Saiba como o model binding permite que as ações do controlador trabalhem diretamente com os tipos de modelo no ASP.NET Core.
ms.author: riande
ms.date: 04/10/2017
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: b8745241b0699d270bb8f3a56ab614b0ca49e64b
ms.sourcegitcommit: 317f9be24db600499e79d25872d743af74bd86c0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48045530"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="16079-103">Model binding personalizado no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16079-103">Custom Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="16079-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="16079-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="16079-105">O model binding permite que as ações do controlador funcionem diretamente com tipos de modelo (passados como argumentos de método), em vez de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="16079-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="16079-106">O mapeamento entre os dados de solicitação de entrada e os modelos de aplicativo é manipulado por associadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="16079-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="16079-107">Os desenvolvedores podem estender a funcionalidade de model binding interna implementando associadores de modelos personalizados (embora, normalmente, você não precise escrever seu próprio provedor).</span><span class="sxs-lookup"><span data-stu-id="16079-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

[<span data-ttu-id="16079-108">Exibir ou baixar a amostra do GitHub</span><span class="sxs-lookup"><span data-stu-id="16079-108">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a><span data-ttu-id="16079-109">Limitações dos associadores de modelos padrão</span><span class="sxs-lookup"><span data-stu-id="16079-109">Default model binder limitations</span></span>

<span data-ttu-id="16079-110">Os associadores de modelos padrão dão suporte à maioria dos tipos de dados comuns do .NET Core e devem atender à maior parte das necessidades dos desenvolvedores.</span><span class="sxs-lookup"><span data-stu-id="16079-110">The default model binders support most of the common .NET Core data types and should meet most developers\` needs.</span></span> <span data-ttu-id="16079-111">Eles esperam associar a entrada baseada em texto da solicitação diretamente a tipos de modelo.</span><span class="sxs-lookup"><span data-stu-id="16079-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="16079-112">Talvez seja necessário transformar a entrada antes de associá-la.</span><span class="sxs-lookup"><span data-stu-id="16079-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="16079-113">Por exemplo, quando você tem uma chave que pode ser usada para pesquisar dados de modelo.</span><span class="sxs-lookup"><span data-stu-id="16079-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="16079-114">Use um associador de modelos personalizado para buscar dados com base na chave.</span><span class="sxs-lookup"><span data-stu-id="16079-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="16079-115">Análise do model binding</span><span class="sxs-lookup"><span data-stu-id="16079-115">Model binding review</span></span>

<span data-ttu-id="16079-116">O model binding usa definições específicas para os tipos nos quais opera.</span><span class="sxs-lookup"><span data-stu-id="16079-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="16079-117">Um *tipo simples* é convertido de uma única cadeia de caracteres na entrada.</span><span class="sxs-lookup"><span data-stu-id="16079-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="16079-118">Um *tipo complexo* é convertido de vários valores de entrada.</span><span class="sxs-lookup"><span data-stu-id="16079-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="16079-119">A estrutura determina a diferença de acordo com a existência de um `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="16079-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="16079-120">Recomendamos que você crie um conversor de tipo se tiver um mapeamento `string` -> `SomeType` simples que não exige recursos externos.</span><span class="sxs-lookup"><span data-stu-id="16079-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="16079-121">Antes de criar seu próprio associador de modelos personalizado, vale a pena analisar como os associadores de modelos existentes são implementados.</span><span class="sxs-lookup"><span data-stu-id="16079-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="16079-122">Considere o [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder), que pode ser usado para converter cadeias de caracteres codificadas em Base64 em matrizes de bytes.</span><span class="sxs-lookup"><span data-stu-id="16079-122">Consider the [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="16079-123">As matrizes de bytes costumam ser armazenadas como arquivos ou campos BLOB do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="16079-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="16079-124">Trabalhando com o ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="16079-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="16079-125">Cadeias de caracteres codificadas em Base64 podem ser usadas para representar dados binários.</span><span class="sxs-lookup"><span data-stu-id="16079-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="16079-126">Por exemplo, a imagem a seguir pode ser codificada como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="16079-126">For example, the following image can be encoded as a string.</span></span>

<span data-ttu-id="16079-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="16079-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span></span>

<span data-ttu-id="16079-128">Uma pequena parte da cadeia de caracteres codificada é mostrada na seguinte imagem:</span><span class="sxs-lookup"><span data-stu-id="16079-128">A small portion of the encoded string is shown in the following image:</span></span>

<span data-ttu-id="16079-129">![dotnet bot codificado](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span><span class="sxs-lookup"><span data-stu-id="16079-129">![dotnet bot encoded](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span></span>

<span data-ttu-id="16079-130">Siga as instruções do [LEIAME da amostra](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) para converter a cadeia de caracteres codificada em Base64 em um arquivo.</span><span class="sxs-lookup"><span data-stu-id="16079-130">Follow the instructions in the [sample's README](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) to convert the base64-encoded string into a file.</span></span>

<span data-ttu-id="16079-131">O ASP.NET Core MVC pode usar uma cadeia de caracteres codificada em Base64 e usar um `ByteArrayModelBinder` para convertê-la em uma matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="16079-131">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="16079-132">O [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) que implementa [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) mapeia argumentos `byte[]` para `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="16079-132">The [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) which implements [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="16079-133">Ao criar seu próprio associador de modelos personalizado, você pode implementar seu próprio tipo `IModelBinderProvider` ou usar o [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span><span class="sxs-lookup"><span data-stu-id="16079-133">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span></span>

<span data-ttu-id="16079-134">O seguinte exemplo mostra como usar `ByteArrayModelBinder` para converter uma cadeia de caracteres codificada em Base64 em um `byte[]` e salvar o resultado em um arquivo:</span><span class="sxs-lookup"><span data-stu-id="16079-134">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

<span data-ttu-id="16079-135">Execute POST em uma cadeia de caracteres codificada em Base64 para esse método de API usando uma ferramenta como o [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="16079-135">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="16079-136">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="16079-136">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="16079-137">Desde que o associador possa associar dados de solicitação a propriedades ou argumentos nomeados de forma adequada, o model binding terá êxito.</span><span class="sxs-lookup"><span data-stu-id="16079-137">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="16079-138">O seguinte exemplo mostra como usar `ByteArrayModelBinder` com um modelo de exibição:</span><span class="sxs-lookup"><span data-stu-id="16079-138">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="16079-139">Amostra de associador de modelos personalizado</span><span class="sxs-lookup"><span data-stu-id="16079-139">Custom model binder sample</span></span>

<span data-ttu-id="16079-140">Nesta seção, implementaremos um associador de modelos personalizado que:</span><span class="sxs-lookup"><span data-stu-id="16079-140">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="16079-141">Converte dados de solicitação de entrada em argumentos de chave fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="16079-141">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="16079-142">Usa o Entity Framework Core para buscar a entidade associada.</span><span class="sxs-lookup"><span data-stu-id="16079-142">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="16079-143">Passa a entidade associada como um argumento para o método de ação.</span><span class="sxs-lookup"><span data-stu-id="16079-143">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="16079-144">A seguinte amostra usa o atributo `ModelBinder` no modelo `Author`:</span><span class="sxs-lookup"><span data-stu-id="16079-144">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

<span data-ttu-id="16079-145">No código anterior, o atributo `ModelBinder` especifica o tipo de `IModelBinder` que deve ser usado para associar parâmetros de ação `Author`.</span><span class="sxs-lookup"><span data-stu-id="16079-145">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span> 

<span data-ttu-id="16079-146">O `AuthorEntityBinder` é usado para associar um parâmetro `Author` buscando a entidade de uma fonte de dados usando o Entity Framework Core e uma `authorId`:</span><span class="sxs-lookup"><span data-stu-id="16079-146">The `AuthorEntityBinder` is used to bind an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

<span data-ttu-id="16079-147">O seguinte código mostra como usar o `AuthorEntityBinder` em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="16079-147">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="16079-148">O atributo `ModelBinder` pode ser usado para aplicar o `AuthorEntityBinder` aos parâmetros que não usam convenções padrão:</span><span class="sxs-lookup"><span data-stu-id="16079-148">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="16079-149">Neste exemplo, como o nome do argumento não é o `authorId` padrão, ele é especificado no parâmetro com o atributo `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="16079-149">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="16079-150">Observe que o controlador e o método de ação são simplificados, comparado à pesquisa da entidade no método de ação.</span><span class="sxs-lookup"><span data-stu-id="16079-150">Note that both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="16079-151">A lógica para buscar o autor usando o Entity Framework Core é movida para o associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="16079-151">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="16079-152">Isso pode ser uma simplificação considerável quando há vários métodos associados ao modelo `Author` e pode ajudá-lo a seguir o [princípio DRY](http://deviq.com/don-t-repeat-yourself/).</span><span class="sxs-lookup"><span data-stu-id="16079-152">This can be a considerable simplification when you have several methods that bind to the `Author` model, and can help you to follow the [DRY principle](http://deviq.com/don-t-repeat-yourself/).</span></span>

<span data-ttu-id="16079-153">Aplique o atributo `ModelBinder` a propriedades de modelo individuais (como em um viewmodel) ou a parâmetros de método de ação para especificar um associador de modelos ou nome de modelo específico para apenas esse tipo ou essa ação.</span><span class="sxs-lookup"><span data-stu-id="16079-153">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="16079-154">Implementando um ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="16079-154">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="16079-155">Em vez de aplicar um atributo, você pode implementar `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="16079-155">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="16079-156">É assim que os associadores de estrutura interna são implementados.</span><span class="sxs-lookup"><span data-stu-id="16079-156">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="16079-157">Quando você especifica o tipo no qual o associador opera, você especifica o tipo de argumento que ele produz, **não** a entrada aceita pelo associador.</span><span class="sxs-lookup"><span data-stu-id="16079-157">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="16079-158">O provedor de associador a seguir funciona com o `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="16079-158">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="16079-159">Quando ele é adicionado à coleção do MVC de provedores, você não precisa usar o atributo `ModelBinder` em `Author` ou parâmetros tipados `Author`.</span><span class="sxs-lookup"><span data-stu-id="16079-159">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author` typed parameters.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="16079-160">Observação: o código anterior retorna um `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="16079-160">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="16079-161">O `BinderTypeModelBinder` atua como um alocador para associadores de modelos e fornece a DI (injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="16079-161">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="16079-162">O `AuthorEntityBinder` exige que a DI acesse o EF Core.</span><span class="sxs-lookup"><span data-stu-id="16079-162">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="16079-163">Use `BinderTypeModelBinder` se o associador de modelos exigir serviços da DI.</span><span class="sxs-lookup"><span data-stu-id="16079-163">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="16079-164">Para usar um provedor de associador de modelos personalizado, adicione-o a `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="16079-164">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

<span data-ttu-id="16079-165">Ao avaliar associadores de modelos, a coleção de provedores é examinada na ordem.</span><span class="sxs-lookup"><span data-stu-id="16079-165">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="16079-166">O primeiro provedor que retorna um associador é usado.</span><span class="sxs-lookup"><span data-stu-id="16079-166">The first provider that returns a binder is used.</span></span>

<span data-ttu-id="16079-167">A imagem a seguir mostra os associadores de modelos padrão do depurador.</span><span class="sxs-lookup"><span data-stu-id="16079-167">The following image shows the default model binders from the debugger.</span></span>

<span data-ttu-id="16079-168">![associadores de modelo padrão](custom-model-binding/images/default-model-binders.png "default model binders")</span><span class="sxs-lookup"><span data-stu-id="16079-168">![default model binders](custom-model-binding/images/default-model-binders.png "default model binders")</span></span>

<span data-ttu-id="16079-169">A adição do provedor ao final da coleção pode resultar na chamada a um associador de modelos interno antes que o associador personalizado tenha uma oportunidade.</span><span class="sxs-lookup"><span data-stu-id="16079-169">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="16079-170">Neste exemplo, o provedor personalizado é adicionado ao início da coleção para garantir que ele é usado para argumentos de ação `Author`.</span><span class="sxs-lookup"><span data-stu-id="16079-170">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="16079-171">Recomendações e melhores práticas</span><span class="sxs-lookup"><span data-stu-id="16079-171">Recommendations and best practices</span></span>

<span data-ttu-id="16079-172">Associadores de modelos personalizados:</span><span class="sxs-lookup"><span data-stu-id="16079-172">Custom model binders:</span></span>
- <span data-ttu-id="16079-173">Não devem tentar definir códigos de status ou retornar resultados (por exemplo, 404 Não Encontrado).</span><span class="sxs-lookup"><span data-stu-id="16079-173">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="16079-174">Se o model binding falhar, um [filtro de ação](xref:mvc/controllers/filters) ou uma lógica no próprio método de ação deverá resolver a falha.</span><span class="sxs-lookup"><span data-stu-id="16079-174">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="16079-175">São muito úteis para eliminar código repetitivo e interesses paralelos de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="16079-175">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="16079-176">Normalmente, não devem ser usados para converter uma cadeia de caracteres em um tipo personalizado; um [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) geralmente é uma opção melhor.</span><span class="sxs-lookup"><span data-stu-id="16079-176">Typically shouldn't be used to convert a string into a custom type, a [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) is usually a better option.</span></span>
