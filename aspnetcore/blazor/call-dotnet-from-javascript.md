---
title: Chamar métodos .NET de funções JavaScript no ASP.NET Core Blazor
author: guardrex
description: Saiba como invocar métodos .NET de funções JavaScript em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e602f29e6932280f4625ade64201ff232e02150d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751627"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="d811a-103">Chamar métodos .NET de funções JavaScript no ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d811a-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="d811a-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d811a-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d811a-105">Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d811a-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="d811a-106">Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).</span><span class="sxs-lookup"><span data-stu-id="d811a-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="d811a-107">Este artigo aborda a invocação de métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d811a-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="d811a-108">Para obter informações sobre como chamar funções JavaScript do .NET, consulte <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="d811a-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="d811a-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d811a-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="d811a-110">Adicione arquivos JS ( `<script>` marcas) antes da marca de fechamento `</body>` no `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="d811a-110">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="d811a-111">Verifique se os arquivos JS com métodos de interoperabilidade JS estão incluídos antes dos Blazor arquivos do Framework js.</span><span class="sxs-lookup"><span data-stu-id="d811a-111">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="d811a-112">Chamada de método .NET estático</span><span class="sxs-lookup"><span data-stu-id="d811a-112">Static .NET method call</span></span>

<span data-ttu-id="d811a-113">Para invocar um método .NET estático do JavaScript, use `DotNet.invokeMethod` as `DotNet.invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="d811a-113">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="d811a-114">Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos.</span><span class="sxs-lookup"><span data-stu-id="d811a-114">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="d811a-115">A versão assíncrona é preferida para dar suporte a Blazor Server cenários.</span><span class="sxs-lookup"><span data-stu-id="d811a-115">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="d811a-116">O método .NET deve ser público, estático e ter o [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="d811a-116">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="d811a-117">Atualmente, não há suporte para chamar métodos genéricos abertos.</span><span class="sxs-lookup"><span data-stu-id="d811a-117">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="d811a-118">O aplicativo de exemplo inclui um método C# para retornar uma `int` matriz.</span><span class="sxs-lookup"><span data-stu-id="d811a-118">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="d811a-119">O [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="d811a-119">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="d811a-120">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="d811a-120">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="d811a-121">O JavaScript servido para o cliente invoca o método .NET do C#.</span><span class="sxs-lookup"><span data-stu-id="d811a-121">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="d811a-122">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="d811a-122">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="d811a-123">Quando o **`Trigger .NET static method ReturnArrayAsync`** botão estiver selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.</span><span class="sxs-lookup"><span data-stu-id="d811a-123">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="d811a-124">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="d811a-124">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="d811a-125">O quarto valor de matriz é enviado por push para a matriz ( `data.push(4);` ) retornada por `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="d811a-125">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="d811a-126">Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente usando o [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) Construtor de atributos:</span><span class="sxs-lookup"><span data-stu-id="d811a-126">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="d811a-127">No arquivo JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="d811a-127">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="d811a-128">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d811a-128">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="d811a-129">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="d811a-129">Instance method call</span></span>

<span data-ttu-id="d811a-130">Você também pode chamar os métodos de instância do .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d811a-130">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="d811a-131">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="d811a-131">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="d811a-132">Passe a instância do .NET por referência ao JavaScript:</span><span class="sxs-lookup"><span data-stu-id="d811a-132">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="d811a-133">Faça uma chamada estática para <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d811a-133">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="d811a-134">Empacote a instância em uma <xref:Microsoft.JSInterop.DotNetObjectReference> instância e chame <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> na <xref:Microsoft.JSInterop.DotNetObjectReference> instância.</span><span class="sxs-lookup"><span data-stu-id="d811a-134">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="d811a-135">Descarte de <xref:Microsoft.JSInterop.DotNetObjectReference> objetos (um exemplo aparece mais adiante nesta seção).</span><span class="sxs-lookup"><span data-stu-id="d811a-135">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="d811a-136">Invoque os métodos de instância do .NET na instância usando as `invokeMethod` `invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="d811a-136">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="d811a-137">A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d811a-137">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="d811a-138">O aplicativo de exemplo registra as mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="d811a-138">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="d811a-139">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="d811a-139">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="d811a-140">Quando o **`Trigger .NET instance method HelloHelper.SayHello`** botão é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um nome, `Blazor` , para o método.</span><span class="sxs-lookup"><span data-stu-id="d811a-140">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="d811a-141">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="d811a-141">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="d811a-142">`CallHelloHelperSayHello` Invoca a função JavaScript `sayHello` com uma nova instância do `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="d811a-142">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="d811a-143">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="d811a-143">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="d811a-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="d811a-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="d811a-145">O nome é passado para o `HelloHelper` Construtor de, que define a `HelloHelper.Name` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d811a-145">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="d811a-146">Quando a função JavaScript `sayHello` é executada, `HelloHelper.SayHello` retorna a `Hello, {Name}!` mensagem, que é gravada no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d811a-146">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="d811a-147">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="d811a-147">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="d811a-148">Saída do console nas ferramentas de desenvolvedor da Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="d811a-148">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="d811a-149">Para evitar um vazamento de memória e permitir a coleta de lixo em um componente que cria um <xref:Microsoft.JSInterop.DotNetObjectReference> , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d811a-149">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="d811a-150">Descarte o objeto na classe que criou a <xref:Microsoft.JSInterop.DotNetObjectReference> instância:</span><span class="sxs-lookup"><span data-stu-id="d811a-150">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="d811a-151">O padrão anterior mostrado na `ExampleJsInterop` classe também pode ser implementado em um componente:</span><span class="sxs-lookup"><span data-stu-id="d811a-151">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="d811a-152">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d811a-152">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="d811a-153">Quando o componente ou classe não descartar o <xref:Microsoft.JSInterop.DotNetObjectReference> , descarte o objeto no cliente chamando `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="d811a-153">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="d811a-154">Chamada de método de instância de componente</span><span class="sxs-lookup"><span data-stu-id="d811a-154">Component instance method call</span></span>

<span data-ttu-id="d811a-155">Para invocar os métodos .NET de um componente:</span><span class="sxs-lookup"><span data-stu-id="d811a-155">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="d811a-156">Use a `invokeMethod` `invokeMethodAsync` função ou para fazer uma chamada de método estático para o componente.</span><span class="sxs-lookup"><span data-stu-id="d811a-156">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="d811a-157">O método estático do componente encapsula a chamada para seu método de instância como uma chamada <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="d811a-157">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="d811a-158">Para Blazor Server aplicativos, em que vários usuários podem estar usando simultaneamente o mesmo componente, use uma classe auxiliar para invocar métodos de instância.</span><span class="sxs-lookup"><span data-stu-id="d811a-158">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="d811a-159">Para obter mais informações, consulte a seção [classe do método da instância do componente](#component-instance-method-helper-class) .</span><span class="sxs-lookup"><span data-stu-id="d811a-159">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="d811a-160">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="d811a-160">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="d811a-161">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d811a-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="d811a-162">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d811a-162">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="d811a-163">Para passar argumentos para o método de instância:</span><span class="sxs-lookup"><span data-stu-id="d811a-163">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="d811a-164">Adicione parâmetros à invocação do método JS.</span><span class="sxs-lookup"><span data-stu-id="d811a-164">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="d811a-165">No exemplo a seguir, um nome é passado para o método.</span><span class="sxs-lookup"><span data-stu-id="d811a-165">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="d811a-166">Parâmetros adicionais podem ser adicionados à lista, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="d811a-166">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="d811a-167">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d811a-167">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="d811a-168">Forneça os tipos corretos para os <xref:System.Action> parâmetros.</span><span class="sxs-lookup"><span data-stu-id="d811a-168">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="d811a-169">Forneça a lista de parâmetros para os métodos C#.</span><span class="sxs-lookup"><span data-stu-id="d811a-169">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="d811a-170">Invoque o <xref:System.Action> ( `UpdateMessage` ) com os parâmetros ( `action.Invoke(name)` ).</span><span class="sxs-lookup"><span data-stu-id="d811a-170">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="d811a-171">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d811a-171">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="d811a-172">Saída `message` quando o botão do **método de chamada js** for selecionado:</span><span class="sxs-lookup"><span data-stu-id="d811a-172">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="d811a-173">Classe auxiliar de método de instância de componente</span><span class="sxs-lookup"><span data-stu-id="d811a-173">Component instance method helper class</span></span>

<span data-ttu-id="d811a-174">A classe auxiliar é usada para invocar um método de instância como um <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="d811a-174">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="d811a-175">As classes auxiliares são úteis quando:</span><span class="sxs-lookup"><span data-stu-id="d811a-175">Helper classes are useful when:</span></span>

* <span data-ttu-id="d811a-176">Vários componentes do mesmo tipo são renderizados na mesma página.</span><span class="sxs-lookup"><span data-stu-id="d811a-176">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="d811a-177">Um Blazor Server aplicativo é usado, onde vários usuários podem estar usando um componente simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="d811a-177">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="d811a-178">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d811a-178">In the following example:</span></span>

* <span data-ttu-id="d811a-179">O `JSInteropExample` componente contém vários `ListItem` componentes.</span><span class="sxs-lookup"><span data-stu-id="d811a-179">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="d811a-180">Cada `ListItem` componente é composto por uma mensagem e um botão.</span><span class="sxs-lookup"><span data-stu-id="d811a-180">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="d811a-181">Quando um `ListItem` botão de componente é selecionado, `ListItem` esse `UpdateMessage` método altera o texto do item da lista e oculta o botão.</span><span class="sxs-lookup"><span data-stu-id="d811a-181">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="d811a-182">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="d811a-182">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="d811a-183">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d811a-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="d811a-184">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="d811a-184">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="d811a-185">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d811a-185">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="d811a-186">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="d811a-186">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="d811a-187">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="d811a-187">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="d811a-188">Evitar referências circulares de objeto</span><span class="sxs-lookup"><span data-stu-id="d811a-188">Avoid circular object references</span></span>

<span data-ttu-id="d811a-189">Os objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="d811a-189">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="d811a-190">Chamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="d811a-190">.NET method calls.</span></span>
* <span data-ttu-id="d811a-191">O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="d811a-191">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="d811a-192">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="d811a-192">For more information, see the following issues:</span></span>

* [<span data-ttu-id="d811a-193">Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="d811a-193">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="d811a-194">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)</span><span class="sxs-lookup"><span data-stu-id="d811a-194">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="d811a-195">Limites de tamanho em chamadas de interoperabilidade JS</span><span class="sxs-lookup"><span data-stu-id="d811a-195">Size limits on JS interop calls</span></span>

<span data-ttu-id="d811a-196">No Blazor WebAssembly , a estrutura não impõe um limite no tamanho das entradas e saídas de interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="d811a-196">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="d811a-197">No Blazor Server , as chamadas de interoperabilidade js têm tamanho limitado pelo SignalR tamanho máximo de mensagem de entrada permitido para métodos de Hub, que é imposto por <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (padrão: 32 KB).</span><span class="sxs-lookup"><span data-stu-id="d811a-197">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="d811a-198">JS para SignalR mensagens do .net maiores do que <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> lançar um erro.</span><span class="sxs-lookup"><span data-stu-id="d811a-198">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="d811a-199">A estrutura não impõe um limite no tamanho de uma SignalR mensagem do hub para um cliente.</span><span class="sxs-lookup"><span data-stu-id="d811a-199">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="d811a-200">Quando SignalR o log não está definido como [debug](xref:Microsoft.Extensions.Logging.LogLevel) ou [trace](xref:Microsoft.Extensions.Logging.LogLevel), um erro de tamanho de mensagem aparece apenas no console de ferramentas de desenvolvimento do navegador:</span><span class="sxs-lookup"><span data-stu-id="d811a-200">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="d811a-201">Erro: conexão desconectada com erro ' erro: o servidor retornou um erro ao fechar: a conexão foi encerrada com um erro. '.</span><span class="sxs-lookup"><span data-stu-id="d811a-201">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="d811a-202">Quando o [ SignalR registro em log do lado do servidor](xref:signalr/diagnostics#server-side-logging) é definido como [depuração](xref:Microsoft.Extensions.Logging.LogLevel) ou [rastreamento](xref:Microsoft.Extensions.Logging.LogLevel), o registro em log do lado do servidor é um <xref:System.IO.InvalidDataException> erro de tamanho de mensagem.</span><span class="sxs-lookup"><span data-stu-id="d811a-202">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="d811a-203">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="d811a-203">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> <span data-ttu-id="d811a-204">System. IO. InvalidDataException: o tamanho máximo da mensagem de 32768B foi excedido.</span><span class="sxs-lookup"><span data-stu-id="d811a-204">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="d811a-205">O tamanho da mensagem pode ser configurado em AddHubOptions.</span><span class="sxs-lookup"><span data-stu-id="d811a-205">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="d811a-206">Aumente o limite definindo <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d811a-206">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d811a-207">O exemplo a seguir define o tamanho máximo da mensagem de recebimento como 64 KB (64 \* 1024):</span><span class="sxs-lookup"><span data-stu-id="d811a-207">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="d811a-208">Aumentar o SignalR limite de tamanho da mensagem de entrada é o custo de exigir mais recursos do servidor e expõe o servidor para aumentar os riscos de um usuário mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="d811a-208">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="d811a-209">Além disso, a leitura de uma grande quantidade de conteúdo na memória como cadeias de caracteres ou matrizes de bytes também pode resultar em alocações que funcionam mal com o coletor de lixo, resultando em penalidades de desempenho adicionais.</span><span class="sxs-lookup"><span data-stu-id="d811a-209">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="d811a-210">Uma opção para ler grandes cargas é enviar o conteúdo em partes menores e processar a carga como um <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="d811a-210">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="d811a-211">Isso pode ser usado ao ler cargas JSON grandes ou se os dados estiverem disponíveis em JavaScript como bytes brutos.</span><span class="sxs-lookup"><span data-stu-id="d811a-211">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="d811a-212">Para obter um exemplo que demonstra o envio de grandes cargas binárias no Blazor Server que usa técnicas semelhantes ao `InputFile` componente, consulte o [aplicativo de exemplo enviar binário](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="d811a-212">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="d811a-213">Considere as seguintes diretrizes ao desenvolver código que transfere uma grande quantidade de dados entre o JavaScript e o Blazor :</span><span class="sxs-lookup"><span data-stu-id="d811a-213">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="d811a-214">Divida os dados em partes menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="d811a-214">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="d811a-215">Não aloque objetos grandes em JavaScript e código C#.</span><span class="sxs-lookup"><span data-stu-id="d811a-215">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="d811a-216">Não bloqueie o thread da interface do usuário principal por longos períodos ao enviar ou receber dados.</span><span class="sxs-lookup"><span data-stu-id="d811a-216">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="d811a-217">Libere qualquer memória consumida quando o processo for concluído ou cancelado.</span><span class="sxs-lookup"><span data-stu-id="d811a-217">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="d811a-218">Impor os seguintes requisitos adicionais para fins de segurança:</span><span class="sxs-lookup"><span data-stu-id="d811a-218">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="d811a-219">Declare o tamanho máximo de arquivo ou de dados que pode ser passado.</span><span class="sxs-lookup"><span data-stu-id="d811a-219">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="d811a-220">Declare a taxa de carregamento mínima do cliente para o servidor.</span><span class="sxs-lookup"><span data-stu-id="d811a-220">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="d811a-221">Depois que os dados são recebidos pelo servidor, os dados podem ser:</span><span class="sxs-lookup"><span data-stu-id="d811a-221">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="d811a-222">Temporariamente armazenados em um buffer de memória até que todos os segmentos sejam coletados.</span><span class="sxs-lookup"><span data-stu-id="d811a-222">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="d811a-223">Consumido imediatamente.</span><span class="sxs-lookup"><span data-stu-id="d811a-223">Consumed immediately.</span></span> <span data-ttu-id="d811a-224">Por exemplo, os dados podem ser armazenados imediatamente em um banco de dado ou gravados no disco à medida que cada segmento é recebido.</span><span class="sxs-lookup"><span data-stu-id="d811a-224">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="d811a-225">Módulos JS</span><span class="sxs-lookup"><span data-stu-id="d811a-225">JS modules</span></span>

<span data-ttu-id="d811a-226">Para isolamento de JS, o JS Interop funciona com o suporte padrão do navegador para [módulos ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificação ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="d811a-226">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d811a-227">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d811a-227">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="d811a-228">`InteropComponent.razor` exemplo (dotNet/AspNetCore do repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="d811a-228">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
