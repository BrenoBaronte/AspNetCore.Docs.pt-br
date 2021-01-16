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
ms.openlocfilehash: 5a00bfb87b8cfe0fb3e2a832a553b8a4cd45ee6d
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252494"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="8ad03-103">Chamar métodos .NET de funções JavaScript no ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="8ad03-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="8ad03-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8ad03-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8ad03-105">Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8ad03-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="8ad03-106">Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).</span><span class="sxs-lookup"><span data-stu-id="8ad03-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="8ad03-107">Este artigo aborda a invocação de métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8ad03-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="8ad03-108">Para obter informações sobre como chamar funções JavaScript do .NET, consulte <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="8ad03-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="8ad03-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8ad03-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="8ad03-110">Chamada de método .NET estático</span><span class="sxs-lookup"><span data-stu-id="8ad03-110">Static .NET method call</span></span>

<span data-ttu-id="8ad03-111">Para invocar um método .NET estático do JavaScript, use `DotNet.invokeMethod` as `DotNet.invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="8ad03-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="8ad03-112">Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos.</span><span class="sxs-lookup"><span data-stu-id="8ad03-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="8ad03-113">A versão assíncrona é preferida para dar suporte a Blazor Server cenários.</span><span class="sxs-lookup"><span data-stu-id="8ad03-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="8ad03-114">O método .NET deve ser público, estático e ter o [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="8ad03-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="8ad03-115">Atualmente, não há suporte para chamar métodos genéricos abertos.</span><span class="sxs-lookup"><span data-stu-id="8ad03-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="8ad03-116">O aplicativo de exemplo inclui um método C# para retornar uma `int` matriz.</span><span class="sxs-lookup"><span data-stu-id="8ad03-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="8ad03-117">O [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atributo é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="8ad03-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="8ad03-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="8ad03-119">O JavaScript servido para o cliente invoca o método .NET do C#.</span><span class="sxs-lookup"><span data-stu-id="8ad03-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="8ad03-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="8ad03-121">Quando o **`Trigger .NET static method ReturnArrayAsync`** botão estiver selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.</span><span class="sxs-lookup"><span data-stu-id="8ad03-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="8ad03-122">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="8ad03-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="8ad03-123">O quarto valor de matriz é enviado por push para a matriz ( `data.push(4);` ) retornada por `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="8ad03-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="8ad03-124">Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente usando o [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) Construtor de atributos:</span><span class="sxs-lookup"><span data-stu-id="8ad03-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="8ad03-125">No arquivo JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="8ad03-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="8ad03-126">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8ad03-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="8ad03-127">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="8ad03-127">Instance method call</span></span>

<span data-ttu-id="8ad03-128">Você também pode chamar os métodos de instância do .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8ad03-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="8ad03-129">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="8ad03-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="8ad03-130">Passe a instância do .NET por referência ao JavaScript:</span><span class="sxs-lookup"><span data-stu-id="8ad03-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="8ad03-131">Faça uma chamada estática para <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8ad03-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="8ad03-132">Empacote a instância em uma <xref:Microsoft.JSInterop.DotNetObjectReference> instância e chame <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> na <xref:Microsoft.JSInterop.DotNetObjectReference> instância.</span><span class="sxs-lookup"><span data-stu-id="8ad03-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="8ad03-133">Descarte de <xref:Microsoft.JSInterop.DotNetObjectReference> objetos (um exemplo aparece mais adiante nesta seção).</span><span class="sxs-lookup"><span data-stu-id="8ad03-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="8ad03-134">Invoque os métodos de instância do .NET na instância usando as `invokeMethod` `invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="8ad03-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="8ad03-135">A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8ad03-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="8ad03-136">O aplicativo de exemplo registra as mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="8ad03-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="8ad03-137">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="8ad03-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="8ad03-138">Quando o **`Trigger .NET instance method HelloHelper.SayHello`** botão é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um nome, `Blazor` , para o método.</span><span class="sxs-lookup"><span data-stu-id="8ad03-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="8ad03-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-139">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="8ad03-140">`CallHelloHelperSayHello` Invoca a função JavaScript `sayHello` com uma nova instância do `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="8ad03-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="8ad03-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="8ad03-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="8ad03-143">O nome é passado para o `HelloHelper` Construtor de, que define a `HelloHelper.Name` propriedade.</span><span class="sxs-lookup"><span data-stu-id="8ad03-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="8ad03-144">Quando a função JavaScript `sayHello` é executada, `HelloHelper.SayHello` retorna a `Hello, {Name}!` mensagem, que é gravada no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8ad03-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="8ad03-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="8ad03-146">Saída do console nas ferramentas de desenvolvedor da Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="8ad03-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="8ad03-147">Para evitar um vazamento de memória e permitir a coleta de lixo em um componente que cria um <xref:Microsoft.JSInterop.DotNetObjectReference> , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8ad03-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="8ad03-148">Descarte o objeto na classe que criou a <xref:Microsoft.JSInterop.DotNetObjectReference> instância:</span><span class="sxs-lookup"><span data-stu-id="8ad03-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="8ad03-149">O padrão anterior mostrado na `ExampleJsInterop` classe também pode ser implementado em um componente:</span><span class="sxs-lookup"><span data-stu-id="8ad03-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
  
  <span data-ttu-id="8ad03-150">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8ad03-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="8ad03-151">Quando o componente ou classe não descartar o <xref:Microsoft.JSInterop.DotNetObjectReference> , descarte o objeto no cliente chamando `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="8ad03-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="8ad03-152">Chamada de método de instância de componente</span><span class="sxs-lookup"><span data-stu-id="8ad03-152">Component instance method call</span></span>

<span data-ttu-id="8ad03-153">Para invocar os métodos .NET de um componente:</span><span class="sxs-lookup"><span data-stu-id="8ad03-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="8ad03-154">Use a `invokeMethod` `invokeMethodAsync` função ou para fazer uma chamada de método estático para o componente.</span><span class="sxs-lookup"><span data-stu-id="8ad03-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="8ad03-155">O método estático do componente encapsula a chamada para seu método de instância como uma chamada <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="8ad03-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="8ad03-156">Para Blazor Server aplicativos, em que vários usuários podem estar usando simultaneamente o mesmo componente, use uma classe auxiliar para invocar métodos de instância.</span><span class="sxs-lookup"><span data-stu-id="8ad03-156">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="8ad03-157">Para obter mais informações, consulte a seção [classe do método da instância do componente](#component-instance-method-helper-class) .</span><span class="sxs-lookup"><span data-stu-id="8ad03-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="8ad03-158">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="8ad03-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="8ad03-159">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8ad03-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="8ad03-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-160">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="8ad03-161">Para passar argumentos para o método de instância:</span><span class="sxs-lookup"><span data-stu-id="8ad03-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="8ad03-162">Adicione parâmetros à invocação do método JS.</span><span class="sxs-lookup"><span data-stu-id="8ad03-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="8ad03-163">No exemplo a seguir, um nome é passado para o método.</span><span class="sxs-lookup"><span data-stu-id="8ad03-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="8ad03-164">Parâmetros adicionais podem ser adicionados à lista, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="8ad03-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="8ad03-165">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8ad03-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="8ad03-166">Forneça os tipos corretos para os <xref:System.Action> parâmetros.</span><span class="sxs-lookup"><span data-stu-id="8ad03-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="8ad03-167">Forneça a lista de parâmetros para os métodos C#.</span><span class="sxs-lookup"><span data-stu-id="8ad03-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="8ad03-168">Invoque o <xref:System.Action> ( `UpdateMessage` ) com os parâmetros ( `action.Invoke(name)` ).</span><span class="sxs-lookup"><span data-stu-id="8ad03-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="8ad03-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-169">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="8ad03-170">Saída `message` quando o botão do **método de chamada js** for selecionado:</span><span class="sxs-lookup"><span data-stu-id="8ad03-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="8ad03-171">Classe auxiliar de método de instância de componente</span><span class="sxs-lookup"><span data-stu-id="8ad03-171">Component instance method helper class</span></span>

<span data-ttu-id="8ad03-172">A classe auxiliar é usada para invocar um método de instância como um <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="8ad03-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="8ad03-173">As classes auxiliares são úteis quando:</span><span class="sxs-lookup"><span data-stu-id="8ad03-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="8ad03-174">Vários componentes do mesmo tipo são renderizados na mesma página.</span><span class="sxs-lookup"><span data-stu-id="8ad03-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="8ad03-175">Um Blazor Server aplicativo é usado, onde vários usuários podem estar usando um componente simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="8ad03-175">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="8ad03-176">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="8ad03-176">In the following example:</span></span>

* <span data-ttu-id="8ad03-177">O `JSInteropExample` componente contém vários `ListItem` componentes.</span><span class="sxs-lookup"><span data-stu-id="8ad03-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="8ad03-178">Cada `ListItem` componente é composto por uma mensagem e um botão.</span><span class="sxs-lookup"><span data-stu-id="8ad03-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="8ad03-179">Quando um `ListItem` botão de componente é selecionado, `ListItem` esse `UpdateMessage` método altera o texto do item da lista e oculta o botão.</span><span class="sxs-lookup"><span data-stu-id="8ad03-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="8ad03-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-180">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="8ad03-181">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8ad03-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="8ad03-182">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="8ad03-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="8ad03-183">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8ad03-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="8ad03-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-184">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="8ad03-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-185">`Pages/JSInteropExample.razor`:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="8ad03-186">Evitar referências circulares de objeto</span><span class="sxs-lookup"><span data-stu-id="8ad03-186">Avoid circular object references</span></span>

<span data-ttu-id="8ad03-187">Os objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="8ad03-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="8ad03-188">Chamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="8ad03-188">.NET method calls.</span></span>
* <span data-ttu-id="8ad03-189">O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="8ad03-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="8ad03-190">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="8ad03-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="8ad03-191">Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="8ad03-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="8ad03-192">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)</span><span class="sxs-lookup"><span data-stu-id="8ad03-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="8ad03-193">Limites de tamanho em chamadas de interoperabilidade JS</span><span class="sxs-lookup"><span data-stu-id="8ad03-193">Size limits on JS interop calls</span></span>

<span data-ttu-id="8ad03-194">No Blazor WebAssembly , a estrutura não impõe um limite no tamanho das entradas e saídas de interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="8ad03-194">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="8ad03-195">No Blazor Server , as chamadas de interoperabilidade js têm tamanho limitado pelo SignalR tamanho máximo de mensagem de entrada permitido para métodos de Hub, que é imposto por <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (padrão: 32 KB).</span><span class="sxs-lookup"><span data-stu-id="8ad03-195">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="8ad03-196">JS para SignalR mensagens do .net maiores do que <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> lançar um erro.</span><span class="sxs-lookup"><span data-stu-id="8ad03-196">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="8ad03-197">A estrutura não impõe um limite no tamanho de uma SignalR mensagem do hub para um cliente.</span><span class="sxs-lookup"><span data-stu-id="8ad03-197">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="8ad03-198">Quando SignalR o log não está definido como [debug](xref:Microsoft.Extensions.Logging.LogLevel) ou [trace](xref:Microsoft.Extensions.Logging.LogLevel), um erro de tamanho de mensagem aparece apenas no console de ferramentas de desenvolvimento do navegador:</span><span class="sxs-lookup"><span data-stu-id="8ad03-198">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="8ad03-199">Erro: conexão desconectada com erro ' erro: o servidor retornou um erro ao fechar: a conexão foi encerrada com um erro. '.</span><span class="sxs-lookup"><span data-stu-id="8ad03-199">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="8ad03-200">Quando o [ SignalR registro em log do lado do servidor](xref:signalr/diagnostics#server-side-logging) é definido como [depuração](xref:Microsoft.Extensions.Logging.LogLevel) ou [rastreamento](xref:Microsoft.Extensions.Logging.LogLevel), o registro em log do lado do servidor é um <xref:System.IO.InvalidDataException> erro de tamanho de mensagem.</span><span class="sxs-lookup"><span data-stu-id="8ad03-200">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="8ad03-201">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="8ad03-201">`appsettings.Development.json`:</span></span>

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

> <span data-ttu-id="8ad03-202">System. IO. InvalidDataException: o tamanho máximo da mensagem de 32768B foi excedido.</span><span class="sxs-lookup"><span data-stu-id="8ad03-202">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="8ad03-203">O tamanho da mensagem pode ser configurado em AddHubOptions.</span><span class="sxs-lookup"><span data-stu-id="8ad03-203">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="8ad03-204">Aumente o limite definindo <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8ad03-204">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8ad03-205">O exemplo a seguir define o tamanho máximo da mensagem de recebimento como 64 KB (64 \* 1024):</span><span class="sxs-lookup"><span data-stu-id="8ad03-205">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="8ad03-206">Aumentar o SignalR limite de tamanho da mensagem de entrada é o custo de exigir mais recursos do servidor e expõe o servidor para aumentar os riscos de um usuário mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="8ad03-206">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="8ad03-207">Além disso, a leitura de uma grande quantidade de conteúdo na memória como cadeias de caracteres ou matrizes de bytes também pode resultar em alocações que funcionam mal com o coletor de lixo, resultando em penalidades de desempenho adicionais.</span><span class="sxs-lookup"><span data-stu-id="8ad03-207">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="8ad03-208">Uma opção para ler grandes cargas é enviar o conteúdo em partes menores e processar a carga como um <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="8ad03-208">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="8ad03-209">Isso pode ser usado ao ler cargas JSON grandes ou se os dados estiverem disponíveis em JavaScript como bytes brutos.</span><span class="sxs-lookup"><span data-stu-id="8ad03-209">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="8ad03-210">Para obter um exemplo que demonstra o envio de grandes cargas binárias no Blazor Server que usa técnicas semelhantes ao `InputFile` componente, consulte o [aplicativo de exemplo enviar binário](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="8ad03-210">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="8ad03-211">Considere as seguintes diretrizes ao desenvolver código que transfere uma grande quantidade de dados entre o JavaScript e o Blazor :</span><span class="sxs-lookup"><span data-stu-id="8ad03-211">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="8ad03-212">Divida os dados em partes menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="8ad03-212">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="8ad03-213">Não aloque objetos grandes em JavaScript e código C#.</span><span class="sxs-lookup"><span data-stu-id="8ad03-213">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="8ad03-214">Não bloqueie o thread da interface do usuário principal por longos períodos ao enviar ou receber dados.</span><span class="sxs-lookup"><span data-stu-id="8ad03-214">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="8ad03-215">Libere qualquer memória consumida quando o processo for concluído ou cancelado.</span><span class="sxs-lookup"><span data-stu-id="8ad03-215">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="8ad03-216">Impor os seguintes requisitos adicionais para fins de segurança:</span><span class="sxs-lookup"><span data-stu-id="8ad03-216">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="8ad03-217">Declare o tamanho máximo de arquivo ou de dados que pode ser passado.</span><span class="sxs-lookup"><span data-stu-id="8ad03-217">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="8ad03-218">Declare a taxa de carregamento mínima do cliente para o servidor.</span><span class="sxs-lookup"><span data-stu-id="8ad03-218">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="8ad03-219">Depois que os dados são recebidos pelo servidor, os dados podem ser:</span><span class="sxs-lookup"><span data-stu-id="8ad03-219">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="8ad03-220">Temporariamente armazenados em um buffer de memória até que todos os segmentos sejam coletados.</span><span class="sxs-lookup"><span data-stu-id="8ad03-220">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="8ad03-221">Consumido imediatamente.</span><span class="sxs-lookup"><span data-stu-id="8ad03-221">Consumed immediately.</span></span> <span data-ttu-id="8ad03-222">Por exemplo, os dados podem ser armazenados imediatamente em um banco de dado ou gravados no disco à medida que cada segmento é recebido.</span><span class="sxs-lookup"><span data-stu-id="8ad03-222">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="8ad03-223">Módulos JS</span><span class="sxs-lookup"><span data-stu-id="8ad03-223">JS modules</span></span>

<span data-ttu-id="8ad03-224">Para isolamento de JS, o JS Interop funciona com o suporte padrão do navegador para [módulos ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificação ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="8ad03-224">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8ad03-225">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8ad03-225">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="8ad03-226">`InteropComponent.razor` exemplo (dotNet/AspNetCore do repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="8ad03-226">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
