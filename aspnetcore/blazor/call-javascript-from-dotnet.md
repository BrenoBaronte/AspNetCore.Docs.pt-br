---
title: Chamar funções JavaScript de métodos .NET no ASP.NET CoreBlazor
author: guardrex
description: Saiba como invocar funções JavaScript a partir de métodos Blazor .net em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 2f12c2a05ef510c38f407ccd69f748aceafd55de
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767156"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="d279c-103">Chamar funções JavaScript de métodos .NET no ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="d279c-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="d279c-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d279c-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d279c-105">Um Blazor aplicativo pode invocar funções JavaScript de métodos .net e métodos .net de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d279c-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="d279c-106">Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).</span><span class="sxs-lookup"><span data-stu-id="d279c-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="d279c-107">Este artigo aborda a invocação de funções JavaScript do .NET.</span><span class="sxs-lookup"><span data-stu-id="d279c-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="d279c-108">Para obter informações sobre como chamar métodos .NET do JavaScript, consulte <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="d279c-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="d279c-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d279c-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d279c-110">Para chamar o JavaScript do .NET, use a `IJSRuntime` abstração.</span><span class="sxs-lookup"><span data-stu-id="d279c-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="d279c-111">Para emitir chamadas de interoperabilidade JS `IJSRuntime` , insira a abstração em seu componente.</span><span class="sxs-lookup"><span data-stu-id="d279c-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="d279c-112">O `InvokeAsync<T>` método usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON.</span><span class="sxs-lookup"><span data-stu-id="d279c-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="d279c-113">O identificador de função é relativo ao escopo global (`window`).</span><span class="sxs-lookup"><span data-stu-id="d279c-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="d279c-114">Se você quiser chamar `window.someScope.someFunction`, o identificador será. `someScope.someFunction`</span><span class="sxs-lookup"><span data-stu-id="d279c-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="d279c-115">Não é necessário registrar a função antes que ela seja chamada.</span><span class="sxs-lookup"><span data-stu-id="d279c-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="d279c-116">O tipo `T` de retorno também deve ser serializável em JSON.</span><span class="sxs-lookup"><span data-stu-id="d279c-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="d279c-117">`T`deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.</span><span class="sxs-lookup"><span data-stu-id="d279c-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="d279c-118">Para Blazor aplicativos de servidor com pré-processamento habilitado, a chamada ao JavaScript não é possível durante o pré-processamento inicial.</span><span class="sxs-lookup"><span data-stu-id="d279c-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="d279c-119">As chamadas de interoperabilidade do JavaScript devem ser adiadas até que a conexão com o navegador seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="d279c-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="d279c-120">Para obter mais informações, consulte a seção [detectar Blazor quando um aplicativo de servidor está sendo renderizado](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="d279c-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="d279c-121">O exemplo a seguir é baseado em [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), um decodificador baseado em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d279c-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="d279c-122">O exemplo demonstra como invocar uma função JavaScript de um método C#.</span><span class="sxs-lookup"><span data-stu-id="d279c-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="d279c-123">A função JavaScript aceita uma matriz de bytes de um método C#, decodifica a matriz e retorna o texto para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="d279c-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="d279c-124">Dentro do `<head>` elemento de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (Blazor servidor), forneça uma função JavaScript que usa `TextDecoder` para decodificar uma matriz passada e retornar o valor decodificado:</span><span class="sxs-lookup"><span data-stu-id="d279c-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="d279c-125">O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript (*. js*) com uma referência ao arquivo de script:</span><span class="sxs-lookup"><span data-stu-id="d279c-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="d279c-126">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="d279c-126">The following component:</span></span>

* <span data-ttu-id="d279c-127">Invoca a `convertArray` função JavaScript usando `JSRuntime` quando um botão de componente (**matriz de conversão**) é selecionado.</span><span class="sxs-lookup"><span data-stu-id="d279c-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="d279c-128">Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="d279c-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="d279c-129">A cadeia de caracteres é retornada para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="d279c-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="d279c-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="d279c-130">IJSRuntime</span></span>

<span data-ttu-id="d279c-131">Para usar a `IJSRuntime` abstração, adote qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d279c-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="d279c-132">Injetar `IJSRuntime` a abstração no Razor componente (*. Razor*):</span><span class="sxs-lookup"><span data-stu-id="d279c-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="d279c-133">Dentro do `<head>` elemento de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (Blazor servidor), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d279c-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="d279c-134">A função é chamada com `IJSRuntime.InvokeVoidAsync` e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="d279c-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="d279c-135">Injetar `IJSRuntime` a abstração em uma classe (*. cs*):</span><span class="sxs-lookup"><span data-stu-id="d279c-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="d279c-136">Dentro do `<head>` elemento de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (Blazor servidor), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d279c-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="d279c-137">A função é chamada com `JSRuntime.InvokeAsync` e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="d279c-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="d279c-138">Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), `[Inject]` use o atributo:</span><span class="sxs-lookup"><span data-stu-id="d279c-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="d279c-139">No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interagem com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:</span><span class="sxs-lookup"><span data-stu-id="d279c-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="d279c-140">`showPrompt`&ndash; Produz uma solicitação para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.</span><span class="sxs-lookup"><span data-stu-id="d279c-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="d279c-141">`displayWelcome`&ndash; Atribui uma mensagem de boas-vindas do chamador a um objeto DOM com `id` um `welcome`de.</span><span class="sxs-lookup"><span data-stu-id="d279c-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="d279c-142">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="d279c-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="d279c-143">Coloque a `<script>` marca que faz referência ao arquivo JavaScript no arquivo *wwwroot/index.html* (Blazor Webassembly) ou no arquivo *pages/_Host. cshtml* (Blazor servidor).</span><span class="sxs-lookup"><span data-stu-id="d279c-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="d279c-144">*wwwroot/index.html* (Blazor Webassembly):</span><span class="sxs-lookup"><span data-stu-id="d279c-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="d279c-145">*Páginas/_Host. cshtml* (Blazor servidor):</span><span class="sxs-lookup"><span data-stu-id="d279c-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="d279c-146">Não coloque uma `<script>` marca em um arquivo de componente porque `<script>` a marca não pode ser atualizada dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="d279c-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="d279c-147">Os métodos .NET interoperam com as funções JavaScript no arquivo *exampleJsInterop. js* chamando `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="d279c-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="d279c-148">A `IJSRuntime` abstração é assíncrona para permitir Blazor cenários de servidor.</span><span class="sxs-lookup"><span data-stu-id="d279c-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="d279c-149">Se o aplicativo for um Blazor aplicativo Webassembly e você quiser invocar uma função JavaScript de forma `IJSInProcessRuntime` síncrona, downcast e `Invoke<T>` chame.</span><span class="sxs-lookup"><span data-stu-id="d279c-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="d279c-150">Recomendamos que a maioria das bibliotecas de interoperabilidade do JS use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="d279c-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="d279c-151">O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade do JS.</span><span class="sxs-lookup"><span data-stu-id="d279c-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="d279c-152">O componente:</span><span class="sxs-lookup"><span data-stu-id="d279c-152">The component:</span></span>

* <span data-ttu-id="d279c-153">Recebe a entrada do usuário por meio de um prompt do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d279c-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="d279c-154">Retorna o texto para o componente para processamento.</span><span class="sxs-lookup"><span data-stu-id="d279c-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="d279c-155">Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="d279c-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="d279c-156">*Páginas/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d279c-156">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. <span data-ttu-id="d279c-157">Quando `TriggerJsPrompt` é executado selecionando o botão de **prompt do JavaScript** do componente de gatilho `showPrompt` , a função JavaScript fornecida no arquivo *wwwroot/exampleJsInterop. js* é chamada.</span><span class="sxs-lookup"><span data-stu-id="d279c-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="d279c-158">A `showPrompt` função aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente.</span><span class="sxs-lookup"><span data-stu-id="d279c-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="d279c-159">O componente armazena o nome do usuário em uma variável local, `name`.</span><span class="sxs-lookup"><span data-stu-id="d279c-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="d279c-160">A cadeia de caracteres `name` armazenada em é incorporada a uma mensagem de boas-vindas, `displayWelcome`que é passada para uma função JavaScript,, que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="d279c-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="d279c-161">Chamar uma função JavaScript void</span><span class="sxs-lookup"><span data-stu-id="d279c-161">Call a void JavaScript function</span></span>

<span data-ttu-id="d279c-162">As funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) são chamadas com `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="d279c-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="d279c-163">Detectar quando um Blazor aplicativo de servidor está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="d279c-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="d279c-164">Capturar referências a elementos</span><span class="sxs-lookup"><span data-stu-id="d279c-164">Capture references to elements</span></span>

<span data-ttu-id="d279c-165">Alguns cenários de interoperabilidade JS exigem referências a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="d279c-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="d279c-166">Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização, ou talvez seja necessário chamar APIs do tipo comando em um elemento `focus` , `play`como ou.</span><span class="sxs-lookup"><span data-stu-id="d279c-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="d279c-167">Capture referências a elementos HTML em um componente usando a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="d279c-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="d279c-168">Adicione um `@ref` atributo ao elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="d279c-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="d279c-169">Defina um campo do tipo `ElementReference` cujo nome corresponde ao valor do `@ref` atributo.</span><span class="sxs-lookup"><span data-stu-id="d279c-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="d279c-170">O exemplo a seguir mostra a captura de uma `username` `<input>` referência ao elemento:</span><span class="sxs-lookup"><span data-stu-id="d279c-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="d279c-171">Use apenas uma referência de elemento para converter o conteúdo de um elemento vazio que não interaja com Blazor.</span><span class="sxs-lookup"><span data-stu-id="d279c-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="d279c-172">Esse cenário é útil quando uma API de terceiros fornece conteúdo para o elemento.</span><span class="sxs-lookup"><span data-stu-id="d279c-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="d279c-173">Como Blazor o não interage com o elemento, não há possibilidade de um conflito entre Blazora representação do elemento e o dom.</span><span class="sxs-lookup"><span data-stu-id="d279c-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="d279c-174">No exemplo a seguir, é *perigoso* modificar o conteúdo da lista não ordenada (`ul`) porque Blazor interage com o dom para preencher os itens de lista deste elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="d279c-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="d279c-175">Se a interoperabilidade do JS converter o `MyList` conteúdo Blazor do elemento e tentar aplicar diffs ao elemento, as diferenças não corresponderão ao dom.</span><span class="sxs-lookup"><span data-stu-id="d279c-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="d279c-176">No que diz respeito ao código .NET, um `ElementReference` é um identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="d279c-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="d279c-177">A *única* coisa que você pode fazer `ElementReference` com o é passá-lo para o código JavaScript por meio da interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="d279c-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="d279c-178">Quando você faz isso, o código do lado do JavaScript recebe `HTMLElement` uma instância, que pode ser usada com APIs dom normais.</span><span class="sxs-lookup"><span data-stu-id="d279c-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="d279c-179">Por exemplo, o código a seguir define um método de extensão .NET que permite definir o foco em um elemento:</span><span class="sxs-lookup"><span data-stu-id="d279c-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="d279c-180">*exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="d279c-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="d279c-181">Para chamar uma função JavaScript que não retorna um valor, use `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="d279c-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="d279c-182">O código a seguir define o foco na entrada de nome de usuário chamando a função JavaScript anterior com `ElementReference`o capturado:</span><span class="sxs-lookup"><span data-stu-id="d279c-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="d279c-183">Para usar um método de extensão, crie um método de extensão estático que `IJSRuntime` receba a instância:</span><span class="sxs-lookup"><span data-stu-id="d279c-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="d279c-184">O `Focus` método é chamado diretamente no objeto.</span><span class="sxs-lookup"><span data-stu-id="d279c-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="d279c-185">O exemplo a seguir pressupõe que `Focus` o método está disponível no `JsInteropClasses` namespace:</span><span class="sxs-lookup"><span data-stu-id="d279c-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="d279c-186">A `username` variável é populada apenas depois que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="d279c-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="d279c-187">Se um não populado `ElementReference` for passado para o código JavaScript, o código JavaScript receberá um valor de. `null`</span><span class="sxs-lookup"><span data-stu-id="d279c-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="d279c-188">Para manipular referências de elemento após a conclusão da renderização do componente (para definir o foco inicial em um elemento), use os [métodos de ciclo de vida do componente OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="d279c-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="d279c-189">Ao trabalhar com tipos genéricos e retornar um valor, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="d279c-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="d279c-190">`GenericMethod`é chamado diretamente no objeto com um tipo.</span><span class="sxs-lookup"><span data-stu-id="d279c-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="d279c-191">O exemplo a seguir pressupõe que `GenericMethod` o está disponível no `JsInteropClasses` namespace:</span><span class="sxs-lookup"><span data-stu-id="d279c-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="d279c-192">Elementos de referência entre componentes</span><span class="sxs-lookup"><span data-stu-id="d279c-192">Reference elements across components</span></span>

<span data-ttu-id="d279c-193">Uma `ElementReference` só é garantida válida no método de `OnAfterRender` um componente (e uma referência de elemento `struct`é a), portanto, uma referência de elemento não pode ser passada entre componentes.</span><span class="sxs-lookup"><span data-stu-id="d279c-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="d279c-194">Para que um componente pai torne uma referência de elemento disponível para outros componentes, o componente pai pode:</span><span class="sxs-lookup"><span data-stu-id="d279c-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="d279c-195">Permitir que componentes filho registrem retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="d279c-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="d279c-196">Invoque os retornos de chamada registrados `OnAfterRender` durante o evento com a referência de elemento passada.</span><span class="sxs-lookup"><span data-stu-id="d279c-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="d279c-197">Indiretamente, essa abordagem permite que os componentes filho interajam com a referência de elemento do pai.</span><span class="sxs-lookup"><span data-stu-id="d279c-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="d279c-198">O exemplo Blazor de Webassembly a seguir ilustra a abordagem.</span><span class="sxs-lookup"><span data-stu-id="d279c-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="d279c-199">No `<head>` de *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="d279c-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="d279c-200">No `<body>` de *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="d279c-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="d279c-201">*Páginas/index. Razor* (componente pai):</span><span class="sxs-lookup"><span data-stu-id="d279c-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="d279c-202">*Páginas/index. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="d279c-202">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

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
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="d279c-203">*Shared/SurveyPrompt. Razor* (componente filho):</span><span class="sxs-lookup"><span data-stu-id="d279c-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="d279c-204">*Compartilhado/SurveyPrompt. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="d279c-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a><span data-ttu-id="d279c-205">Proteger chamadas Interop JS</span><span class="sxs-lookup"><span data-stu-id="d279c-205">Harden JS interop calls</span></span>

<span data-ttu-id="d279c-206">A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável.</span><span class="sxs-lookup"><span data-stu-id="d279c-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="d279c-207">Por padrão, um Blazor aplicativo de servidor atinge o tempo limite de chamadas de interoperabilidade js no servidor após um minuto.</span><span class="sxs-lookup"><span data-stu-id="d279c-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="d279c-208">Se um aplicativo puder tolerar um tempo limite mais agressivo, como 10 segundos, defina o tempo limite usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d279c-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="d279c-209">Globalmente no `Startup.ConfigureServices`, especifique o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="d279c-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="d279c-210">Por invocação no código do componente, uma única chamada pode especificar o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="d279c-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="d279c-211">Para obter mais informações sobre esgotamento de <xref:security/blazor/server/threat-mitigation>recursos, consulte.</span><span class="sxs-lookup"><span data-stu-id="d279c-211">For more information on resource exhaustion, see <xref:security/blazor/server/threat-mitigation>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="d279c-212">Evitar referências circulares de objeto</span><span class="sxs-lookup"><span data-stu-id="d279c-212">Avoid circular object references</span></span>

<span data-ttu-id="d279c-213">Os objetos que contêm referências circulares não podem ser serializados no cliente para:</span><span class="sxs-lookup"><span data-stu-id="d279c-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="d279c-214">Chamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="d279c-214">.NET method calls.</span></span>
* <span data-ttu-id="d279c-215">O método JavaScript chama de C# quando o tipo de retorno tem referências circulares.</span><span class="sxs-lookup"><span data-stu-id="d279c-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="d279c-216">Para obter mais informações, consulte os seguintes problemas:</span><span class="sxs-lookup"><span data-stu-id="d279c-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="d279c-217">Não há suporte para referências circulares, use duas (dotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="d279c-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="d279c-218">Proposta: Adicionar mecanismo para lidar com referências circulares ao serializar (dotNet/tempo de execução #30820)</span><span class="sxs-lookup"><span data-stu-id="d279c-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="d279c-219">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d279c-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="d279c-220">Exemplo de InteropComponent. Razor (AspNetCore dotnet/repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="d279c-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="d279c-221">[Executar grandes transferências de dados Blazor em aplicativos de servidor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="d279c-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
