---
title: JsonPatch na API Web do ASP.NET Core
author: rick-anderson
description: Saiba como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: da507974b88c21de22e2c7a56950943207565138
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060541"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="42ce9-103">JsonPatch na API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42ce9-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="42ce9-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="42ce9-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42ce9-105">Este artigo explica como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="42ce9-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="42ce9-106">Instalação do pacote</span><span class="sxs-lookup"><span data-stu-id="42ce9-106">Package installation</span></span>

<span data-ttu-id="42ce9-107">Para habilitar o suporte a patch JSON em seu aplicativo, conclua as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="42ce9-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="42ce9-108">Instale o [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="42ce9-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="42ce9-109">Atualize o método do projeto `Startup.ConfigureServices` para chamar <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="42ce9-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="42ce9-110">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="42ce9-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="42ce9-111">`AddNewtonsoftJson` é compatível com os métodos de registro do serviço MVC:</span><span class="sxs-lookup"><span data-stu-id="42ce9-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.Add:::no-loc(Razor):::Pages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="42ce9-112">Patch JSON, AddNewtonsoftJson e System.Text.Jsem</span><span class="sxs-lookup"><span data-stu-id="42ce9-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="42ce9-113">`AddNewtonsoftJson` substitui os `System.Text.Json` formatadores de entrada e saída baseados em dados usados para formatar **todo** o conteúdo JSON.</span><span class="sxs-lookup"><span data-stu-id="42ce9-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="42ce9-114">Para adicionar suporte ao patch JSON usando `Newtonsoft.Json` o, deixando os outros formatadores inalterados, atualize o método do projeto da `Startup.ConfigureServices` seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="42ce9-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="42ce9-115">O código anterior requer o `Microsoft.AspNetCore.Mvc.NewtonsoftJson` pacote e as seguintes `using` instruções:</span><span class="sxs-lookup"><span data-stu-id="42ce9-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="42ce9-116">Método de solicitação HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="42ce9-116">PATCH HTTP request method</span></span>

<span data-ttu-id="42ce9-117">Os métodos PUT e [PATCH](https://tools.ietf.org/html/rfc5789) são usados para atualizar um recurso existente.</span><span class="sxs-lookup"><span data-stu-id="42ce9-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="42ce9-118">A diferença entre eles é que PUT substitui o recurso inteiro, enquanto PATCH especifica apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="42ce9-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="42ce9-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="42ce9-119">JSON Patch</span></span>

<span data-ttu-id="42ce9-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) é um formato para especificar as atualizações a serem aplicadas a um recurso.</span><span class="sxs-lookup"><span data-stu-id="42ce9-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="42ce9-121">Um documento de JSON Patch tem uma matriz de *operações* .</span><span class="sxs-lookup"><span data-stu-id="42ce9-121">A JSON Patch document has an array of *operations* .</span></span> <span data-ttu-id="42ce9-122">Cada operação identifica um tipo específico de alteração.</span><span class="sxs-lookup"><span data-stu-id="42ce9-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="42ce9-123">Exemplos de tais alterações incluem adicionar um elemento de matriz ou substituir um valor de propriedade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="42ce9-124">Por exemplo, os documentos JSON a seguir representam um recurso, um documento de patch JSON para o recurso e o resultado da aplicação das operações de patch.</span><span class="sxs-lookup"><span data-stu-id="42ce9-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="42ce9-125">Exemplo de recurso</span><span class="sxs-lookup"><span data-stu-id="42ce9-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="42ce9-126">Exemplo de JSON Patch</span><span class="sxs-lookup"><span data-stu-id="42ce9-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="42ce9-127">No JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="42ce9-127">In the preceding JSON:</span></span>

* <span data-ttu-id="42ce9-128">A propriedade `op` indica o tipo de operação.</span><span class="sxs-lookup"><span data-stu-id="42ce9-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="42ce9-129">A propriedade `path` indica o elemento a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="42ce9-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="42ce9-130">A propriedade `value` fornece o novo valor.</span><span class="sxs-lookup"><span data-stu-id="42ce9-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="42ce9-131">Recurso depois do patch</span><span class="sxs-lookup"><span data-stu-id="42ce9-131">Resource after patch</span></span>

<span data-ttu-id="42ce9-132">Este é o recurso após a aplicação do documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="42ce9-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="42ce9-133">As alterações feitas pela aplicação de um documento de patch JSON a um recurso são atômicas.</span><span class="sxs-lookup"><span data-stu-id="42ce9-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="42ce9-134">Se qualquer operação na lista falhar, nenhuma operação na lista será aplicada.</span><span class="sxs-lookup"><span data-stu-id="42ce9-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="42ce9-135">Sintaxe de path</span><span class="sxs-lookup"><span data-stu-id="42ce9-135">Path syntax</span></span>

<span data-ttu-id="42ce9-136">A propriedade [path](https://tools.ietf.org/html/rfc6901) de um objeto de operação tem barras entre os níveis.</span><span class="sxs-lookup"><span data-stu-id="42ce9-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="42ce9-137">Por exemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="42ce9-138">Índices baseados em zero são usados para especificar os elementos da matriz.</span><span class="sxs-lookup"><span data-stu-id="42ce9-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="42ce9-139">O primeiro elemento da matriz `addresses` estaria em `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="42ce9-140">Para `add` o final de uma matriz, use um hífen ( `-` ) em vez de um número de índice: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="42ce9-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="42ce9-141">Operations</span><span class="sxs-lookup"><span data-stu-id="42ce9-141">Operations</span></span>

<span data-ttu-id="42ce9-142">A tabela a seguir mostra operações compatíveis conforme definido na [especificação de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="42ce9-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="42ce9-143">Operação</span><span class="sxs-lookup"><span data-stu-id="42ce9-143">Operation</span></span>  | <span data-ttu-id="42ce9-144">Observações</span><span class="sxs-lookup"><span data-stu-id="42ce9-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="42ce9-145">Adicione uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="42ce9-145">Add a property or array element.</span></span> <span data-ttu-id="42ce9-146">Para a propriedade existente: defina o valor.</span><span class="sxs-lookup"><span data-stu-id="42ce9-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="42ce9-147">Remova uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="42ce9-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="42ce9-148">É o mesmo que `remove`, seguido por `add` no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="42ce9-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="42ce9-149">É o mesmo que `remove` da origem, seguido por `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="42ce9-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="42ce9-150">É o mesmo que `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="42ce9-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="42ce9-151">Retorna o código de status de êxito se o valor em `path` é igual ao `value` fornecido.</span><span class="sxs-lookup"><span data-stu-id="42ce9-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="42ce9-152">Patch JSON no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42ce9-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="42ce9-153">A implementação do ASP.NET Core de JSON Patch é fornecida no pacote do NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="42ce9-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="42ce9-154">Código do método de ação</span><span class="sxs-lookup"><span data-stu-id="42ce9-154">Action method code</span></span>

<span data-ttu-id="42ce9-155">Em um controlador de API, um método de ação para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="42ce9-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="42ce9-156">É anotado com o atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="42ce9-157">Aceita um `JsonPatchDocument<T>` , normalmente com `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="42ce9-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="42ce9-158">Chama `ApplyTo` no documento de patch para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="42ce9-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="42ce9-159">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="42ce9-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="42ce9-160">Este código do aplicativo de exemplo funciona com o seguinte `Customer` modelo:</span><span class="sxs-lookup"><span data-stu-id="42ce9-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="42ce9-161">O exemplo de método de ação:</span><span class="sxs-lookup"><span data-stu-id="42ce9-161">The sample action method:</span></span>

* <span data-ttu-id="42ce9-162">Constrói um `Customer`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="42ce9-163">Aplica o patch.</span><span class="sxs-lookup"><span data-stu-id="42ce9-163">Applies the patch.</span></span>
* <span data-ttu-id="42ce9-164">Retorna o resultado no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="42ce9-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="42ce9-165">Em um aplicativo real, o código recuperaria os dados de um repositório, como um banco de dados, e atualizaria o banco de dados após a aplicação do patch.</span><span class="sxs-lookup"><span data-stu-id="42ce9-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="42ce9-166">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="42ce9-166">Model state</span></span>

<span data-ttu-id="42ce9-167">O exemplo de método de ação anterior chama uma sobrecarga de `ApplyTo` que utiliza o estado do modelo como um de seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="42ce9-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="42ce9-168">Com essa opção, você pode receber mensagens de erro nas respostas.</span><span class="sxs-lookup"><span data-stu-id="42ce9-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="42ce9-169">O exemplo a seguir mostra o corpo de uma resposta 400 Solicitação Incorreta para uma operação `test`:</span><span class="sxs-lookup"><span data-stu-id="42ce9-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="42ce9-170">Objetos dinâmicos</span><span class="sxs-lookup"><span data-stu-id="42ce9-170">Dynamic objects</span></span>

<span data-ttu-id="42ce9-171">O exemplo do método de ação a seguir mostra como aplicar um patch a um objeto dinâmico:</span><span class="sxs-lookup"><span data-stu-id="42ce9-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="42ce9-172">A operação add</span><span class="sxs-lookup"><span data-stu-id="42ce9-172">The add operation</span></span>

* <span data-ttu-id="42ce9-173">Se `path` aponta para um elemento de matriz: insere um novo elemento antes do especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="42ce9-174">Se `path` aponta para uma propriedade: define o valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="42ce9-175">Se `path` aponta para um local não existente:</span><span class="sxs-lookup"><span data-stu-id="42ce9-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="42ce9-176">Se o recurso no qual fazer patch é um objeto dinâmico: adiciona uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="42ce9-177">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="42ce9-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="42ce9-178">O exemplo de documento de patch a seguir define o valor de `CustomerName` e adiciona um objeto `Order` ao final da matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="42ce9-179">A operação remove</span><span class="sxs-lookup"><span data-stu-id="42ce9-179">The remove operation</span></span>

* <span data-ttu-id="42ce9-180">Se `path` aponta para um elemento de matriz: remove o elemento.</span><span class="sxs-lookup"><span data-stu-id="42ce9-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="42ce9-181">Se `path` aponta para uma propriedade:</span><span class="sxs-lookup"><span data-stu-id="42ce9-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="42ce9-182">Se o recurso no qual fazer patch é um objeto dinâmico: remove a propriedade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="42ce9-183">Se o recurso no qual fazer patch é um objeto estático:</span><span class="sxs-lookup"><span data-stu-id="42ce9-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="42ce9-184">Se a propriedade é anulável: define como nulo.</span><span class="sxs-lookup"><span data-stu-id="42ce9-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="42ce9-185">Se a propriedade não é anulável: define como `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="42ce9-186">O documento de exemplo de patch a seguir define `CustomerName` como nulo e exclui `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="42ce9-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="42ce9-187">A operação replace</span><span class="sxs-lookup"><span data-stu-id="42ce9-187">The replace operation</span></span>

<span data-ttu-id="42ce9-188">Esta operação é funcionalmente a mesma que `remove` seguida por `add`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="42ce9-189">O documento de patch de exemplo a seguir define o valor de `CustomerName` e substitui `Orders[0]` por um novo `Order` objeto:</span><span class="sxs-lookup"><span data-stu-id="42ce9-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="42ce9-190">A operação move</span><span class="sxs-lookup"><span data-stu-id="42ce9-190">The move operation</span></span>

* <span data-ttu-id="42ce9-191">Se `path` aponta para um elemento de matriz: copia o elemento `from` para o local do elemento `path` e, em seguida, executa uma operação `remove` no elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="42ce9-192">Se `path` aponta para uma propriedade: copia o valor da propriedade `from` para a propriedade `path`, depois executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="42ce9-193">Se `path` aponta para uma propriedade não existente:</span><span class="sxs-lookup"><span data-stu-id="42ce9-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="42ce9-194">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="42ce9-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="42ce9-195">Se o recurso no qual fazer patch é um objeto dinâmico: copia a propriedade `from` para o local indicado por `path` e, em seguida, executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="42ce9-196">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="42ce9-196">The following sample patch document:</span></span>

* <span data-ttu-id="42ce9-197">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="42ce9-198">Define `Orders[0].OrderName` como nulo.</span><span class="sxs-lookup"><span data-stu-id="42ce9-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="42ce9-199">Move `Orders[1]` para antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="42ce9-200">A operação copy</span><span class="sxs-lookup"><span data-stu-id="42ce9-200">The copy operation</span></span>

<span data-ttu-id="42ce9-201">Esta operação é funcionalmente a mesma que uma operação `move`, sem a etapa final `remove`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="42ce9-202">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="42ce9-202">The following sample patch document:</span></span>

* <span data-ttu-id="42ce9-203">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="42ce9-204">Insere uma cópia de `Orders[1]` antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="42ce9-205">A operação test</span><span class="sxs-lookup"><span data-stu-id="42ce9-205">The test operation</span></span>

<span data-ttu-id="42ce9-206">Se o valor no local indicado por `path` for diferente do valor fornecido em `value`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="42ce9-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="42ce9-207">Nesse caso, toda a solicitação de PATCH falhará, mesmo se todas as outras operações no documento de patch forem bem-sucedidas.</span><span class="sxs-lookup"><span data-stu-id="42ce9-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="42ce9-208">A operação `test` normalmente é usada para impedir uma atualização quando há um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="42ce9-209">O seguinte exemplo de documento de patch não terá nenhum efeito se o valor inicial de `CustomerName` for "John", porque o teste falha:</span><span class="sxs-lookup"><span data-stu-id="42ce9-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="42ce9-210">Obter o código</span><span class="sxs-lookup"><span data-stu-id="42ce9-210">Get the code</span></span>

<span data-ttu-id="42ce9-211">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="42ce9-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="42ce9-212">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="42ce9-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="42ce9-213">Para testar o exemplo, execute o aplicativo e envie solicitações HTTP com as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="42ce9-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="42ce9-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="42ce9-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="42ce9-215">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="42ce9-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="42ce9-216">Cabeçalho: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="42ce9-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="42ce9-217">Corpo: Copie e cole um dos exemplos de documento de patch JSON da pasta de projeto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="42ce9-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42ce9-218">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="42ce9-218">Additional resources</span></span>

* [<span data-ttu-id="42ce9-219">Especificação do método PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="42ce9-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="42ce9-220">Especificação do JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="42ce9-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="42ce9-221">Especificação de formato de caminho do JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="42ce9-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="42ce9-222">[Documentação do JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="42ce9-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="42ce9-223">Inclui links para recursos a fim de criar documentos de JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="42ce9-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="42ce9-224">Código-fonte de JSON Patch do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42ce9-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42ce9-225">Este artigo explica como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="42ce9-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="42ce9-226">Método de solicitação HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="42ce9-226">PATCH HTTP request method</span></span>

<span data-ttu-id="42ce9-227">Os métodos PUT e [PATCH](https://tools.ietf.org/html/rfc5789) são usados para atualizar um recurso existente.</span><span class="sxs-lookup"><span data-stu-id="42ce9-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="42ce9-228">A diferença entre eles é que PUT substitui o recurso inteiro, enquanto PATCH especifica apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="42ce9-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="42ce9-229">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="42ce9-229">JSON Patch</span></span>

<span data-ttu-id="42ce9-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) é um formato para especificar as atualizações a serem aplicadas a um recurso.</span><span class="sxs-lookup"><span data-stu-id="42ce9-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="42ce9-231">Um documento de JSON Patch tem uma matriz de *operações* .</span><span class="sxs-lookup"><span data-stu-id="42ce9-231">A JSON Patch document has an array of *operations* .</span></span> <span data-ttu-id="42ce9-232">Cada operação identifica um tipo específico de alteração, como adicionar um elemento de matriz ou substituir um valor de propriedade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="42ce9-233">Por exemplo, os documentos JSON a seguir representam um recurso, um documento de JSON Patch para o recurso e o resultado da aplicação de operações patch.</span><span class="sxs-lookup"><span data-stu-id="42ce9-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="42ce9-234">Exemplo de recurso</span><span class="sxs-lookup"><span data-stu-id="42ce9-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="42ce9-235">Exemplo de JSON Patch</span><span class="sxs-lookup"><span data-stu-id="42ce9-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="42ce9-236">No JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="42ce9-236">In the preceding JSON:</span></span>

* <span data-ttu-id="42ce9-237">A propriedade `op` indica o tipo de operação.</span><span class="sxs-lookup"><span data-stu-id="42ce9-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="42ce9-238">A propriedade `path` indica o elemento a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="42ce9-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="42ce9-239">A propriedade `value` fornece o novo valor.</span><span class="sxs-lookup"><span data-stu-id="42ce9-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="42ce9-240">Recurso depois do patch</span><span class="sxs-lookup"><span data-stu-id="42ce9-240">Resource after patch</span></span>

<span data-ttu-id="42ce9-241">Este é o recurso após a aplicação do documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="42ce9-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="42ce9-242">As alterações feitas ao aplicar um documento JSON Patch a um recurso são atômicas: se alguma operação da lista falhar, nenhuma operação da lista será aplicada.</span><span class="sxs-lookup"><span data-stu-id="42ce9-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="42ce9-243">Sintaxe de path</span><span class="sxs-lookup"><span data-stu-id="42ce9-243">Path syntax</span></span>

<span data-ttu-id="42ce9-244">A propriedade [path](https://tools.ietf.org/html/rfc6901) de um objeto de operação tem barras entre os níveis.</span><span class="sxs-lookup"><span data-stu-id="42ce9-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="42ce9-245">Por exemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="42ce9-246">Índices baseados em zero são usados para especificar os elementos da matriz.</span><span class="sxs-lookup"><span data-stu-id="42ce9-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="42ce9-247">O primeiro elemento da matriz `addresses` estaria em `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="42ce9-248">Para `add` ao final de uma matriz, use um hífen (-) ao invés de um número de índice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="42ce9-249">Operations</span><span class="sxs-lookup"><span data-stu-id="42ce9-249">Operations</span></span>

<span data-ttu-id="42ce9-250">A tabela a seguir mostra operações compatíveis conforme definido na [especificação de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="42ce9-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="42ce9-251">Operação</span><span class="sxs-lookup"><span data-stu-id="42ce9-251">Operation</span></span>  | <span data-ttu-id="42ce9-252">Observações</span><span class="sxs-lookup"><span data-stu-id="42ce9-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="42ce9-253">Adicione uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="42ce9-253">Add a property or array element.</span></span> <span data-ttu-id="42ce9-254">Para a propriedade existente: defina o valor.</span><span class="sxs-lookup"><span data-stu-id="42ce9-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="42ce9-255">Remova uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="42ce9-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="42ce9-256">É o mesmo que `remove`, seguido por `add` no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="42ce9-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="42ce9-257">É o mesmo que `remove` da origem, seguido por `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="42ce9-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="42ce9-258">É o mesmo que `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="42ce9-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="42ce9-259">Retorna o código de status de êxito se o valor em `path` é igual ao `value` fornecido.</span><span class="sxs-lookup"><span data-stu-id="42ce9-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="42ce9-260">JsonPatch em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42ce9-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="42ce9-261">A implementação do ASP.NET Core de JSON Patch é fornecida no pacote do NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="42ce9-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="42ce9-262">O pacote está incluso no metapacote [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="42ce9-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="42ce9-263">Código do método de ação</span><span class="sxs-lookup"><span data-stu-id="42ce9-263">Action method code</span></span>

<span data-ttu-id="42ce9-264">Em um controlador de API, um método de ação para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="42ce9-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="42ce9-265">É anotado com o atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="42ce9-266">Aceita um `JsonPatchDocument<T>` , normalmente com `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="42ce9-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="42ce9-267">Chama `ApplyTo` no documento de patch para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="42ce9-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="42ce9-268">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="42ce9-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="42ce9-269">Esse código do aplicativo de exemplo funciona com o seguinte modelo `Customer`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="42ce9-270">O exemplo de método de ação:</span><span class="sxs-lookup"><span data-stu-id="42ce9-270">The sample action method:</span></span>

* <span data-ttu-id="42ce9-271">Constrói um `Customer`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="42ce9-272">Aplica o patch.</span><span class="sxs-lookup"><span data-stu-id="42ce9-272">Applies the patch.</span></span>
* <span data-ttu-id="42ce9-273">Retorna o resultado no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="42ce9-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="42ce9-274">Em um aplicativo real, o código recuperaria os dados de um repositório, como um banco de dados, e atualizaria o banco de dados após a aplicação do patch.</span><span class="sxs-lookup"><span data-stu-id="42ce9-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="42ce9-275">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="42ce9-275">Model state</span></span>

<span data-ttu-id="42ce9-276">O exemplo de método de ação anterior chama uma sobrecarga de `ApplyTo` que utiliza o estado do modelo como um de seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="42ce9-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="42ce9-277">Com essa opção, você pode receber mensagens de erro nas respostas.</span><span class="sxs-lookup"><span data-stu-id="42ce9-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="42ce9-278">O exemplo a seguir mostra o corpo de uma resposta 400 Solicitação Incorreta para uma operação `test`:</span><span class="sxs-lookup"><span data-stu-id="42ce9-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="42ce9-279">Objetos dinâmicos</span><span class="sxs-lookup"><span data-stu-id="42ce9-279">Dynamic objects</span></span>

<span data-ttu-id="42ce9-280">O exemplo de método de ação a seguir mostra como aplicar um patch a um objeto dinâmico.</span><span class="sxs-lookup"><span data-stu-id="42ce9-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="42ce9-281">A operação add</span><span class="sxs-lookup"><span data-stu-id="42ce9-281">The add operation</span></span>

* <span data-ttu-id="42ce9-282">Se `path` aponta para um elemento de matriz: insere um novo elemento antes do especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="42ce9-283">Se `path` aponta para uma propriedade: define o valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="42ce9-284">Se `path` aponta para um local não existente:</span><span class="sxs-lookup"><span data-stu-id="42ce9-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="42ce9-285">Se o recurso no qual fazer patch é um objeto dinâmico: adiciona uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="42ce9-286">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="42ce9-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="42ce9-287">O exemplo de documento de patch a seguir define o valor de `CustomerName` e adiciona um objeto `Order` ao final da matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="42ce9-288">A operação remove</span><span class="sxs-lookup"><span data-stu-id="42ce9-288">The remove operation</span></span>

* <span data-ttu-id="42ce9-289">Se `path` aponta para um elemento de matriz: remove o elemento.</span><span class="sxs-lookup"><span data-stu-id="42ce9-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="42ce9-290">Se `path` aponta para uma propriedade:</span><span class="sxs-lookup"><span data-stu-id="42ce9-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="42ce9-291">Se o recurso no qual fazer patch é um objeto dinâmico: remove a propriedade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="42ce9-292">Se o recurso no qual fazer patch é um objeto estático:</span><span class="sxs-lookup"><span data-stu-id="42ce9-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="42ce9-293">Se a propriedade é anulável: define como nulo.</span><span class="sxs-lookup"><span data-stu-id="42ce9-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="42ce9-294">Se a propriedade não é anulável: define como `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="42ce9-295">O exemplo de documento de patch a seguir define `CustomerName` como nulo e exclui `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="42ce9-296">A operação replace</span><span class="sxs-lookup"><span data-stu-id="42ce9-296">The replace operation</span></span>

<span data-ttu-id="42ce9-297">Esta operação é funcionalmente a mesma que `remove` seguida por `add`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="42ce9-298">O exemplo de documento de patch a seguir define o valor de `CustomerName` e substitui `Orders[0]` por um novo objeto `Order`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="42ce9-299">A operação move</span><span class="sxs-lookup"><span data-stu-id="42ce9-299">The move operation</span></span>

* <span data-ttu-id="42ce9-300">Se `path` aponta para um elemento de matriz: copia o elemento `from` para o local do elemento `path` e, em seguida, executa uma operação `remove` no elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="42ce9-301">Se `path` aponta para uma propriedade: copia o valor da propriedade `from` para a propriedade `path`, depois executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="42ce9-302">Se `path` aponta para uma propriedade não existente:</span><span class="sxs-lookup"><span data-stu-id="42ce9-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="42ce9-303">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="42ce9-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="42ce9-304">Se o recurso no qual fazer patch é um objeto dinâmico: copia a propriedade `from` para o local indicado por `path` e, em seguida, executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="42ce9-305">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="42ce9-305">The following sample patch document:</span></span>

* <span data-ttu-id="42ce9-306">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="42ce9-307">Define `Orders[0].OrderName` como nulo.</span><span class="sxs-lookup"><span data-stu-id="42ce9-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="42ce9-308">Move `Orders[1]` para antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="42ce9-309">A operação copy</span><span class="sxs-lookup"><span data-stu-id="42ce9-309">The copy operation</span></span>

<span data-ttu-id="42ce9-310">Esta operação é funcionalmente a mesma que uma operação `move`, sem a etapa final `remove`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="42ce9-311">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="42ce9-311">The following sample patch document:</span></span>

* <span data-ttu-id="42ce9-312">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="42ce9-313">Insere uma cópia de `Orders[1]` antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="42ce9-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="42ce9-314">A operação test</span><span class="sxs-lookup"><span data-stu-id="42ce9-314">The test operation</span></span>

<span data-ttu-id="42ce9-315">Se o valor no local indicado por `path` for diferente do valor fornecido em `value`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="42ce9-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="42ce9-316">Nesse caso, toda a solicitação de PATCH falhará, mesmo se todas as outras operações no documento de patch forem bem-sucedidas.</span><span class="sxs-lookup"><span data-stu-id="42ce9-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="42ce9-317">A operação `test` normalmente é usada para impedir uma atualização quando há um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="42ce9-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="42ce9-318">O seguinte exemplo de documento de patch não terá nenhum efeito se o valor inicial de `CustomerName` for "John", porque o teste falha:</span><span class="sxs-lookup"><span data-stu-id="42ce9-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="42ce9-319">Obter o código</span><span class="sxs-lookup"><span data-stu-id="42ce9-319">Get the code</span></span>

<span data-ttu-id="42ce9-320">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="42ce9-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="42ce9-321">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="42ce9-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="42ce9-322">Para testar o exemplo, execute o aplicativo e envie solicitações HTTP com as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="42ce9-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="42ce9-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="42ce9-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="42ce9-324">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="42ce9-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="42ce9-325">Cabeçalho: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="42ce9-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="42ce9-326">Corpo: Copie e cole um dos exemplos de documento de patch JSON da pasta de projeto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="42ce9-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42ce9-327">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="42ce9-327">Additional resources</span></span>

* [<span data-ttu-id="42ce9-328">Especificação do método PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="42ce9-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="42ce9-329">Especificação do JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="42ce9-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="42ce9-330">Especificação de formato de caminho do JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="42ce9-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="42ce9-331">[Documentação do JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="42ce9-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="42ce9-332">Inclui links para recursos a fim de criar documentos de JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="42ce9-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="42ce9-333">Código-fonte de JSON Patch do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42ce9-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
