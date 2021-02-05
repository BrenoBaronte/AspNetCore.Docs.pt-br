---
title: Chamar funções JavaScript de métodos .NET no ASP.NET Core Blazor
author: guardrex
description: Saiba como invocar funções JavaScript a partir de métodos .NET em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: ca42b611a61fc394655e396f914e8e050c578e6a
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99530080"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="5bdfa-103">Chamar funções JavaScript de métodos .NET no ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5bdfa-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="5bdfa-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5bdfa-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5bdfa-105">Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="5bdfa-106">Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="5bdfa-107">Este artigo aborda a invocação de funções JavaScript do .NET.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="5bdfa-108">Para obter informações sobre como chamar métodos .NET do JavaScript, consulte <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="5bdfa-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5bdfa-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="5bdfa-110">Adicione arquivos JS ( `<script>` marcas) antes da marca de fechamento `</body>` no `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-110">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="5bdfa-111">Verifique se os arquivos JS com métodos de interoperabilidade JS estão incluídos antes dos Blazor arquivos do Framework js.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-111">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

<span data-ttu-id="5bdfa-112">Para chamar o JavaScript do .NET, use a <xref:Microsoft.JSInterop.IJSRuntime> abstração.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-112">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="5bdfa-113">Para emitir chamadas de interoperabilidade JS, insira a <xref:Microsoft.JSInterop.IJSRuntime> abstração em seu componente.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-113">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="5bdfa-114"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-114"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="5bdfa-115">O identificador de função é relativo ao escopo global ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-115">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="5bdfa-116">Se você quiser chamar `window.someScope.someFunction` , o identificador será `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-116">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="5bdfa-117">Não é necessário registrar a função antes que ela seja chamada.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-117">There's no need to register the function before it's called.</span></span> <span data-ttu-id="5bdfa-118">O tipo de retorno `T` também deve ser serializável em JSON.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-118">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="5bdfa-119">`T` deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-119">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="5bdfa-120">As funções JavaScript que retornam uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) são chamadas com <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-120">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="5bdfa-121">`InvokeAsync` desenvolve a promessa e retorna o valor esperado pela promessa.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-121">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="5bdfa-122">Para Blazor Server aplicativos com pré-processamento habilitado, não é possível chamar o JavaScript durante o pré-processamento inicial.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-122">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="5bdfa-123">As chamadas de interoperabilidade do JavaScript devem ser adiadas até que a conexão com o navegador seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-123">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="5bdfa-124">Para obter mais informações, consulte a seção [detectar quando um Blazor Server aplicativo está sendo renderizado](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-124">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="5bdfa-125">O exemplo a seguir se baseia em [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) um decodificador baseado em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-125">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="5bdfa-126">O exemplo demonstra como invocar uma função JavaScript de um método C# que descarrega um requisito do código do desenvolvedor para uma API JavaScript existente.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-126">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="5bdfa-127">A função JavaScript aceita uma matriz de bytes de um método C#, decodifica a matriz e retorna o texto para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-127">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="5bdfa-128">Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma função JavaScript que usa `TextDecoder` para decodificar uma matriz passada e retornar o valor decodificado:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-128">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="5bdfa-129">O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript ( `.js` ) com uma referência ao arquivo de script:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-129">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="5bdfa-130">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-130">The following component:</span></span>

* <span data-ttu-id="5bdfa-131">Invoca a `convertArray` função JavaScript usando `JS` quando um botão de componente ( **`Convert Array`** ) é selecionado.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-131">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="5bdfa-132">Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-132">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="5bdfa-133">A cadeia de caracteres é retornada para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-133">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="5bdfa-134">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="5bdfa-134">IJSRuntime</span></span>

<span data-ttu-id="5bdfa-135">Para usar a <xref:Microsoft.JSInterop.IJSRuntime> abstração, adote qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-135">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="5bdfa-136">Injetar a <xref:Microsoft.JSInterop.IJSRuntime> abstração no Razor componente ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-136">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="5bdfa-137">Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-137">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="5bdfa-138">A função é chamada com <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-138">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="5bdfa-139">Injetar a <xref:Microsoft.JSInterop.IJSRuntime> abstração em uma classe ( `.cs` ):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-139">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="5bdfa-140">Dentro do `<head>` elemento de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-140">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="5bdfa-141">A função é chamada com `JS.InvokeAsync` e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-141">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="5bdfa-142">Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use o `[Inject]` atributo:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-142">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="5bdfa-143">No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interagem com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-143">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="5bdfa-144">`showPrompt`: Produz uma solicitação para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-144">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="5bdfa-145">`displayWelcome`: Atribui uma mensagem de boas-vindas do chamador a um objeto DOM com um `id` de `welcome` .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-145">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="5bdfa-146">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-146">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="5bdfa-147">Coloque a `<script>` marca que faz referência ao arquivo JavaScript no `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-147">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="5bdfa-148">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-148">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="5bdfa-149">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-149">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="5bdfa-150">Não coloque uma `<script>` marca em um arquivo de componente porque a `<script>` marca não pode ser atualizada dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-150">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="5bdfa-151">Os métodos .NET interoperam com as funções JavaScript no `exampleJsInterop.js` arquivo chamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-151">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="5bdfa-152">A <xref:Microsoft.JSInterop.IJSRuntime> abstração é assíncrona para permitir Blazor Server cenários.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-152">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="5bdfa-153">Se o aplicativo for um Blazor WebAssembly aplicativo e você quiser invocar uma função JavaScript de forma síncrona, downcast <xref:Microsoft.JSInterop.IJSInProcessRuntime> e chame <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-153">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="5bdfa-154">Recomendamos que a maioria das bibliotecas de interoperabilidade do JS use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-154">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5bdfa-155">Para habilitar o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão, consulte a seção [ Blazor isolamento de JavaScript e referências de objeto](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-155">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="5bdfa-156">O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade do JS.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-156">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="5bdfa-157">O componente:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-157">The component:</span></span>

* <span data-ttu-id="5bdfa-158">Recebe a entrada do usuário por meio de um prompt do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-158">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="5bdfa-159">Retorna o texto para o componente para processamento.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-159">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="5bdfa-160">Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-160">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="5bdfa-161">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-161">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="5bdfa-162">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-162">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="5bdfa-163">Quando `TriggerJsPrompt` é executado selecionando o botão do componente **`Trigger JavaScript Prompt`** , a `showPrompt` função JavaScript fornecida no `wwwroot/exampleJsInterop.js` arquivo é chamada.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-163">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="5bdfa-164">A `showPrompt` função aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-164">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="5bdfa-165">O componente armazena o nome do usuário em uma variável local, `name` .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-165">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="5bdfa-166">A cadeia de caracteres armazenada em `name` é incorporada a uma mensagem de boas-vindas, que é passada para uma função JavaScript, `displayWelcome` , que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-166">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="5bdfa-167">Chamar uma função JavaScript void</span><span class="sxs-lookup"><span data-stu-id="5bdfa-167">Call a void JavaScript function</span></span>

<span data-ttu-id="5bdfa-168">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-168">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="5bdfa-169">Funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [indefinido](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-169">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="5bdfa-170">Se o .NET não for necessário para ler o resultado de uma chamada JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-170">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="5bdfa-171">Detectar quando um Blazor Server aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="5bdfa-171">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="5bdfa-172">Capturar referências a elementos</span><span class="sxs-lookup"><span data-stu-id="5bdfa-172">Capture references to elements</span></span>

<span data-ttu-id="5bdfa-173">Alguns cenários de interoperabilidade JS exigem referências a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-173">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="5bdfa-174">Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização, ou talvez seja necessário chamar APIs do tipo comando em um elemento, como `focus` ou `play` .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-174">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="5bdfa-175">Capture referências a elementos HTML em um componente usando a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-175">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="5bdfa-176">Adicione um `@ref` atributo ao elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-176">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="5bdfa-177">Defina um campo do tipo <xref:Microsoft.AspNetCore.Components.ElementReference> cujo nome corresponde ao valor do `@ref` atributo.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-177">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="5bdfa-178">O exemplo a seguir mostra a captura de uma referência ao `username` `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-178">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="5bdfa-179">Use apenas uma referência de elemento para converter o conteúdo de um elemento vazio que não interaja com Blazor .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-179">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="5bdfa-180">Esse cenário é útil quando uma API de terceiros fornece conteúdo para o elemento.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-180">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="5bdfa-181">Como o Blazor não interage com o elemento, não há possibilidade de um conflito entre a Blazor representação do elemento e o dom.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-181">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="5bdfa-182">No exemplo a seguir, é *perigoso* modificar o conteúdo da lista não ordenada ( `ul` ) porque Blazor interage com o dom para preencher os itens de lista deste elemento ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-182">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="5bdfa-183">Se a interoperabilidade do JS converter o conteúdo do elemento `MyList` e Blazor tentar aplicar diffs ao elemento, as diferenças não corresponderão ao dom.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-183">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="5bdfa-184">Um <xref:Microsoft.AspNetCore.Components.ElementReference> é passado para o código JavaScript por meio da interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-184">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="5bdfa-185">O código JavaScript recebe uma `HTMLElement` instância, que pode ser usada com APIs dom normais.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-185">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="5bdfa-186">Por exemplo, o código a seguir define um método de extensão .NET que permite enviar um clique do mouse para um elemento:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-186">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="5bdfa-187">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-187">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5bdfa-188">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) em código C# para focalizar um elemento, que é interno da Blazor estrutura e funciona com referências de elemento.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-188">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="5bdfa-189">Para chamar uma função JavaScript que não retorna um valor, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-189">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="5bdfa-190">O código a seguir dispara um evento do lado `Click` do cliente chamando a função JavaScript anterior com o capturado <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="5bdfa-190">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="5bdfa-191">Para usar um método de extensão, crie um método de extensão estático que receba a <xref:Microsoft.JSInterop.IJSRuntime> instância:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-191">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="5bdfa-192">O `clickElement` método é chamado diretamente no objeto.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-192">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="5bdfa-193">O exemplo a seguir pressupõe que o `TriggerClickEvent` método está disponível no `JsInteropClasses` namespace:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-193">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="5bdfa-194">A `exampleButton` variável é populada apenas depois que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-194">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="5bdfa-195">Se um não populado <xref:Microsoft.AspNetCore.Components.ElementReference> for passado para o código JavaScript, o código JavaScript receberá um valor de `null` .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-195">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="5bdfa-196">Para manipular referências de elemento após a conclusão da renderização do componente, use os [ `OnAfterRenderAsync` métodos de ciclo de vida do `OnAfterRender` componente ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-196">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="5bdfa-197">Ao trabalhar com tipos genéricos e retornar um valor, use <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="5bdfa-197">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="5bdfa-198">`GenericMethod` é chamado diretamente no objeto com um tipo.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-198">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="5bdfa-199">O exemplo a seguir pressupõe que o `GenericMethod` está disponível no `JsInteropClasses` namespace:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-199">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="5bdfa-200">Elementos de referência entre componentes</span><span class="sxs-lookup"><span data-stu-id="5bdfa-200">Reference elements across components</span></span>

<span data-ttu-id="5bdfa-201">Um <xref:Microsoft.AspNetCore.Components.ElementReference> não pode ser passado entre componentes porque:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="5bdfa-202">Só é garantido que a instância exista depois que o componente é renderizado, que é durante ou após a execução do método de um componente <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-202">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="5bdfa-203">Um <xref:Microsoft.AspNetCore.Components.ElementReference> é um [`struct`](/csharp/language-reference/builtin-types/struct) , que não pode ser passado como um [parâmetro de componente](xref:blazor/components/index#component-parameters).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-203">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="5bdfa-204">Para que um componente pai torne uma referência de elemento disponível para outros componentes, o componente pai pode:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-204">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="5bdfa-205">Permitir que componentes filho registrem retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-205">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="5bdfa-206">Invoque os retornos de chamada registrados durante o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> evento com a referência de elemento passada.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-206">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="5bdfa-207">Indiretamente, essa abordagem permite que os componentes filho interajam com a referência de elemento do pai.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-207">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="5bdfa-208">O exemplo a seguir Blazor WebAssembly ilustra a abordagem.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-208">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="5bdfa-209">No `<head>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="5bdfa-209">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="5bdfa-210">No `<body>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="5bdfa-210">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="5bdfa-211">`Pages/Index.razor` (componente pai):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-211">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="5bdfa-212">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-212">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="5bdfa-213">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-213">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="5bdfa-214">`Shared/SurveyPrompt.razor` (componente filho):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-214">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="5bdfa-215">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-215">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="5bdfa-216">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly de aplicativo do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-216">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="5bdfa-217">Proteger chamadas Interop JS</span><span class="sxs-lookup"><span data-stu-id="5bdfa-217">Harden JS interop calls</span></span>

<span data-ttu-id="5bdfa-218">A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-218">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="5bdfa-219">Por padrão, um Blazor Server aplicativo expira as chamadas de interoperabilidade js no servidor após um minuto.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-219">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="5bdfa-220">Se um aplicativo puder tolerar um tempo limite mais agressivo, defina o tempo limite usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-220">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="5bdfa-221">Globalmente no `Startup.ConfigureServices` , especifique o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-221">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="5bdfa-222">Por invocação no código do componente, uma única chamada pode especificar o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-222">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="5bdfa-223">Para obter mais informações sobre esgotamento de recursos, consulte <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-223">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="5bdfa-224">Evitar referências circulares de objeto</span><span class="sxs-lookup"><span data-stu-id="5bdfa-224">Avoid circular object references</span></span>

<span data-ttu-id="5bdfa-225">Os objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-225">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="5bdfa-226">Chamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-226">.NET method calls.</span></span>
* <span data-ttu-id="5bdfa-227">O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-227">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="5bdfa-228">Para obter mais informações, consulte [referências circulares não são suportadas, pegue duas (dotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-228">For more information, see [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="blazor-javascript-isolation-and-object-references"></a><span data-ttu-id="5bdfa-229">Blazor Isolamento de JavaScript e referências de objeto</span><span class="sxs-lookup"><span data-stu-id="5bdfa-229">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="5bdfa-230">Blazor habilita o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-230">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="5bdfa-231">O isolamento de JavaScript oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="5bdfa-232">O JavaScript importado não polui mais o namespace global.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="5bdfa-233">Os consumidores de uma biblioteca e componentes não são necessários para importar o JavaScript relacionado.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="5bdfa-234">Por exemplo, o módulo JavaScript a seguir exporta uma função JavaScript para mostrar um prompt do navegador:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="5bdfa-235">Adicione o módulo JavaScript anterior a uma biblioteca do .NET como um ativo da Web estático ( `wwwroot/exampleJsInterop.js` ) e, em seguida, importe o módulo para o código .net chamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> no <xref:Microsoft.JSInterop.IJSRuntime> serviço.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> on the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="5bdfa-236">O serviço é injetado como `js` (não mostrado) para o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-236">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="5bdfa-237">O `import` identificador no exemplo anterior é um identificador especial usado especificamente para importar um módulo JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="5bdfa-238">Especifique o módulo usando seu caminho de ativo da Web estático estável: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-238">Specify the module using its stable static web asset path: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="5bdfa-239">O segmento de caminho para o diretório atual ( `./` ) é necessário para criar o caminho de ativo estático correto para o arquivo JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-239">The path segment for the current directory (`./`) is required in order to create the correct static asset path to the JavaScript file.</span></span> <span data-ttu-id="5bdfa-240">A importação dinâmica de um módulo requer uma solicitação de rede, portanto, ela só pode ser Obtida de forma assíncrona chamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-240">Dynamically importing a module requires a network request, so it can only be achieved asynchronously by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="5bdfa-241">O `{LIBRARY NAME}` espaço reservado é o nome da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-241">The `{LIBRARY NAME}` placeholder is the library name.</span></span> <span data-ttu-id="5bdfa-242">O `{PATH UNDER WWWROOT}` espaço reservado é o caminho para o script em `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-242">The `{PATH UNDER WWWROOT}` placeholder is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="5bdfa-243"><xref:Microsoft.JSInterop.IJSRuntime> importa o módulo como um `IJSObjectReference` , que representa uma referência a um objeto JavaScript do código .net.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-243"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="5bdfa-244">Use o `IJSObjectReference` para invocar funções JavaScript exportadas do módulo:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-244">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="5bdfa-245">`IJSInProcessObjectReference` representa uma referência a um objeto JavaScript cujas funções podem ser invocadas de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-245">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="5bdfa-246">Uso de bibliotecas JavaScript que renderizam a interface do usuário (elementos DOM)</span><span class="sxs-lookup"><span data-stu-id="5bdfa-246">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="5bdfa-247">Às vezes, você pode querer usar bibliotecas JavaScript que produzem elementos visíveis da interface do usuário dentro do DOM do navegador.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-247">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="5bdfa-248">À primeira vista, isso pode parecer difícil, pois o Blazor sistema diferencial depende de ter controle sobre a árvore de elementos DOM e é executado em erros se algum código externo modifica a árvore DOM e invalida seu mecanismo para aplicar diffs.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-248">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="5bdfa-249">Essa não é uma Blazor limitação específica.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-249">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="5bdfa-250">O mesmo desafio ocorre com qualquer estrutura de interface do usuário baseada em comparação.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-250">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="5bdfa-251">Felizmente, é simples inserir a interface do usuário gerada externamente em uma Blazor interface do usuário do componente de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-251">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="5bdfa-252">A técnica recomendada é fazer com que o código do componente ( `.razor` arquivo) produza um elemento vazio.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-252">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="5bdfa-253">No que diz Blazor respeito ao sistema diferencial, o elemento está sempre vazio, portanto, o renderizador não recursivamente no elemento e, em vez disso, deixa seu conteúdo sozinho.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-253">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="5bdfa-254">Isso torna seguro popular o elemento com conteúdo gerenciado de modo externo arbitrário.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-254">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="5bdfa-255">O exemplo a seguir demonstra o conceito.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-255">The following example demonstrates the concept.</span></span> <span data-ttu-id="5bdfa-256">Na `if` instrução quando `firstRender` é `true` , faça algo com `myElement` .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-256">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="5bdfa-257">Por exemplo, chame uma biblioteca JavaScript externa para preenchê-la.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-257">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="5bdfa-258">Blazor Deixa o conteúdo do elemento sozinho até que este componente seja removido.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-258">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="5bdfa-259">Quando o componente é removido, a subárvore DOM inteira do componente também é removida.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-259">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="5bdfa-260">Como um exemplo mais detalhado, considere o seguinte componente que renderiza um mapa interativo usando as [APIs de Mapbox de código aberto](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-260">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="5bdfa-261">O módulo JavaScript correspondente, que deve ser colocado em `wwwroot/mapComponent.js` , é o seguinte:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-261">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="5bdfa-262">No exemplo anterior, substitua a cadeia de caracteres `{ACCESS TOKEN}` por um token de acesso válido do qual você pode obter https://account.mapbox.com .</span><span class="sxs-lookup"><span data-stu-id="5bdfa-262">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="5bdfa-263">Para produzir o estilo correto, adicione a seguinte marca de folha de estilos à página HTML do host ( `index.html` ou `_Host.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-263">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="5bdfa-264">O exemplo anterior produz uma interface de usuário de mapa interativa, na qual o usuário:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-264">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="5bdfa-265">Pode arrastar para rolar ou aplicar zoom.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-265">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="5bdfa-266">Clique nos botões para saltar para locais predefinidos.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-266">Click buttons to jump to predefined locations.</span></span>

![Mapa de Mapbox de Tóquio, Japão com botões para selecionar Bristol, Reino Unido e Tóquio, Japão](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="5bdfa-268">Os pontos principais a serem compreendidos são:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-268">The key points to understand are:</span></span>

 * <span data-ttu-id="5bdfa-269">O `<div>` com o `@ref="mapElement"` é deixado vazio no que diz Blazor respeito.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-269">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="5bdfa-270">Portanto, é seguro `mapbox-gl.js` preenchê-lo e modificar seu conteúdo ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-270">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="5bdfa-271">Você pode usar essa técnica com qualquer biblioteca JavaScript que renderiza a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-271">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="5bdfa-272">Você poderia até mesmo inserir componentes de uma estrutura de SPA do JavaScript de terceiros dentro de Blazor componentes, desde que eles não tentem acessar e modificar outras partes da página.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-272">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="5bdfa-273">*Não* é seguro que o código JavaScript externo modifique os elementos que não se Blazor consideram como vazios.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-273">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="5bdfa-274">Ao usar essa abordagem, tenha em mente as regras sobre como o Blazor retém ou destrói elementos DOM.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-274">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="5bdfa-275">No exemplo anterior, o componente manipula com segurança eventos de clique de botão e atualiza a instância de mapa existente porque os elementos DOM são retidos sempre que possível por padrão.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-275">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="5bdfa-276">Se você estivesse Renderizando uma lista de elementos de mapa de dentro de um `@foreach` loop, você deseja usar `@key` para garantir a preservação de instâncias de componente.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-276">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="5bdfa-277">Caso contrário, as alterações nos dados da lista podem fazer com que as instâncias do componente retenham o estado das instâncias anteriores de maneira indesejável.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-277">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="5bdfa-278">Para obter mais informações, consulte [usando @key para preservar elementos e componentes](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-278">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="5bdfa-279">Além disso, o exemplo anterior mostra como é possível encapsular a lógica JavaScript e dependências dentro de um módulo ES6 e carregá-lo dinamicamente usando o `import` identificador.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-279">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="5bdfa-280">Para obter mais informações, consulte [isolamento de JavaScript e referências de objeto](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-280">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="5bdfa-281">Limites de tamanho em chamadas de interoperabilidade JS</span><span class="sxs-lookup"><span data-stu-id="5bdfa-281">Size limits on JS interop calls</span></span>

<span data-ttu-id="5bdfa-282">No Blazor WebAssembly , a estrutura não impõe um limite no tamanho das entradas e saídas de interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-282">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="5bdfa-283">No Blazor Server , as chamadas de interoperabilidade js têm tamanho limitado pelo SignalR tamanho máximo de mensagem de entrada permitido para métodos de Hub, que é imposto por <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (padrão: 32 KB).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-283">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="5bdfa-284">JS para SignalR mensagens do .net maiores do que <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> lançar um erro.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-284">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="5bdfa-285">A estrutura não impõe um limite no tamanho de uma SignalR mensagem do hub para um cliente.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-285">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span> <span data-ttu-id="5bdfa-286">Para obter mais informações, consulte <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-286">For more information, see <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="5bdfa-287">Módulos JS</span><span class="sxs-lookup"><span data-stu-id="5bdfa-287">JS modules</span></span>

<span data-ttu-id="5bdfa-288">Para isolamento de JS, o JS Interop funciona com o suporte padrão do navegador para [módulos ECMAScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([especificação ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-288">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="5bdfa-289">Interoperabilidade de JS não empacotado</span><span class="sxs-lookup"><span data-stu-id="5bdfa-289">Unmarshalled JS interop</span></span>

<span data-ttu-id="5bdfa-290">Blazor WebAssembly os componentes podem apresentar baixo desempenho quando os objetos do .NET são serializados para a interoperabilidade do JS e qualquer uma das seguintes opções é verdadeira:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-290">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="5bdfa-291">Um alto volume de objetos .NET é serializado rapidamente.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-291">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="5bdfa-292">Exemplo: as chamadas de interoperabilidade do JS são feitas com base na movimentação de um dispositivo de entrada, como girar uma roda do mouse.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-292">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="5bdfa-293">Grandes objetos .NET ou muitos objetos .NET devem ser serializados para a interoperabilidade do JS.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-293">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="5bdfa-294">Exemplo: as chamadas de interoperabilidade do JS exigem a serialização de dezenas de arquivos.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-294">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="5bdfa-295"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> representa uma referência a um objeto JavaScript cujas funções podem ser invocadas sem a sobrecarga de serializar dados .NET.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-295"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="5bdfa-296">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-296">In the following example:</span></span>

* <span data-ttu-id="5bdfa-297">Uma [struct](/dotnet/csharp/language-reference/builtin-types/struct) que contém uma cadeia de caracteres e um inteiro é passada desserializada para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-297">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="5bdfa-298">As funções JavaScript processam os dados e retornam um booliano ou uma cadeia de caracteres para o chamador.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-298">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="5bdfa-299">Uma cadeia de caracteres JavaScript não é conversível diretamente em um `string` objeto .net.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-299">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="5bdfa-300">A `unmarshalledFunctionReturnString` função chama `BINDING.js_string_to_mono_string` para gerenciar a conversão de uma cadeia de caracteres JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-300">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="5bdfa-301">Os exemplos a seguir não são casos de uso típicos para esse cenário porque a [estrutura](/dotnet/csharp/language-reference/builtin-types/struct) passada para o JavaScript não resulta em um baixo desempenho de componente.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-301">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="5bdfa-302">O exemplo usa um pequeno objeto simplesmente para demonstrar os conceitos para passar dados não serializados do .NET.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-302">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="5bdfa-303">Conteúdo de um `<script>` bloco no `wwwroot/index.html` ou em um arquivo JavaScript externo referenciado por `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="5bdfa-303">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> <span data-ttu-id="5bdfa-304">O `js_string_to_mono_string` nome da função, o comportamento e a existência estão sujeitos a alterações em uma versão futura do .net.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-304">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="5bdfa-305">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-305">For example:</span></span>
>
> * <span data-ttu-id="5bdfa-306">A função provavelmente será renomeada.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-306">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="5bdfa-307">A própria função pode ser removida em favor da conversão automática de cadeias de caracteres pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-307">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="5bdfa-308">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop` ):</span><span class="sxs-lookup"><span data-stu-id="5bdfa-308">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

<span data-ttu-id="5bdfa-309">Se uma `IJSUnmarshalledObjectReference` instância não for descartada no código C#, ela poderá ser descartada em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-309">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="5bdfa-310">A função a seguir `dispose` descarta a referência de objeto quando chamada do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="5bdfa-310">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

<span data-ttu-id="5bdfa-311">Os tipos de matriz podem ser convertidos de objetos JavaScript em objetos .NET usando `js_typed_array_to_array` , mas a matriz de JavaScript deve ser uma matriz com tipo.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-311">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="5bdfa-312">As matrizes do JavaScript podem ser lidas em código C# como uma matriz de objetos do .NET ( `object[]` ).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-312">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="5bdfa-313">Outros tipos de dados, como matrizes de cadeia de caracteres, podem ser convertidos, mas exigem a criação de um novo objeto de matriz mono ( `mono_obj_array_new` ) e a definição de seu valor ( `mono_obj_array_set` ).</span><span class="sxs-lookup"><span data-stu-id="5bdfa-313">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="5bdfa-314">As funções de JavaScript fornecidas pela Blazor estrutura, como `js_typed_array_to_array` , `mono_obj_array_new` e `mono_obj_array_set` , estão sujeitas a alterações de nome, alterações comportamentais ou remoção em versões futuras do .net.</span><span class="sxs-lookup"><span data-stu-id="5bdfa-314">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5bdfa-315">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5bdfa-315">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="5bdfa-316">Exemplo de InteropComponent. Razor (AspNetCore dotnet/repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="5bdfa-316">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
